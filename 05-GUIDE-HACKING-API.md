# Guide Hacking API : Vulnérabilités et Protections

**Tableau des attaques courantes et défenses**

---

## Vue d'Ensemble

### Niveaux de Protection

| Niveau | Sécurité | Temps pour Hacker | Compétence | Verdict |
|--------|----------|-------------------|------------|---------|
| **Client seul** | 0/10 | 30 secondes | Aucune | Très dangereux |
| **Serveur partiel** | 5/10 | Jours/semaines | Moyenne | Risqué |
| **Serveur + RLS** | 10/10 | Quasi-impossible | Experte | Sécurisé |

---

## Attaques Côté Client

### Tableau Attaques Frontend

| Attaque | Difficulté | Temps | Outil | Impact | Protection |
|---------|-----------|-------|-------|--------|------------|
| **Modifier user.role dans console** | Très facile | 10s | DevTools | Admin gratuit | Serveur vérifie |
| **Inspect element (cacher div)** | Très facile | 15s | DevTools | Voir contenu caché | Serveur ne renvoie pas data |
| **Copier URLs hardcodées** | Très facile | 30s | View Source | Download premium | URLs signées serveur |
| **Modifier state React** | Facile | 1 min | React DevTools | Bypass UI | Serveur vérifie |
| **Modifier localStorage** | Facile | 1 min | DevTools | Faux premium | Serveur vérifie token |
| **Désactiver JavaScript** | Facile | 5s | Browser | Voir HTML brut | Server-side rendering |

---

## Attaques Côté Serveur (API)

### Tableau Attaques API Courantes

| Attaque | Cible | Exploit | Difficulté | Protection |
|---------|-------|---------|------------|------------|
| **Modifier paramètre prix** | E-commerce | `{"price": 0.01}` | Facile | Prix depuis DB jamais client |
| **Modifier userId** | Multi-user | `{"userId": "autre"}` | Facile | userId depuis token vérifié |
| **Modifier role** | RBAC | `{"role": "admin"}` | Facile | Role depuis DB jamais client |
| **IDOR (ID manipulation)** | Resources | `/api/invoice/999` (pas le sien) | Facile | Vérif ownership + RLS |
| **Missing auth check** | Endpoint oublié | Endpoint sans auth | Facile | Middleware global |
| **SQL Injection** | Database | `id=1 OR 1=1` | Moyen | Prepared statements + RLS |
| **JWT manipulation** | Token | Modifier claims JWT | Moyen | Signature vérifiée serveur |
| **Rate limit bypass** | API abuse | Changer IP/headers | Moyen | Rate limit IP + token |
| **Replay attack** | Sessions | Rejouer requête capturée | Moyen | Nonce/timestamp |
| **CSRF** | State change | Requête depuis autre site | Moyen | CSRF tokens |

---

## Exemples Exploits Concrets

### Exploit 1 : Prix Modifiable

**Code vulnérable** :

```javascript
// API accepte prix du client
app.post('/checkout', (req, res) => {
  const { productId, price } = req.body
  stripe.charges.create({ amount: price * 100 })
})
```

**Exploit** :

```bash
curl -X POST https://api.shop.com/checkout \
  -d '{"productId":123, "price":0.01}'
```

**Impact** : Produit 100 dollars acheté 1 centime

**Fix** :

```javascript
app.post('/checkout', async (req, res) => {
  const product = await db.products.findById(req.body.productId)
  stripe.charges.create({ amount: product.price * 100 })
})
```

---

### Exploit 2 : IDOR (Insecure Direct Object Reference)

**Code vulnérable** :

```javascript
// Pas de vérif ownership
app.get('/api/invoices/:id', (req, res) => {
  const invoice = db.getInvoice(req.params.id)
  return res.json(invoice)
})
```

**Exploit** :

