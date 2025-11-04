# Cours Complet JWT : De A à Z

**Formation complète sur JSON Web Tokens**

---

## Table des Matières

1. [Qu'est-ce qu'un JWT ?](#quest-ce-quun-jwt)
2. [Structure d'un JWT](#structure-jwt)
3. [Génération du token](#génération-token)
4. [Refresh tokens](#refresh-tokens)
5. [Durée de vie](#durée-de-vie)
6. [Stockage : localStorage vs sessionStorage vs cookies](#stockage)
7. [Flow complet authentification](#flow-complet)
8. [Sécurité et bonnes pratiques](#sécurité)

---

## Qu'est-ce qu'un JWT ?

### Définition

**JWT** = JSON Web Token

**C'est quoi** : Un format de token sécurisé pour transmettre informations entre parties

**Analogie** : Comme un badge d'accès avec photo et permissions

**Format** : Trois parties séparées par des points

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjMiLCJyb2xlIjoiYWRtaW4ifQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

[     HEADER (base64)     ].[     PAYLOAD (base64)     ].[     SIGNATURE     ]
```

---

### Pourquoi JWT ?

**Avant JWT (Sessions classiques)** :

```
1. User login
2. Serveur crée session en mémoire
3. Serveur retourne session ID
4. Chaque requête : serveur lookup session en DB
```

**Problème** :
- Serveur doit stocker sessions (mémoire)
- Lookup DB à chaque requête (lent)
- Difficile à scaler (sessions en mémoire)

---

**Avec JWT** :

```
1. User login
2. Serveur génère JWT (pas de stockage)
3. Serveur retourne JWT
4. Chaque requête : serveur vérifie signature JWT (rapide, pas de DB)
```

**Avantages** :
- Serveur stateless (pas de stockage sessions)
- Rapide (pas de DB lookup)
- Scalable (pas de mémoire serveur)
- Cross-domain (APIs, microservices)

---

## Structure d'un JWT

### Les 3 Parties

**Format visuel** :

```
HEADER.PAYLOAD.SIGNATURE
```

---

### Partie 1 : Header

**Contenu** : Métadonnées sur le token

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Champs** :

| Champ | Signification | Valeurs Courantes |
|-------|---------------|-------------------|
| `alg` | Algorithme de signature | HS256, RS256, ES256 |
| `typ` | Type de token | JWT |

**Encodé en base64** : `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`

---

### Partie 2 : Payload

**Contenu** : Données de l'utilisateur (claims)

```json
{
  "userId": "123",
  "email": "user@example.com",
  "role": "admin",
  "subscription": "premium",
  "iat": 1699000000,
  "exp": 1699003600
}
```

**Claims standards** :

| Claim | Nom | Description | Exemple |
|-------|-----|-------------|---------|
| `sub` | Subject | ID utilisateur | "user-123" |
| `iat` | Issued At | Date création (timestamp) | 1699000000 |
| `exp` | Expiration | Date expiration (timestamp) | 1699003600 |
| `iss` | Issuer | Qui a émis le token | "myapp.com" |
| `aud` | Audience | Pour qui le token | "api.myapp.com" |
| `jti` | JWT ID | ID unique du token | "abc-123" |

**Claims custom** :
- `userId` : ID dans ta base
- `email` : Email user
- `role` : Rôle (admin, user)
- `subscription` : Tier abonnement
- Tout ce dont tu as besoin

**Important** : Ne JAMAIS mettre données sensibles (passwords, carte crédit)

**Encodé en base64** : `eyJ1c2VySWQiOiIxMjMiLCJlbWFpbCI6InVzZXJAZXhhbXBsZS5jb20ifQ`

---

### Partie 3 : Signature

**Contenu** : Signature cryptographique pour sécurité

**Calcul** :

```javascript
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret_key
)
```

**Exemple** :

```
Secret : "ma-cle-secrete-ultra-forte-256-bits"

Signature = HMAC-SHA256(
  "eyJhbGc...9" + "." + "eyJ1c2V...",
  "ma-cle-secrete-ultra-forte-256-bits"
)

Résultat : SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Rôle** :
- Vérifier que token n'a pas été modifié
- Seul serveur avec secret peut vérifier
- Si 1 caractère changé → signature invalide

---

### JWT Complet Assemblé

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJ1c2VySWQiOiIxMjMiLCJlbWFpbCI6InVzZXJAZXhhbXBsZS5jb20iLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE2OTkwMDAwMDAsImV4cCI6MTY5OTAwMzYwMH0
.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Décode sur** : [jwt.io](https://jwt.io)

---

## Génération du Token

### Flow Première Connexion

**Étape 1 : User se connecte**

```
User → POST /api/login
{
  "email": "user@example.com",
  "password": "motdepasse123"
}
```

---

**Étape 2 : Serveur vérifie credentials**

```javascript
// Serveur (Node.js exemple)
app.post('/api/login', async (req, res) => {
  const { email, password } = req.body
  
  // 1. Trouver user dans DB
  const user = await db.users.findByEmail(email)
  
  if (!user) {
    return res.status(401).json({ error: 'Invalid credentials' })
  }
  
  // 2. Vérifier password (hashé)
  const isValid = await bcrypt.compare(password, user.hashedPassword)
  
  if (!isValid) {
    return res.status(401).json({ error: 'Invalid credentials' })
  }
  
  // 3. Credentials valides → Générer JWT
  // (voir étape suivante)
})
```

---

**Étape 3 : Serveur génère JWT**

```javascript
const jwt = require('jsonwebtoken')

// Suite de l'endpoint login
// ...credentials valides...

// Créer payload
const payload = {
  userId: user.id,
  email: user.email,
  role: user.role,
  subscription: user.subscription
}

// Générer access token (courte durée)
const accessToken = jwt.sign(
  payload,
  process.env.JWT_SECRET,      // Clé secrète
  { expiresIn: '15m' }          // Expire dans 15 minutes
)

// Générer refresh token (longue durée)
const refreshToken = jwt.sign(
  { userId: user.id },
  process.env.REFRESH_SECRET,
  { expiresIn: '7d' }           // Expire dans 7 jours
)

// Sauvegarder refresh token en DB (optionnel mais recommandé)
await db.refreshTokens.create({
  userId: user.id,
  token: refreshToken,
  expiresAt: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
})

return res.json({
  accessToken,
  refreshToken,
  expiresIn: 900 // 15 minutes en secondes
})
```

---

**Étape 4 : Client reçoit tokens**

```javascript
// Frontend
const response = await fetch('/api/login', {
  method: 'POST',
  body: JSON.stringify({ email, password })
})

const { accessToken, refreshToken } = await response.json()

// Stocker tokens (voir section Stockage)
localStorage.setItem('accessToken', accessToken)
localStorage.setItem('refreshToken', refreshToken)
```

---

### Tableau Génération

| Étape | Acteur | Action | Résultat |
|-------|--------|--------|----------|
| **1** | Client | POST credentials | Envoi email/password |
| **2** | Serveur | Vérifier DB | User trouvé |
| **3** | Serveur | Vérifier password hash | Match confirmé |
| **4** | Serveur | Créer payload | JSON avec user info |
| **5** | Serveur | Signer avec secret | JWT créé |
| **6** | Serveur | Retourner tokens | Access + Refresh |
| **7** | Client | Stocker tokens | localStorage/cookie |

---

## Refresh Tokens

### Pourquoi Deux Tokens ?

**Access Token** :
- Courte durée (15 min - 1h)
- Utilisé pour chaque requête
- Si volé : exposition limitée (expire vite)

**Refresh Token** :
- Longue durée (7 jours - 30 jours)
- Utilisé UNIQUEMENT pour obtenir nouveau access token
- Si volé : détectable (invalidation possible)

---

### Flow Refresh Token

**Scénario** : Access token expiré après 15 minutes

**Étape 1 : Requête avec token expiré**

```javascript
// Frontend fait requête
const response = await fetch('/api/courses', {
  headers: {
    'Authorization': `Bearer ${accessToken}` // Token expiré
  }
})

// Serveur répond
// Status: 401 Unauthorized
// Body: { error: 'Token expired' }
```

---

**Étape 2 : Client détecte expiration**

```javascript
if (response.status === 401) {
  const errorData = await response.json()
  
  if (errorData.error === 'Token expired') {
    // Utiliser refresh token
    await refreshAccessToken()
  }
}
```

---

**Étape 3 : Demander nouveau access token**

```javascript
async function refreshAccessToken() {
  const refreshToken = localStorage.getItem('refreshToken')
  
  const response = await fetch('/api/refresh', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ refreshToken })
  })
  
  if (response.ok) {
    const { accessToken, refreshToken: newRefreshToken } = await response.json()
    
    // Sauvegarder nouveaux tokens
    localStorage.setItem('accessToken', accessToken)
    localStorage.setItem('refreshToken', newRefreshToken)
    
    // Retry requête originale
    return true
  } else {
    // Refresh token invalide/expiré → re-login
    redirectToLogin()
    return false
  }
}
```

---

**Étape 4 : Serveur vérifie refresh token et génère nouveau access token**

```javascript
app.post('/api/refresh', async (req, res) => {
  const { refreshToken } = req.body
  
  if (!refreshToken) {
    return res.status(401).json({ error: 'Refresh token required' })
  }
  
  try {
    // 1. Vérifier signature refresh token
    const decoded = jwt.verify(refreshToken, process.env.REFRESH_SECRET)
    
    // 2. Vérifier que token existe en DB (optionnel mais recommandé)
    const tokenInDb = await db.refreshTokens.findOne({
      userId: decoded.userId,
      token: refreshToken
    })
    
    if (!tokenInDb) {
      return res.status(401).json({ error: 'Invalid refresh token' })
    }
    
    // 3. Récupérer user actuel
    const user = await db.users.findById(decoded.userId)
    
    // 4. Générer nouveau access token
    const newAccessToken = jwt.sign(
      {
        userId: user.id,
        email: user.email,
        role: user.role,
        subscription: user.subscription
      },
      process.env.JWT_SECRET,
      { expiresIn: '15m' }
    )
    
    // 5. Optionnel : Rotation refresh token (sécurité++)
    const newRefreshToken = jwt.sign(
      { userId: user.id },
      process.env.REFRESH_SECRET,
      { expiresIn: '7d' }
    )
    
    // Invalider ancien refresh token
    await db.refreshTokens.delete(tokenInDb.id)
    
    // Sauvegarder nouveau
    await db.refreshTokens.create({
      userId: user.id,
      token: newRefreshToken,
      expiresAt: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
    })
    
    return res.json({
      accessToken: newAccessToken,
      refreshToken: newRefreshToken,
      expiresIn: 900
    })
    
  } catch (error) {
    return res.status(401).json({ error: 'Invalid refresh token' })
  }
})
```

---

### Diagramme Flow Refresh

```mermaid
sequenceDiagram
    participant C as Client
    participant API as API Server
    participant DB as Database
    
    Note over C,API: Access Token Expiré
    
    C->>API: GET /api/data + Access Token (expiré)
    API->>API: Vérifier token
    API->>API: Token expiré
    API->>C: 401 Unauthorized {error: "Token expired"}
    
    Note over C,API: Refresh Flow
    
    C->>C: Récupérer refresh token
    C->>API: POST /api/refresh + Refresh Token
    API->>API: Vérifier signature refresh
    API->>DB: Vérifier token existe
    DB->>API: Token valide
    API->>DB: Get user data
    DB->>API: User info
    API->>API: Générer nouveau access token
    API->>API: Générer nouveau refresh token
    API->>DB: Invalider ancien refresh
    API->>DB: Sauvegarder nouveau refresh
    API->>C: Nouveaux tokens
    
    Note over C,API: Retry Requête Originale
    
    C->>API: GET /api/data + Nouveau Access Token
    API->>C: 200 OK + Données
```

---

### Tableau Access vs Refresh Token

| Aspect | Access Token | Refresh Token |
|--------|--------------|---------------|
| **Durée de vie** | Courte (15 min - 1h) | Longue (7 jours - 30 jours) |
| **Usage** | Chaque requête API | Obtenir nouveau access token |
| **Fréquence** | Très fréquent | Rare (toutes les 15-60 min) |
| **Contenu** | User info complète | Juste user ID |
| **Stockage** | localStorage/memory | localStorage/httpOnly cookie |
| **Si volé** | Expire vite (15 min) | Détectable, révocable |
| **Révocable** | Non (stateless) | Oui (vérif DB) |

---

## Durée de Vie des Tokens

### Durées Recommandées

| Type Token | Durée | Justification |
|------------|-------|---------------|
| **Access Token** | 15 minutes | Balance sécurité/UX |
| **Access Token (mobile)** | 1 heure | UX mobile (moins de refresh) |
| **Refresh Token** | 7 jours | Pas de re-login fréquent |
| **Refresh Token (remember me)** | 30 jours | Convenance user |
| **Session cookie** | Session browser | Alternative JWT |

---

### Calcul Timestamps

**Exemple** :

```javascript
const now = Date.now() // 1699000000000 (millisecondes)
const nowSeconds = Math.floor(now / 1000) // 1699000000 (secondes)

// Access token : expire dans 15 minutes
const accessExp = nowSeconds + (15 * 60) // +900 secondes
// exp: 1699000900

// Refresh token : expire dans 7 jours
const refreshExp = nowSeconds + (7 * 24 * 60 * 60) // +604800 secondes
// exp: 1699604800
```

**Dans JWT** :

```json
{
  "userId": "123",
  "iat": 1699000000,    // Créé à (timestamp)
  "exp": 1699000900     // Expire à (timestamp)
}
```

---

### Vérification Expiration

**Serveur** :

```javascript
app.get('/api/protected', (req, res) => {
  const token = req.headers.authorization?.split(' ')[1]
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET)
    // Si expiré, jwt.verify() lance erreur "TokenExpiredError"
    
    // Token valide
    return res.json({ data: 'Protected data' })
    
  } catch (error) {
    if (error.name === 'TokenExpiredError') {
      return res.status(401).json({ error: 'Token expired' })
    }
    
    return res.status(401).json({ error: 'Invalid token' })
  }
})
```

**Client** : Vérifier avant d'envoyer (optionnel)

```javascript
function isTokenExpired(token) {
  const decoded = JSON.parse(atob(token.split('.')[1]))
  const now = Date.now() / 1000
  
  return decoded.exp < now
}

if (isTokenExpired(accessToken)) {
  await refreshAccessToken()
}
```

---

## Stockage des Tokens

### Options de Stockage

**3 options principales** :

1. **localStorage**
2. **sessionStorage**
3. **Cookies (httpOnly)**

---

### localStorage

**Caractéristiques** :

| Aspect | Valeur |
|--------|--------|
| **Persiste** | Oui (même après fermeture browser) |
| **Capacité** | 5-10 MB |
| **Accessible** | JavaScript (window.localStorage) |
| **Sécurité XSS** | Vulnérable |
| **Partage onglets** | Oui |
| **Expiration** | Jamais (manuel) |

**Usage** :

```javascript
// Sauvegarder
localStorage.setItem('accessToken', token)

// Récupérer
const token = localStorage.getItem('accessToken')

// Supprimer
localStorage.removeItem('accessToken')

// Tout supprimer
localStorage.clear()
```

**Avantages** :
- Simple à utiliser
- Persiste (user pas obligé re-login)
- Accessible facilement

**Inconvénients** :
- Vulnérable XSS (script malveillant peut lire)
- Pas de protection native
- Accessible par tout JavaScript (même libs tierces)

---

### sessionStorage

**Caractéristiques** :

| Aspect | Valeur |
|--------|--------|
| **Persiste** | Non (supprimé à fermeture onglet) |
| **Capacité** | 5-10 MB |
| **Accessible** | JavaScript (window.sessionStorage) |
| **Sécurité XSS** | Vulnérable |
| **Partage onglets** | Non (isolé par onglet) |
| **Expiration** | Fermeture onglet |

**Usage** :

```javascript
// Identique à localStorage
sessionStorage.setItem('accessToken', token)
const token = sessionStorage.getItem('accessToken')
sessionStorage.removeItem('accessToken')
```

**Différence clé avec localStorage** :

```
localStorage : Fermer browser → Token reste
sessionStorage : Fermer onglet → Token supprimé
```

**Avantages** :
- Plus sécurisé que localStorage (supprimé auto)
- Isolé par onglet

**Inconvénients** :
- User doit re-login à chaque onglet
- Vulnérable XSS aussi
- Perte si fermeture accidentelle

---

### Cookies (httpOnly)

**Caractéristiques** :

| Aspect | Valeur |
|--------|--------|
| **Persiste** | Configurable |
| **Capacité** | 4 KB |
| **Accessible** | Non accessible JavaScript si httpOnly |
| **Sécurité XSS** | Protégé si httpOnly |
| **Partage onglets** | Oui |
| **Expiration** | Configurable |

**Serveur définit cookie** :

```javascript
app.post('/api/login', async (req, res) => {
  // ... vérification credentials ...
  
  const accessToken = jwt.sign(payload, secret, { expiresIn: '15m' })
  
  // Définir cookie httpOnly
  res.cookie('accessToken', accessToken, {
    httpOnly: true,     // Pas accessible JavaScript (sécurité XSS)
    secure: true,       // HTTPS uniquement
    sameSite: 'strict', // Protection CSRF
    maxAge: 15 * 60 * 1000 // 15 minutes
  })
  
  return res.json({ success: true })
})
```

**Client ne voit pas le token** :

```javascript
// JavaScript ne peut PAS accéder
document.cookie // Ne contient pas httpOnly cookies

// Token envoyé automatiquement avec chaque requête
fetch('/api/protected') // Cookie envoyé auto par browser
```

**Avantages** :
- Protégé XSS (httpOnly)
- Envoyé automatiquement
- Protection CSRF (sameSite)

**Inconvénients** :
- Moins flexible (pas d'accès JS)
- CSRF si mal configuré
- Pas pour apps mobiles (pas de cookies)

---

### Comparaison Stockages

| Aspect | localStorage | sessionStorage | Cookie httpOnly |
|--------|--------------|----------------|-----------------|
| **Persiste** | Oui | Non | Configurable |
| **XSS Protection** | Non | Non | Oui |
| **CSRF Protection** | N/A | N/A | Si sameSite |
| **Mobile apps** | Oui | Oui | Non |
| **Simplicité** | Très simple | Très simple | Moyenne |
| **Sécurité** | Faible | Faible | Élevée |
| **Usage recommandé** | Refresh token | Temp data | Access token |

---

### Recommandations Stockage

**Pour Web Apps** :

```
Access Token → Cookie httpOnly (sécurité XSS)
Refresh Token → localStorage (besoin accès JS pour refresh)
```

**Pour Mobile Apps** :

```
Access Token → Secure storage native (Keychain iOS, Keystore Android)
Refresh Token → Secure storage native
```

**Pour SPAs (React, Vue)** :

```
Option A (Sécurisé) :
  Access Token → Cookie httpOnly
  Refresh Token → Cookie httpOnly
  
Option B (Pratique) :
  Access Token → Memory (state)
  Refresh Token → localStorage
```

---

## Flow Complet Authentification

### Diagramme Complet

```mermaid
sequenceDiagram
    participant U as User
    participant F as Frontend
    participant API as API Server
    participant DB as Database
    
    Note over U,DB: 1. LOGIN INITIAL
    
    U->>F: Email + Password
    F->>API: POST /api/login
    API->>DB: Vérifier credentials
    DB->>API: User valide
    API->>API: Générer Access Token (15min)
    API->>API: Générer Refresh Token (7d)
    API->>DB: Sauvegarder refresh token
    API->>F: {accessToken, refreshToken}
    F->>F: localStorage.setItem tokens
    
    Note over U,DB: 2. REQUÊTES NORMALES (15 min)
    
    F->>API: GET /api/data + Access Token
    API->>API: Vérifier signature
    API->>API: Vérifier expiration (OK)
    API->>F: 200 OK + Données
    
    Note over U,DB: 3. TOKEN EXPIRÉ (après 15 min)
    
    F->>API: GET /api/data + Access Token (expiré)
    API->>API: Vérifier expiration (EXPIRÉ)
    API->>F: 401 Token expired
    
    Note over U,DB: 4. REFRESH
    
    F->>F: Détecter expiration
    F->>API: POST /api/refresh + Refresh Token
    API->>API: Vérifier refresh signature
    API->>DB: Vérifier refresh existe
    DB->>API: Refresh valide
    API->>API: Générer nouveau Access (15min)
    API->>API: Générer nouveau Refresh (7d)
    API->>DB: Invalider ancien refresh
    API->>DB: Sauvegarder nouveau refresh
    API->>F: Nouveaux tokens
    F->>F: Update localStorage
    
    Note over U,DB: 5. RETRY REQUÊTE
    
    F->>API: GET /api/data + Nouveau Access Token
    API->>F: 200 OK + Données
    
    Note over U,DB: 6. LOGOUT
    
    U->>F: Clic Logout
    F->>API: POST /api/logout + Refresh Token
    API->>DB: Invalider refresh token
    F->>F: localStorage.clear()
    F->>U: Redirect login
```

---

### Tableau Étapes Flow

| Phase | Durée | Tokens Actifs | Actions |
|-------|-------|---------------|---------|
| **Login** | Instant | Access (15min) + Refresh (7j) | Générer les deux |
| **Utilisation** | 0-15 min | Access valide | Requêtes normales |
| **Expiration** | 15 min | Access expiré, Refresh valide | Erreur 401 |
| **Refresh** | Instant | Nouveaux tokens | Refresh flow |
| **Utilisation** | 15 min | Nouveau access valide | Requêtes normales |
| **Logout** | Instant | Tous invalidés | Clear storage + DB |

---

## Composants d'un Token Détaillé

### Header Complet

```json
{
  "alg": "HS256",        // Algorithm : HS256, RS256, ES256
  "typ": "JWT",          // Type : Toujours JWT
  "kid": "key-id-123"    // Key ID (optionnel, si rotation clés)
}
```

**Algorithmes** :

| Algorithme | Type | Sécurité | Usage |
|------------|------|----------|-------|
| **HS256** | Symétrique (HMAC) | Bonne | 1 serveur |
| **RS256** | Asymétrique (RSA) | Excellente | Microservices |
| **ES256** | Asymétrique (ECDSA) | Excellente | Performance |

---

### Payload Complet

```json
{
  // Claims standards (registered)
  "iss": "https://myapp.com",           // Issuer
  "sub": "user-123",                    // Subject (user ID)
  "aud": "https://api.myapp.com",       // Audience
  "exp": 1699003600,                    // Expiration
  "nbf": 1699000000,                    // Not Before
  "iat": 1699000000,                    // Issued At
  "jti": "token-uuid-456",              // JWT ID
  
  // Claims custom (privés)
  "userId": "123",
  "email": "user@example.com",
  "role": "admin",
  "subscription": "premium",
  "permissions": ["read", "write", "delete"],
  "orgId": "org-789"
}
```

**Claims à inclure** :

| Claim | Obligatoire | Usage | Exemple |
|-------|-------------|-------|---------|
| `sub` | Recommandé | User ID unique | "user-123" |
| `exp` | Obligatoire | Sécurité | 1699003600 |
| `iat` | Recommandé | Audit | 1699000000 |
| `userId` | Selon app | Pratique | "123" |
| `role` | Si RBAC | Authorisation | "admin" |
| `email` | Optionnel | Display | "user@example.com" |

**Ce qu'il ne faut JAMAIS mettre** :
- Passwords
- Numéros carte crédit
- Secrets API
- Données sensibles

---

### Signature Détaillée

**Algorithmes signature** :

**HS256 (Symétrique)** :

```javascript
const crypto = require('crypto')

const header = base64url(JSON.stringify({alg: "HS256", typ: "JWT"}))
const payload = base64url(JSON.stringify({userId: "123", exp: 1699003600}))

const signature = crypto
  .createHmac('sha256', SECRET_KEY)
  .update(header + '.' + payload)
  .digest('base64url')

const jwt = header + '.' + payload + '.' + signature
```

**RS256 (Asymétrique)** :

```javascript
const crypto = require('crypto')
const fs = require('fs')

// Générer paires clés (une fois)
const { publicKey, privateKey } = crypto.generateKeyPairSync('rsa', {
  modulusLength: 2048
})

// Signer avec clé privée
const signature = crypto
  .createSign('RSA-SHA256')
  .update(header + '.' + payload)
  .sign(privateKey, 'base64url')

// Vérifier avec clé publique (n'importe qui peut vérifier)
const isValid = crypto
  .createVerify('RSA-SHA256')
  .update(header + '.' + payload)
  .verify(publicKey, signature, 'base64url')
```

**Différence clé** :

| Aspect | HS256 | RS256 |
|--------|-------|-------|
| **Type** | Symétrique | Asymétrique |
| **Clés** | 1 secret partagé | Publique + Privée |
| **Signer** | Secret | Clé privée |
| **Vérifier** | Même secret | Clé publique |
| **Distribution** | Difficile (secret partagé) | Facile (publique distribuable) |
| **Use case** | Serveur unique | Microservices |

---

## localStorage vs sessionStorage vs Cookies

### Tableau Comparaison Complète

| Caractéristique | localStorage | sessionStorage | Cookie httpOnly |
|----------------|--------------|----------------|-----------------|
| **Persistance** | Permanent | Session onglet | Configurable |
| **Capacité** | 5-10 MB | 5-10 MB | 4 KB |
| **Accessible JS** | Oui | Oui | Non (httpOnly) |
| **Envoi auto serveur** | Non | Non | Oui |
| **XSS Vulnérable** | Oui | Oui | Non (httpOnly) |
| **CSRF Vulnérable** | Non | Non | Oui (si mal config) |
| **Expire à fermeture** | Non | Oui | Configurable |
| **Partage onglets** | Oui | Non | Oui |
| **Mobile apps** | Oui | Oui | Non |
| **API calls** | Manuel (header) | Manuel (header) | Auto |

---

### Quand Utiliser Quoi

**localStorage** :

Utiliser pour :
- Refresh tokens (web apps)
- Préférences user
- Cache données
- Remember me

Ne PAS utiliser pour :
- Access tokens (si alternative plus sécurisée)
- Données très sensibles

---

**sessionStorage** :

Utiliser pour :
- Données temporaires session
- Wizards multi-étapes
- Temp cache

Ne PAS utiliser pour :
- Tokens (localStorage ou cookie mieux)
- Données devant persister

---

**Cookies httpOnly** :

Utiliser pour :
- Access tokens (protection XSS)
- Session IDs
- Applications web classiques

Ne PAS utiliser pour :
- Mobile apps (pas de cookies)
- APIs cross-domain (CORS compliqué)

---

### Code Exemple Chaque Stockage

**localStorage** :

```javascript
// Après login
const { accessToken, refreshToken } = await loginResponse.json()

localStorage.setItem('accessToken', accessToken)
localStorage.setItem('refreshToken', refreshToken)

// Chaque requête
const token = localStorage.getItem('accessToken')

fetch('/api/data', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
})

// Logout
localStorage.removeItem('accessToken')
localStorage.removeItem('refreshToken')
```

---

**sessionStorage** :

```javascript
// Identique mais session uniquement
sessionStorage.setItem('accessToken', accessToken)

// Fermer onglet → token supprimé auto
```

---

**Cookie httpOnly** :

```javascript
// Serveur set cookie (client ne fait rien)
res.cookie('accessToken', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
  maxAge: 15 * 60 * 1000
})

// Client : Token envoyé automatiquement
fetch('/api/data') // Pas besoin header Authorization

// Logout (serveur clear cookie)
res.clearCookie('accessToken')
```

---

## Sécurité et Bonnes Pratiques

### Tableau Bonnes Pratiques

| Pratique | Pourquoi | Comment |
|----------|----------|---------|
| **Secret fort** | Éviter JWT forging | 256 bits minimum, crypto.randomBytes |
| **Expiration courte** | Limiter exposition | 15 min access, 7j refresh |
| **HTTPS obligatoire** | Éviter interception | SSL/TLS certificat |
| **Pas de données sensibles** | Privacy | Jamais password, carte crédit |
| **Vérifier signature** | Intégrité | jwt.verify() toujours |
| **Refresh rotation** | Sécurité | Nouveau refresh à chaque refresh |
| **Révocation possible** | Contrôle | Refresh tokens en DB |
| **httpOnly cookies** | XSS protection | Si possible |
| **sameSite strict** | CSRF protection | Cookies |

---

### Dangers à Éviter

| Danger | Impact | Solution |
|--------|--------|----------|
| **Secret faible** | Token forgeable | Minimum 256 bits |
| **Pas d'expiration** | Token volé utilisable toujours | exp obligatoire |
| **Données sensibles** | Privacy leak | Jamais dans JWT |
| **localStorage seul** | XSS vulnérable | httpOnly cookie préférable |
| **Pas de HTTPS** | Token interceptable | SSL obligatoire |
| **Pas de refresh** | UX mauvaise (re-login fréquent) | Refresh token |
| **Refresh non révocable** | Pas de contrôle si volé | DB refresh tokens |

---

## Cas d'Usage par Type App

### Web App (Next.js, React)

**Recommandation** :

```javascript
// Access Token : Cookie httpOnly (sécurité)
res.cookie('accessToken', token, { httpOnly: true })

// Refresh Token : localStorage (besoin JS pour refresh)
// Client reçoit et stocke
localStorage.setItem('refreshToken', refreshToken)
```

**Avantages** :
- XSS ne peut pas voler access token
- Refresh en localStorage pour logique client
- Balance sécurité/praticité

---

### Mobile App (React Native, Flutter)

**Recommandation** :

```javascript
// Pas de cookies sur mobile
// Utiliser secure storage natif

// React Native
import * as SecureStore from 'expo-secure-store'

await SecureStore.setItemAsync('accessToken', token)
const token = await SecureStore.getItemAsync('accessToken')

// Flutter
import 'package:flutter_secure_storage/flutter_secure_storage.dart'

final storage = FlutterSecureStorage()
await storage.write(key: 'accessToken', value: token)
String token = await storage.read(key: 'accessToken')
```

**Pas localStorage** : Pas assez sécurisé mobile

---

### SPA (Single Page Application)

**Recommandation** :

```javascript
// Access Token : Memory/State (plus sécurisé)
const [accessToken, setAccessToken] = useState(null)

// Refresh Token : localStorage
localStorage.setItem('refreshToken', refreshToken)

// Au chargement page
useEffect(() => {
  const refresh = localStorage.getItem('refreshToken')
  if (refresh) {
    refreshAccessToken(refresh)
  }
}, [])
```

**Avantages** :
- Access token jamais persisté (sécurité)
- Refresh au chargement page
- XSS ne peut pas voler access (en mémoire)

---

## Résumé Visuel

### Cycle de Vie JWT

```mermaid
stateDiagram-v2
    [*] --> Login
    Login --> AccessTokenValid : Générer tokens
    
    AccessTokenValid --> MakingRequests : Utiliser access token
    MakingRequests --> AccessTokenValid : Requêtes OK (< 15 min)
    
    MakingRequests --> AccessTokenExpired : 15 minutes passées
    AccessTokenExpired --> RefreshFlow : Utiliser refresh token
    
    RefreshFlow --> AccessTokenValid : Nouveaux tokens
    RefreshFlow --> Login : Refresh expiré (7 jours)
    
    AccessTokenValid --> Logout : User logout
    Logout --> [*] : Clear tokens
```

---

### Comparaison Stockages

```mermaid
graph TB
    subgraph "localStorage"
        L1[Persiste toujours]
        L2[Vulnérable XSS]
        L3[Partage onglets]
        L4[Capacité 5-10 MB]
    end
    
    subgraph "sessionStorage"
        S1[Supprimé à fermeture]
        S2[Vulnérable XSS]
        S3[Isolé par onglet]
        S4[Capacité 5-10 MB]
    end
    
    subgraph "Cookie httpOnly"
        C1[Configurable]
        C2[Protégé XSS]
        C3[Partage onglets]
        C4[Capacité 4 KB]
    end
    
    style C2 fill:#90EE90
    style L2 fill:#FF6B6B
    style S2 fill:#FF6B6B
```

---

## Conclusion

### Points Clés à Retenir

**Structure JWT** :
- 3 parties : Header.Payload.Signature
- Header : Algorithme
- Payload : Données user
- Signature : Sécurité (vérifier intégrité)

**Tokens** :
- Access token : Court (15 min), usage fréquent
- Refresh token : Long (7 jours), usage rare

**Stockage** :
- localStorage : Persiste, vulnérable XSS
- sessionStorage : Temporaire, vulnérable XSS
- Cookie httpOnly : Sécurisé XSS, recommandé

**Flow** :
1. Login → Recevoir tokens
2. Utiliser access (15 min)
3. Access expire → Refresh
4. Recevoir nouveaux tokens
5. Répéter jusqu'à logout

**Sécurité** :
- Secret fort (256 bits)
- Expiration obligatoire
- HTTPS toujours
- httpOnly si possible
- Pas de données sensibles

---

**Cours complet JWT de niveau débutant à avancé.**