```bash
# User A (invoice ID 100)
curl https://api.com/api/invoices/100  # OK ses données

# User A essaie accéder facture User B
curl https://api.com/api/invoices/101  # Accès données User B
curl https://api.com/api/invoices/102  # Accès données User C
# ... énumération complète
```

**Impact** : Toutes factures exposées

**Fix (Serveur)** :

```javascript
app.get('/api/invoices/:id', async (req, res) => {
  const user = verifyToken(req.headers.authorization)
  const invoice = await db.getInvoice(req.params.id)
  
  if (invoice.userId !== user.id) {
    return res.status(403).send('Forbidden')
  }
  
  return res.json(invoice)
})
```

**Fix (RLS)** :

```sql
CREATE POLICY "users_own_invoices"
ON invoices FOR SELECT
USING (auth.uid() = user_id);
```

Avec RLS : Même si code oublié, DB refuse automatiquement

---

### Exploit 3 : Role Escalation

**Code vulnérable** :

```javascript
// Backend fait confiance au role envoyé
app.delete('/admin/users/:id', (req, res) => {
  if (req.body.role !== 'admin') {
    return res.status(403).send('Admin only')
  }
  db.deleteUser(req.params.id)
})
```

**Exploit** :

```bash
curl -X DELETE https://api.com/admin/users/123 \
  -d '{"role":"admin"}' \
  # Attaquant envoie role="admin"
```

**Impact** : N'importe qui devient admin

**Fix** :

```javascript
app.delete('/admin/users/:id', async (req, res) => {
  const user = await verifyToken(req.headers.authorization)
  const dbUser = await db.getUser(user.id)
  
  // Role depuis DATABASE
  if (dbUser.role !== 'admin') {
    return res.status(403).send('Admin only')
  }
  
  db.deleteUser(req.params.id)
})
```

---

### Exploit 4 : Missing Function Level Auth

**Code vulnérable** :

```javascript
// Endpoint admin sans vérif
app.get('/api/admin/stats', (req, res) => {
  // Oubli vérifier si admin
  const stats = db.getAllStats()
  return res.json(stats)
})
```

**Exploit** :

```bash
# N'importe qui accède
curl https://api.com/api/admin/stats
# Reçoit toutes les statistiques
```

**Impact** : Données sensibles exposées

**Fix** :

```javascript
app.get('/api/admin/stats', requireAdmin, (req, res) => {
  const stats = db.getAllStats()
  return res.json(stats)
})
```

---

### Exploit 5 : SQL Injection

**Code vulnérable** :

```javascript
app.get('/api/search', (req, res) => {
  const query = req.query.q
  const results = db.query(`SELECT * FROM courses WHERE title LIKE '%${query}%'`)
  return res.json(results)
})
```

**Exploit** :

```bash
curl "https://api.com/api/search?q=%' OR 1=1 --"
# Retourne TOUS les cours (même premium)
```

**Impact** : Database compromise complète

**Fix (Prepared Statements)** :

```javascript
app.get('/api/search', async (req, res) => {
  const results = await db.query(
    'SELECT * FROM courses WHERE title LIKE $1',
    [`%${req.query.q}%`]
  )
  return res.json(results)
})
```

**Fix (RLS)** : Même avec injection, RLS applique policies

---

## Tableau Exploits par Type App

### Applications avec Abonnements

| Exploit | Code Vulnérable | Impact | Fix |
|---------|----------------|--------|-----|
| **Modifier tier client** | `if (user.tier === 'premium')` | Accès gratuit | Vérif serveur + RLS |
| **URLs vidéos exposées** | `videoUrl` hardcodé | Download premium | URLs signées (expire) |
| **Endpoint oublié** | Route sans vérif tier | Bypass paywall | RLS automatique |
| **Compteur downloads client** | Check client-side | Downloads illimités | Compteur serveur + RLS |

---

### Applications Multi-Tenant

| Exploit | Code Vulnérable | Impact | Fix |
|---------|----------------|--------|-----|
| **Modifier org_id** | `{orgId: "autre"}` | Cross-org leak | orgId depuis session + RLS |
| **Énumération IDs** | `/api/orgs/1`, `/api/orgs/2` | Voir toutes orgs | RLS isolation |
| **Missing org filter** | Query sans WHERE org_id | Toutes données | RLS obligatoire |
| **Invite bypass** | Créer membre sans vérif | Accès non autorisé | RLS members table |

---

### Applications Financières

| Exploit | Code Vulnérable | Impact | Fix |
|---------|----------------|--------|-----|
| **Modifier montant** | Prix du client | Transactions gratuites | Prix DB + validation serveur |
| **Modifier destinataire** | `{to: "hacker"}` | Vol d'argent | Vérif stricte + audit |
| **Replay transaction** | Rejouer requête | Double charge | Nonce + idempotence |
| **Missing balance check** | Pas vérif solde | Découvert non autorisé | Transaction atomique |

---

## Protections par Niveau

### Tableau Défenses

| Type Protection | Bloque Console | Bloque Inspect | Bloque IDOR | Bloque Injection | Bloque Bug Code | Score |
|-----------------|----------------|----------------|-------------|------------------|-----------------|-------|
| **Client seul** | Non | Non | Non | N/A | N/A | 0/10 |
| **HTTPS only** | Non | Non | Non | Non | Non | 1/10 |
| **JWT validation** | Oui | Oui | Non | Non | Non | 3/10 |
| **Authz serveur** | Oui | Oui | Si codé | Si prepared | Non | 6/10 |
| **Authz + RLS** | Oui | Oui | Oui | Oui | Oui | 10/10 |

---

## Solutions par Niveau Sécurité

### Niveau 0 : Pas de Protection (Catastrophique)

**Exemple** :

```javascript
// API ouverte
app.get('/api/courses', (req, res) => {
  const courses = db.getAllCourses()
  return res.json(courses)
})
```

**Exploitable par** : Tout le monde

**Temps hack** : 0 secondes (pas de hack nécessaire)

---

### Niveau 1 : Client Only (Très Dangereux)

**Exemple** :

```javascript
// React
{user.isPremium && <PremiumContent />}
```

**Exploitable par** : Débutant (F12)

**Temps hack** : 30 secondes

**Utilisé par** : Débutants, prototypes (jamais production)

---

### Niveau 2 : JWT Basique (Dangereux)

**Exemple** :

```javascript
app.get('/api/courses', (req, res) => {
  const user = verifyJWT(req.headers.authorization)
  if (!user) return res.status(401).send()
  
  // Mais pas de vérif authz
  return res.json(db.getAllCourses())
})
```

**Exploitable** : Authenticated users voient tout

**Temps hack** : Créer compte gratuit

**Utilisé par** : Développeurs juniors

---

### Niveau 3 : Authz Manuelle (Risqué)

**Exemple** :

```javascript
app.get('/api/courses/:id', async (req, res) => {
  const user = verifyJWT(req.headers.authorization)
  const course = await db.getCourse(req.params.id)
  
  if (course.tier === 'premium' && user.subscription !== 'premium') {
    return res.status(403).send()
  }
  
  return res.json(course)
})
```

**Exploitable** : Si 1 endpoint oublié (sur 50)

**Temps hack** : Énumération endpoints (heures/jours)

**Utilisé par** : Développeurs intermédiaires

---

### Niveau 4 : Authz + RLS (Sécurisé)

**Exemple** :

```javascript
app.get('/api/courses/:id', async (req, res) => {
  const supabase = createClient(req.headers.authorization)
  
  // RLS vérifie automatiquement
  const { data } = await supabase
    .from('courses')
    .select('*')
    .eq('id', req.params.id)
    .single()
  
  if (!data) return res.status(403).send()
  
  return res.json(data)
})
```

**Exploitable** : Non (RLS protège toujours)

**Temps hack** : Impossible

**Utilisé par** : Développeurs expérimentés, production

---

## Techniques de Hacking API

### Tableau Techniques Courantes

| Technique | Principe | Exemple | Difficulté | Prévention |
|-----------|----------|---------|------------|------------|
| **IDOR** | Changer ID dans URL | `/api/user/999` | Très facile | Ownership check + RLS |
| **Parameter Tampering** | Modifier paramètres | `price=0.01` | Très facile | Valider serveur |
| **Mass Assignment** | Envoyer champs non prévus | `{"role":"admin"}` | Facile | Whitelist fields |
| **Path Traversal** | Accéder fichiers | `../../etc/passwd` | Facile | Validation paths |
| **Broken Auth** | Token faible/volé | JWT secret faible | Moyen | Strong secrets + rotation |
| **Broken Function Auth** | Endpoint sans auth | Route oubliée | Facile | Middleware global |
| **Excessive Data** | API retourne trop | Toutes données user | Facile | Select specific fields |
| **Rate Limit Bypass** | Trop de requêtes | Scripts automatiques | Moyen | Rate limit strict |
| **Injection** | SQL/NoSQL/Command | `' OR 1=1--` | Moyen | Prepared statements |
| **XXE** | XML externe | Parser XML | Avancé | Disable external entities |

---

## IDOR : Étude Détaillée

### Qu'est-ce que IDOR ?

**IDOR** = Insecure Direct Object Reference

**Principe** : Accéder ressource en changeant ID dans URL

### Exemple Vulnérable

**API** :

```javascript
GET /api/courses/123    // Mon cours
GET /api/courses/124    // Cours autre user (accessible !)
GET /api/courses/125    // Cours autre user (accessible !)
```

### Exploitation

**Script automatique** :

```python
# Énumération tous cours
for i in range(1, 10000):
    response = requests.get(f'https://api.com/api/courses/{i}')
    if response.status_code == 200:
        print(f'Course {i}: {response.json()}')
        # Sauvegarder tous les cours premium gratuitement
```

**Résultat** : 10,000 cours téléchargés en 1 heure

---

### Protection IDOR

**Niveau 1 : Vérification Manuelle**

```javascript
app.get('/api/courses/:id', async (req, res) => {
  const user = verifyToken(req.headers.authorization)
  const course = await db.getCourse(req.params.id)
  
  // Vérif ownership
  if (course.userId !== user.id) {
    return res.status(403).send()
  }
  
  return res.json(course)
})
```

**Problème** : À répéter dans CHAQUE endpoint

---

**Niveau 2 : RLS (Automatique)**

```sql
CREATE POLICY "users_own_courses"
ON courses FOR SELECT
USING (auth.uid() = user_id);
```

**Avantage** : Impossible d'oublier, DB refuse automatiquement

---

## Mass Assignment Attack

### Exemple Vulnérable

**Code** :

```javascript
// User peut modifier profil
app.put('/api/profile', async (req, res) => {
  const user = verifyToken(req.headers.authorization)
  
  // Mise à jour avec TOUS les champs reçus
  await db.users.update(user.id, req.body)
  
  return res.json({ success: true })
})
```

**Exploit** :

```bash
# User normal envoie
curl -X PUT https://api.com/api/profile \
  -d '{
    "name": "John",
    "email": "john@test.com",
    "role": "admin",              # Champ non prévu
    "subscription": "premium",     # Champ non prévu
    "balance": 999999              # Champ non prévu
  }'
```

**Impact** : User devient admin + premium + riche

---

### Protection Mass Assignment

**Whitelist fields** :

```javascript
app.put('/api/profile', async (req, res) => {
  const user = verifyToken(req.headers.authorization)
  
  // Accepter UNIQUEMENT champs autorisés
  const allowedFields = {
    name: req.body.name,
    email: req.body.email,
    avatar: req.body.avatar
  }
  
  await db.users.update(user.id, allowedFields)
  
  return res.json({ success: true })
})
```

---

## Broken Authentication

### Tableau Failles Auth Courantes

| Faille | Exemple | Impact | Fix |
|--------|---------|--------|-----|
| **Token jamais expire** | JWT sans exp | Token volé utilisable toujours | exp: 1h |
| **Secret faible** | JWT secret = "secret" | Token forgeable | Secret fort 256 bits |
| **Token dans URL** | `/reset?token=abc` | Token loggé | Token dans body POST |
| **Session fixation** | Session ID prévisible | Hijacking session | UUID random |
| **No rate limit login** | Tentatives illimitées | Brute force password | Rate limit 5/min |
| **Credentials in logs** | Logger passwords | Exposure logs | Jamais logger credentials |

---

## Rate Limiting

### Tableau Techniques Bypass

| Technique Bypass | Principe | Difficulté | Prévention |
|-----------------|----------|------------|------------|
| **Changer IP** | VPN/Proxy | Facile | Rate limit par token aussi |
| **Changer User-Agent** | Headers différents | Très facile | Ignorer User-Agent |
| **Distributed** | Botnet IPs multiples | Moyen | WAF + Cloudflare |
| **Token rotation** | Créer comptes multiples | Facile | Email verification + phone |

---

### Rate Limiting Efficace

**Mauvais** :

```javascript
// Rate limit IP uniquement
rateLimit({ windowMs: 60000, max: 100 })
```

Bypass : Changer IP

**Bon** :

```javascript
// Rate limit IP + Token + Endpoint
rateLimit({
  keyGenerator: (req) => {
    return `${req.ip}-${req.user?.id}-${req.path}`
  },
  windowMs: 60000,
  max: 100
})
```

**Meilleur** : Kong Gateway ou Cloudflare (distributed)

---

## Solutions par Type de Faille

### Matrice Protection

| Faille | Client | Serveur Code | Serveur RLS | WAF | API Gateway |
|--------|--------|--------------|-------------|-----|-------------|
| **IDOR** | Aucune | Si codé | Automatique | Non | Non |
| **Mass Assignment** | Aucune | Whitelist | RLS + constraints | Non | Non |
| **Price Manipulation** | Aucune | Validation | RLS + constraints | Non | Non |
| **Missing Auth** | Aucune | Middleware | RLS refuse | Oui | Oui |
| **SQL Injection** | Aucune | Prepared stmt | RLS applique | Oui | Non |
| **Rate Limit Bypass** | Aucune | Basique | Non | Oui | Oui |
| **DDoS** | Aucune | Non | Non | Oui | Oui |
| **XSS** | Sanitization | Non | Non | Oui | Non |

**Defense-in-Depth** : Combiner plusieurs

---

## Outils Hacking (Pour Tester)

### Tableau Outils

| Outil | Type | Usage | Gratuit | Niveau |
|-------|------|-------|---------|--------|
| **Browser DevTools** | Manual | Modifier client, voir network | Oui | Débutant |
| **Postman** | API Test | Tester endpoints, modifier requêtes | Oui | Débutant |
| **curl** | CLI | Scripts API | Oui | Intermédiaire |
| **Burp Suite** | Proxy | Intercepter/modifier requêtes | Free version | Intermédiaire |
| **OWASP ZAP** | Scanner | Scan vulnérabilités auto | Oui | Intermédiaire |
| **sqlmap** | Injection | Test SQL injection auto | Oui | Avancé |
| **Nikto** | Scanner | Scan server vulnerabilities | Oui | Avancé |
| **Metasploit** | Exploitation | Exploitation framework | Oui | Expert |

**Recommandation** : Tester avec Postman + Burp minimum

---

## Checklist Sécurité API

### Avant Production

**Authentification** :
- [ ] Token validé côté serveur (jamais client)
- [ ] Secret fort (256 bits minimum)
- [ ] Token expire (1h recommended)
- [ ] Refresh token rotation
- [ ] Rate limit login (5 tentatives/min)

**Autorisation** :
- [ ] Vérification CHAQUE endpoint
- [ ] Ownership check (mes données uniquement)
- [ ] Role depuis database (jamais client)
- [ ] RLS activé (données sensibles)
- [ ] Pas de confiance paramètres client

**Validation** :
- [ ] Whitelist fields (pas tout accepter)
- [ ] Validation types (string, number, etc.)
- [ ] Sanitization inputs
- [ ] Prepared statements (SQL)
- [ ] Path validation (files)

**Infrastructure** :
- [ ] HTTPS obligatoire
- [ ] CORS configuré
- [ ] Rate limiting actif
- [ ] WAF si critique (Cloudflare)
- [ ] Monitoring/alerting

**Database** :
- [ ] RLS activé (si Postgres)
- [ ] Firestore Rules (si Firebase)
- [ ] Encryption at rest
- [ ] Backup réguliers
- [ ] Audit logs si critique

---

## Comparaison Solutions

### Protection IDOR

| Solution | Protection | Maintenance | Risque Oubli |
|----------|-----------|-------------|--------------|
| **Vérif manuelle** | Si codé partout | Élevée | Élevé |
| **Middleware** | Bonne | Moyenne | Moyen |
| **RLS** | Automatique | Faible | Aucun |

**Recommandation** : RLS (Supabase, Hasura)

---

### Protection Mass Assignment

| Solution | Protection | Complexité |
|----------|-----------|------------|
| **Accepter tout** | Aucune | Facile (dangereux) |
| **Whitelist manuelle** | Bonne | Moyenne |
| **DTO/Schema validation** | Excellente | Moyenne |
| **RLS constraints** | Garantie | Moyenne |

**Recommandation** : DTO validation + RLS

---

### Protection Injection

| Solution | Protection | Performance |
|----------|-----------|-------------|
| **String concatenation** | Aucune | Bonne |
| **Escaping** | Partielle | Bonne |
| **Prepared statements** | Excellente | Bonne |
| **ORM** | Excellente | Moyenne |
| **RLS** | Defense-in-depth | Excellente |

**Recommandation** : Prepared statements obligatoire

---

## Cas Réels de Hacks

### Tableau Incidents Documentés

| Année | Type App | Faille | Exploit | Impact |
|-------|----------|--------|---------|--------|
| **2021** | Cours en ligne | URLs hardcodées | View source | 500 cours volés |
| **2022** | SaaS B2B | Endpoint oublié | IDOR | Cross-org leak, faillite |
| **2023** | E-commerce | Prix client | Parameter tampering | 1M dollars pertes |
| **2020** | API publique | Pas de rate limit | Scraping | Database leak complète |
| **2019** | Banking app | SQL injection | Input validation | 10M records exposés |
| **2023** | Social network | Missing authz | IDOR | Photos privées publiques |

**Constat** : Toutes évitables avec RLS + validation

---

## Recommandations par Type App

### Ton Cas : Vente Cours + Images + Abonnements

**Vulnérabilités critiques** :

| Vulnérabilité | Risque | Protection Obligatoire |
|---------------|--------|------------------------|
| **URLs vidéos exposées** | Très élevé | URLs signées + RLS |
| **Accès tier supérieur** | Élevé | RLS tier-based |
| **Downloads illimités** | Élevé | Compteur RLS + trigger |
| **IDOR cours/images** | Moyen | RLS ownership |

**Stack recommandée** :

```
Next.js
    ↓
Supabase (JWT validation)
    ↓
PostgreSQL + RLS (protection automatique)
    ↓
Storage URLs signées (expire 1h)
```

**Protections actives** :
- RLS tier-based : Free ne voit pas Premium
- URLs signées : Expire après 1h
- Compteur downloads : Limite par tier
- Ownership : User voit ses achats uniquement

---

### SaaS Multi-Tenant

**Vulnérabilités critiques** :

| Vulnérabilité | Risque | Protection |
|---------------|--------|------------|
| **Cross-org data leak** | Critique | RLS isolation OBLIGATOIRE |
| **IDOR inter-org** | Critique | RLS + org_id filter |
| **Énumération orgs** | Élevé | RLS automatic |

**Protection minimale** : RLS avec isolation org_id

---

### API Publique

**Vulnérabilités critiques** :

| Vulnérabilité | Risque | Protection |
|---------------|--------|------------|
| **Scraping** | Élevé | Rate limiting + API keys |
| **Abuse gratuit** | Élevé | Plans payants + quotas |
| **DDoS** | Élevé | WAF + Cloudflare |

**Stack** : Kong Gateway + Rate limiting

---

## Tableau Final : Solutions vs Attaques

### Résistance par Solution

| Solution | IDOR | Mass Assign | Price Hack | Injection | Bug Code | Score |
|----------|------|-------------|------------|-----------|----------|-------|
| **Client seul** | Vulnérable | Vulnérable | Vulnérable | N/A | N/A | 0/10 |
| **NextAuth** | Si codé | Si codé | Si codé | Si prepared | Vulnérable | 5/10 |
| **Clerk** | Si codé | Si codé | Si codé | Si prepared | Vulnérable | 5/10 |
| **Auth0** | Si codé | Si codé | Si codé | Si prepared | Vulnérable | 6/10 |
| **Supabase RLS** | Protégé | Protégé | Protégé | Protégé | Protégé | 10/10 |
| **Firebase Rules** | Protégé | Protégé | Protégé | Protégé | Protégé | 9/10 |

**Verdict** : RLS/Rules seule vraie protection

---

## Synthèse Recommandations

### Par Niveau Sécurité Requis

| Niveau Requis | Solution Minimale | Solution Recommandée | À Éviter |
|---------------|------------------|---------------------|----------|
| **Critique (Finance, Santé)** | Serveur + RLS + Audit | Auth0 + Backend + RLS | Client, Code seul |
| **Élevé (SaaS, E-commerce)** | Serveur + RLS | Supabase RLS | Client, Authz manuelle |
| **Moyen (Blog membres)** | Serveur authz | Supabase ou Firebase | Client seul |
| **Faible (Prototype)** | Serveur basique | Supabase | Production avec client |

---

### Par Type de Faille

| Faille | Solution Optimale |
|--------|------------------|
| **IDOR** | RLS (Supabase/Hasura) |
| **Mass Assignment** | DTO validation + RLS |
| **Price Manipulation** | Validation serveur + RLS constraints |
| **SQL Injection** | Prepared statements + RLS |
| **Missing Auth** | Middleware global + RLS |
| **Rate Abuse** | Kong/Cloudflare + Redis |

---

## Conclusion

### Principes Absolus

**1. Ne jamais faire confiance au client**
- Toute donnée client est manipulable
- Validation serveur obligatoire

**2. Protection database = dernier rempart**
- RLS protège même si code buggé
- Seule garantie pour données sensibles

**3. Defense-in-Depth**
- Client : UX uniquement
- Serveur : Validation + authz
- Database : RLS protection finale

**4. Un seul oubli suffit**
- 1 endpoint sur 50 sans protection = faille
- RLS élimine ce risque

---

### Pour Ton Projet

**Cours + Images + Abonnements** :

**Dangers si mal protégé** :
- Vidéos premium téléchargées gratuitement
- Images vendues partagées gratuitement
- Perte revenus totale
- Business non viable

**Protection obligatoire** :
- Supabase RLS tier-based
- Storage URLs signées
- Compteur downloads RLS
- Zero confiance client

**Investissement** : 2-3 jours setup

**ROI** : Protection revenus + sécurité garantie

---

**Document focus hacking API et protections essentielles.**

