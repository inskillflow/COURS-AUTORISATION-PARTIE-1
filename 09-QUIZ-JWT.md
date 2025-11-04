# Quiz Complet : JWT (JSON Web Tokens)

**Test de connaissances sur les JWT**

---

## Instructions

- 50 questions
- Réponses en accordéon (cliquer pour voir)
- Score : 1 point par bonne réponse
- Temps recommandé : 45 minutes

---

## Section 1 : Concepts de Base (10 questions)


- **A)** Java Web Token
- **B)** JSON Web Token
- **C)** JavaScript Web Token
- **D)** JSON Web Transport

<details>
<summary><h3>Question 1 : Que signifie JWT ?</h3></summary>

**Réponse : B) JSON Web Token**

</details>

---

<details>
<summary><h3>Question 2 : Combien de parties compose un JWT ?</h3></summary>

**A)** 2 parties
**B)** 3 parties
**C)** 4 parties
**D)** Variable

**Réponse : B) 3 parties (Header, Payload, Signature)**

</details>

---

<details>
<summary><h3>Question 3 : Quel séparateur entre les parties d'un JWT ?</h3></summary>

**A)** Virgule (,)
**B)** Slash (/)
**C)** Point (.)
**D)** Tiret (-)

**Réponse : C) Point (.)**

Format : `header.payload.signature`

</details>

---

<details>
<summary><h3>Question 4 : Le payload d'un JWT est encodé en quoi ?</h3></summary>

**A)** Base64
**B)** Hexadécimal
**C)** Encryption AES
**D)** Plain text

**Réponse : A) Base64**

**Important** : Encodé ≠ Encrypté. N'importe qui peut décoder et lire le payload.

</details>

---

<details>
<summary><h3>Question 5 : Quelle information ne doit JAMAIS être dans un JWT ?</h3></summary>

**A)** User ID
**B)** Password
**C)** Email
**D)** Role

**Réponse : B) Password**

Les JWT sont décodables. Ne jamais mettre : passwords, numéros carte crédit, secrets.

</details>

---

<details>
<summary><h3>Question 6 : À quoi sert la signature d'un JWT ?</h3></summary>

**A)** Encrypter le payload
**B)** Vérifier que le token n'a pas été modifié
**C)** Compresser le token
**D)** Identifier l'utilisateur

**Réponse : B) Vérifier que le token n'a pas été modifié**

Si 1 caractère du payload change, la signature ne correspond plus.

</details>

---

<details>
<summary><h3>Question 7 : Un JWT est-il encrypté par défaut ?</h3></summary>

**A)** Oui, toujours
**B)** Non, il est encodé (lisible)
**C)** Seulement le payload
**D)** Selon l'algorithme

**Réponse : B) Non, il est encodé (base64), pas encrypté**

N'importe qui peut décoder et lire le contenu avec jwt.io ou base64 decode.

</details>

---

<details>
<summary><h3>Question 8 : Où le JWT est-il vérifié ?</h3></summary>

**A)** Côté client (browser)
**B)** Côté serveur
**C)** Dans la base de données
**D)** Les deux

**Réponse : B) Côté serveur**

Seul le serveur a la clé secrète pour vérifier la signature.

</details>

---

<details>
<summary><h3>Question 9 : Quel avantage principal du JWT vs sessions ?</h3></summary>

**A)** Plus rapide
**B)** Serveur stateless (pas de stockage)
**C)** Plus sécurisé
**D)** Plus simple

**Réponse : B) Serveur stateless**

Pas besoin de stocker sessions en mémoire ou DB. Le token contient toutes les infos.

</details>

---

<details>
<summary><h3>Question 10 : JWT est un standard défini par quel RFC ?</h3></summary>

**A)** RFC 6749
**B)** RFC 7519
**C)** RFC 2616
**D)** RFC 7230

**Réponse : B) RFC 7519**

</details>

---

## Section 2 : Structure et Composants (10 questions)

<details>
<summary><h3>Question 11 : Que contient le Header d'un JWT ?</h3></summary>

**A)** User ID
**B)** Algorithme de signature et type
**C)** Password
**D)** Date expiration

**Réponse : B) Algorithme (alg) et Type (typ)**

Exemple : `{"alg":"HS256","typ":"JWT"}`

</details>

---

<details>
<summary><h3>Question 12 : Quel claim indique la date d'expiration ?</h3></summary>

**A)** iat
**B)** nbf
**C)** exp
**D)** exp_date

**Réponse : C) exp (expiration)**

Timestamp Unix en secondes.

</details>

---

<details>
<summary><h3>Question 13 : Que signifie "iat" dans le payload ?</h3></summary>

**A)** Is Admin Token
**B)** Issued At (date création)
**C)** Invalid At
**D)** Issuer Authentication

**Réponse : B) Issued At**

Timestamp de création du token.

</details>

---

<details>
<summary><h3>Question 14 : Quel claim identifie l'utilisateur ?</h3></summary>

**A)** sub (subject)
**B)** user
**C)** id
**D)** uid

**Réponse : A) sub (subject)**

Claim standard pour identifier le sujet (généralement user ID).

</details>

---

<details>
<summary><h3>Question 15 : HS256 est quel type d'algorithme ?</h3></summary>

**A)** Asymétrique
**B)** Symétrique
**C)** Hash seulement
**D)** Encryption

**Réponse : B) Symétrique (HMAC avec SHA-256)**

Même clé pour signer et vérifier.

</details>

---

<details>
<summary><h3>Question 16 : RS256 utilise quoi pour signer ?</h3></summary>

**A)** Secret partagé
**B)** Clé privée
**C)** Clé publique
**D)** Password user

**Réponse : B) Clé privée**

RS256 = RSA asymétrique. Signer avec privée, vérifier avec publique.

</details>

---

<details>
<summary><h3>Question 17 : Quelle taille minimum pour le secret HS256 ?</h3></summary>

**A)** 64 bits
**B)** 128 bits
**C)** 256 bits
**D)** 512 bits

**Réponse : C) 256 bits minimum**

Pour éviter brute force et forging.

</details>

---

<details>
<summary><h3>Question 18 : Que contient le claim "aud" ?</h3></summary>

**A)** Audio file
**B)** Audience (pour qui le token)
**C)** Author
**D)** Authentication date

**Réponse : B) Audience**

Identifie le destinataire prévu du token.

</details>

---

<details>
<summary><h3>Question 19 : Un JWT peut-il être modifié sans invalider la signature ?</h3></summary>

**A)** Oui, facilement
**B)** Oui, avec outils
**C)** Non, signature devient invalide
**D)** Seulement le header

**Réponse : C) Non**

Tout changement (header, payload) invalide la signature.

</details>

---

<details>
<summary><h3>Question 20 : Format timestamp dans JWT ?</h3></summary>

**A)** Millisecondes
**B)** Secondes (Unix timestamp)
**C)** ISO 8601
**D)** Date string

**Réponse : B) Secondes (Unix timestamp)**

Exemple : `1699000000` = secondes depuis 1970-01-01

</details>

---

## Section 3 : Access et Refresh Tokens (10 questions)

<details>
<summary><h3>Question 21 : Durée de vie typique d'un access token ?</h3></summary>

**A)** 15 secondes
**B)** 15 minutes à 1 heure
**C)** 24 heures
**D)** 7 jours

**Réponse : B) 15 minutes à 1 heure**

Court pour limiter exposition si volé.

</details>

---

<details>
<summary><h3>Question 22 : Pourquoi un refresh token ?</h3></summary>

**A)** Rafraîchir la page
**B)** Obtenir nouveau access token sans re-login
**C)** Actualiser données user
**D)** Supprimer cache

**Réponse : B) Obtenir nouveau access token**

Évite re-login toutes les 15 minutes.

</details>

---

<details>
<summary><h3>Question 23 : Durée de vie typique refresh token ?</h3></summary>

**A)** 15 minutes
**B)** 1 heure
**C)** 7 à 30 jours
**D)** Jamais expire

**Réponse : C) 7 à 30 jours**

Long pour UX, mais pas infini pour sécurité.

</details>

---

<details>
<summary><h3>Question 24 : Que contient un refresh token ?</h3></summary>

**A)** Toutes infos user (comme access)
**B)** Juste user ID minimal
**C)** Seulement email
**D)** Rien

**Réponse : B) Juste user ID minimal**

Refresh token contient moins d'infos (juste assez pour identifier user).

</details>

---

<details>
<summary><h3>Question 25 : Refresh token doit-il être stocké en database ?</h3></summary>

**A)** Non, jamais
**B)** Oui, fortement recommandé
**C)** Optionnel
**D)** Seulement en production

**Réponse : B) Oui, fortement recommandé**

Permet révocation si compromis.

</details>

---

<details>
<summary><h3>Question 26 : Qu'est-ce que la rotation de refresh token ?</h3></summary>

**A)** Changer l'ordre des tokens
**B)** Générer nouveau refresh à chaque refresh
**C)** Tourner le secret périodiquement
**D)** Supprimer tokens anciens

**Réponse : B) Générer nouveau refresh token à chaque utilisation**

Sécurité : ancien refresh invalidé, nouveau émis.

</details>

---

<details>
<summary><h3>Question 27 : Si access token expiré, que fait le client ?</h3></summary>

**A)** Re-login immédiatement
**B)** Utiliser refresh token pour obtenir nouveau access
**C)** Attendre expiration refresh aussi
**D)** Continuer avec token expiré

**Réponse : B) Utiliser refresh token**

Flow refresh évite re-login constant.

</details>

---

<details>
<summary><h3>Question 28 : Access token est-il révocable facilement ?</h3></summary>

**A)** Oui, dans database
**B)** Non, stateless (expire naturellement)
**C)** Oui, avec blacklist
**D)** B et C

**Réponse : D) Non par nature (stateless), mais blacklist possible**

JWT stateless = pas de DB. Révocation nécessite blacklist (complexe).

</details>

---

<details>
<summary><h3>Question 29 : Refresh token est-il révocable ?</h3></summary>

**A)** Oui, si stocké en DB
**B)** Non, jamais
**C)** Seulement par user
**D)** Automatiquement

**Réponse : A) Oui, si stocké en database**

Supprimer de DB = token invalide au prochain refresh.

</details>

---

<details>
<summary><h3>Question 30 : Que faire si refresh token volé et détecté ?</h3></summary>

**A)** Ignorer
**B)** Invalider ce refresh + forcer re-login
**C)** Changer password user
**D)** B et C

**Réponse : D) Invalider refresh + forcer re-login (et changer password recommandé)**

</details>

---

## Section 4 : Stockage (10 questions)

<details>
<summary><h3>Question 31 : localStorage persiste après fermeture browser ?</h3></summary>

**A)** Oui
**B)** Non
**C)** Selon configuration
**D)** Seulement si remember me

**Réponse : A) Oui**

localStorage persiste toujours (permanent).

</details>

---

<details>
<summary><h3>Question 32 : sessionStorage persiste après fermeture onglet ?</h3></summary>

**A)** Oui
**B)** Non, supprimé à fermeture onglet
**C)** Oui, mais pas browser
**D)** Selon browser

**Réponse : B) Non, supprimé à fermeture onglet**

sessionStorage = durée session onglet uniquement.

</details>

---

<details>
<summary><h3>Question 33 : Cookie httpOnly est accessible via JavaScript ?</h3></summary>

**A)** Oui, avec document.cookie
**B)** Non, pas accessible JavaScript
**C)** Oui, avec getCookie()
**D)** Selon configuration

**Réponse : B) Non, pas accessible JavaScript**

C'est le but de httpOnly : protection XSS.

</details>

---

<details>
<summary><h3>Question 34 : Quelle capacité maximum localStorage ?</h3></summary>

**A)** 4 KB
**B)** 1 MB
**C)** 5-10 MB (selon browser)
**D)** Illimité

**Réponse : C) 5-10 MB**

Varie selon navigateur (5 MB Chrome, 10 MB Firefox).

</details>

---

<details>
<summary><h3>Question 35 : Quelle capacité maximum d'un cookie ?</h3></summary>

**A)** 4 KB
**B)** 1 MB
**C)** 5 MB
**D)** Illimité

**Réponse : A) 4 KB**

Beaucoup plus limité que localStorage.

</details>

---

<details>
<summary><h3>Question 36 : localStorage est vulnérable à quelle attaque ?</h3></summary>

**A)** SQL Injection
**B)** XSS (Cross-Site Scripting)
**C)** DDoS
**D)** Phishing

**Réponse : B) XSS**

Script malveillant peut lire localStorage.

</details>

---

<details>
<summary><h3>Question 37 : Cookie httpOnly protège contre quelle attaque ?</h3></summary>

**A)** XSS
**B)** CSRF
**C)** SQL Injection
**D)** Toutes

**Réponse : A) XSS**

httpOnly = JavaScript ne peut pas lire. Mais CSRF reste possible (protection : sameSite).

</details>

---

<details>
<summary><h3>Question 38 : Attribut cookie pour protection CSRF ?</h3></summary>

**A)** httpOnly
**B)** secure
**C)** sameSite
**D)** expires

**Réponse : C) sameSite**

`sameSite: 'strict'` empêche envoi cookie depuis autre site.

</details>

---

<details>
<summary><h3>Question 39 : Meilleur stockage pour access token (web app) ?</h3></summary>

**A)** localStorage
**B)** sessionStorage
**C)** Cookie httpOnly
**D)** Memory (state)

**Réponse : C) Cookie httpOnly ou D) Memory**

httpOnly = Protection XSS. Memory = Encore mieux (disparu au refresh page).

</details>

---

<details>
<summary><h3>Question 40 : Meilleur stockage pour refresh token ?</h3></summary>

**A)** localStorage
**B)** sessionStorage
**C)** Cookie httpOnly
**D)** Ne pas stocker

**Réponse : C) Cookie httpOnly (idéal) ou A) localStorage (acceptable)**

Cookie httpOnly = Plus sécurisé. localStorage = Plus flexible pour logique client.

</details>

---

## Section 5 : Flow et Utilisation (10 questions)

<details>
<summary><h3>Question 41 : Après login, combien de tokens reçus ?</h3></summary>

**A)** 1 token
**B)** 2 tokens (access + refresh)
**C)** 3 tokens
**D)** Variable

**Réponse : B) 2 tokens (access + refresh)**

Best practice moderne.

</details>

---

<details>
<summary><h3>Question 42 : Comment envoyer JWT dans requête HTTP ?</h3></summary>

**A)** Query parameter : `?token=...`
**B)** Header Authorization : `Bearer <token>`
**C)** Body JSON
**D)** Cookie

**Réponse : B) Header Authorization avec Bearer**

Format standard : `Authorization: Bearer eyJhbG...`

</details>

---

<details>
<summary><h3>Question 43 : Que fait serveur si access token expiré ?</h3></summary>

**A)** Retourne données quand même
**B)** Retourne 401 Unauthorized
**C)** Génère nouveau token auto
**D)** Crash

**Réponse : B) 401 Unauthorized**

Client doit refresh ou re-login.

</details>

---

<details>
<summary><h3>Question 44 : Endpoint typique pour refresh token ?</h3></summary>

**A)** POST /api/login
**B)** POST /api/refresh
**C)** GET /api/token
**D)** POST /api/renew

**Réponse : B) POST /api/refresh**

Convention standard.

</details>

---

<details>
<summary><h3>Question 45 : Si refresh token expiré, que faire ?</h3></summary>

**A)** Générer nouveau refresh
**B)** User doit re-login
**C)** Utiliser access token
**D)** Continuer sans token

**Réponse : B) User doit re-login**

Refresh expiré = Session terminée.

</details>

---

<details>
<summary><h3>Question 46 : JWT doit-il être envoyé en HTTPS ?</h3></summary>

**A)** Non, encodé donc sécurisé
**B)** Oui, obligatoire
**C)** Optionnel
**D)** Seulement en production

**Réponse : B) Oui, HTTPS obligatoire**

HTTP = Token interceptable (man-in-the-middle).

</details>

---

<details>
<summary><h3>Question 47 : Lors logout, que faire côté client ?</h3></summary>

**A)** Rien
**B)** Supprimer tokens (localStorage.clear)
**C)** Garder refresh
**D)** Expirer access seulement

**Réponse : B) Supprimer tous tokens**

Clear localStorage/sessionStorage + clear cookies.

</details>

---

<details>
<summary><h3>Question 48 : Lors logout, que faire côté serveur ?</h3></summary>

**A)** Rien (JWT stateless)
**B)** Invalider refresh token en DB
**C)** Blacklist access token
**D)** B et C (idéal)

**Réponse : D) Invalider refresh + optionnel blacklist access**

</details>

---

<details>
<summary><h3>Question 49 : Peut-on décoder JWT sans secret ?</h3></summary>

**A)** Non, secret nécessaire
**B)** Oui, payload est base64
**C)** Seulement header
**D)** Avec outils spéciaux

**Réponse : B) Oui, n'importe qui peut décoder (base64)**

jwt.io ou `atob(payload)`. Mais ne peut pas VÉRIFIER sans secret.

</details>

---

<details>
<summary><h3>Question 50 : Peut-on modifier JWT et re-signer sans secret serveur ?</h3></summary>

**A)** Oui, facilement
**B)** Oui, avec outils
**C)** Non, secret nécessaire
**D)** Seulement admin

**Réponse : C) Non, impossible sans secret**

C'est tout le principe de la signature cryptographique.

</details>

---

## Section 6 : Sécurité (Bonus - 10 questions)

<details>
<summary><h3>Question 51 : Quelle attaque si secret JWT trop simple ?</h3></summary>

**A)** XSS
**B)** JWT forging (création faux tokens)
**C)** CSRF
**D)** DDoS

**Réponse : B) JWT forging**

Attaquant peut deviner secret et créer tokens admin.

</details>

---

<details>
<summary><h3>Question 52 : JWT protège contre XSS ?</h3></summary>

**A)** Oui, automatiquement
**B)** Non, si localStorage vulnérable
**C)** Seulement avec httpOnly
**D)** B et C

**Réponse : D) Non si localStorage, Oui si httpOnly cookie**

</details>

---

<details>
<summary><h3>Question 53 : Algorithme "none" dans JWT est sécurisé ?</h3></summary>

**A)** Oui
**B)** Non, dangereux (pas de signature)
**C)** Selon contexte
**D)** Oui si HTTPS

**Réponse : B) Non, très dangereux**

`{"alg":"none"}` = Pas de signature = Token modifiable.

</details>

---

<details>
<summary><h3>Question 54 : Que faire si secret JWT compromis ?</h3></summary>

**A)** Ignorer
**B)** Changer secret + invalider tous tokens
**C)** Avertir users
**D)** B et C

**Réponse : D) Changer secret immédiatement + invalider tous tokens + avertir users**

</details>

---

<details>
<summary><h3>Question 55 : JWT doit-il être loggé dans logs serveur ?</h3></summary>

**A)** Oui, pour debug
**B)** Non, jamais
**C)** Seulement en dev
**D)** Oui si anonymisé

**Réponse : B) Non, jamais logger tokens complets**

Logs peuvent être exposés. Logger seulement user ID, pas token.

</details>

---

<details>
<summary><h3>Question 56 : Peut-on avoir plusieurs JWT actifs pour même user ?</h3></summary>

**A)** Non, 1 seul à la fois
**B)** Oui (multi-devices)
**C)** Seulement 2
**D)** Interdit

**Réponse : B) Oui**

User peut être connecté sur phone + laptop + tablet simultanément.

</details>

---

<details>
<summary><h3>Question 57 : Comment implémenter "Remember Me" avec JWT ?</h3></summary>

**A)** Access token long
**B)** Refresh token très long (30 jours)
**C)** Pas possible
**D)** Cookie permanent

**Réponse : B) Refresh token longue durée**

Access reste court, refresh 30 jours si "remember me" coché.

</details>

---

<details>
<summary><h3>Question 58 : JWT contient-il données utilisateur actuelles ?</h3></summary>

**A)** Oui, toujours synchronisé
**B)** Non, snapshot au moment création
**C)** Mise à jour auto
**D)** Selon configuration

**Réponse : B) Non, snapshot au moment création**

Si user change role → token a encore ancien role jusqu'à refresh.

</details>

---

<details>
<summary><h3>Question 59 : Si user change password, ses JWT restent valides ?</h3></summary>

**A)** Non, invalidés auto
**B)** Oui, jusqu'à expiration
**C)** Seulement access
**D)** Selon configuration

**Réponse : B) Oui, JWT stateless**

Problème sécurité. Solution : Invalider refresh tokens en DB après changement password.

</details>

---

<details>
<summary><h3>Question 60 : Faut-il vérifier signature JWT côté client ?</h3></summary>

**A)** Oui, toujours
**B)** Non, pas de secret côté client
**C)** Optionnel
**D)** Seulement en prod

**Réponse : B) Non**

Client ne peut pas vérifier (pas de secret). Seul serveur vérifie.

</details>

---

## Corrigé et Barème

### Calcul Score

**Total questions** : 60

**Barème** :

| Score | Niveau | Commentaire |
|-------|--------|-------------|
| **55-60** | Expert | Excellente maîtrise JWT |
| **45-54** | Avancé | Bonne compréhension |
| **35-44** | Intermédiaire | Connaissances solides |
| **25-34** | Débutant | Bases acquises |
| **< 25** | Novice | Relire le cours |

---

### Répartition Points

| Section | Questions | Points | Thème |
|---------|-----------|--------|-------|
| **1** | 1-10 | 10 | Concepts base |
| **2** | 11-20 | 10 | Structure |
| **3** | 21-30 | 10 | Access/Refresh |
| **4** | 31-40 | 10 | Stockage |
| **5** | 41-50 | 10 | Flow utilisation |
| **6** | 51-60 | 10 | Sécurité |

---

## Tableau Récapitulatif Réponses

### Section 1 : Concepts

| Q | Réponse | Mot-Clé |
|---|---------|---------|
| 1 | B | JSON Web Token |
| 2 | B | 3 parties |
| 3 | C | Point (.) |
| 4 | A | Base64 |
| 5 | B | Jamais password |
| 6 | B | Intégrité |
| 7 | B | Encodé pas encrypté |
| 8 | B | Serveur |
| 9 | B | Stateless |
| 10 | B | RFC 7519 |

---

### Section 2 : Structure

| Q | Réponse | Mot-Clé |
|---|---------|---------|
| 11 | B | alg + typ |
| 12 | C | exp |
| 13 | B | Issued At |
| 14 | A | sub (subject) |
| 15 | B | Symétrique |
| 16 | B | Clé privée |
| 17 | C | 256 bits |
| 18 | B | Audience |
| 19 | C | Non |
| 20 | B | Secondes |

---

### Section 3 : Tokens

| Q | Réponse | Mot-Clé |
|---|---------|---------|
| 21 | B | 15 min - 1h |
| 22 | B | Nouveau access |
| 23 | C | 7-30 jours |
| 24 | B | User ID minimal |
| 25 | B | Oui DB |
| 26 | B | Rotation |
| 27 | B | Refresh |
| 28 | D | Stateless + blacklist |
| 29 | A | Oui si DB |
| 30 | D | Invalider + re-login |

---

### Section 4 : Stockage

| Q | Réponse | Mot-Clé |
|---|---------|---------|
| 31 | A | Oui permanent |
| 32 | B | Non onglet |
| 33 | B | Non httpOnly |
| 34 | C | 5-10 MB |
| 35 | A | 4 KB |
| 36 | B | XSS |
| 37 | A | XSS |
| 38 | C | sameSite |
| 39 | C/D | httpOnly ou Memory |
| 40 | C/A | httpOnly ou localStorage |

---

### Section 5 : Flow

| Q | Réponse | Mot-Clé |
|---|---------|---------|
| 41 | B | 2 tokens |
| 42 | B | Bearer |
| 43 | B | 401 |
| 44 | B | /api/refresh |
| 45 | B | Re-login |
| 46 | B | HTTPS |
| 47 | B | Clear all |
| 48 | D | Invalider + blacklist |
| 49 | B | Oui base64 |
| 50 | C | Non impossible |

---

### Section 6 : Sécurité

| Q | Réponse | Mot-Clé |
|---|---------|---------|
| 51 | B | Forging |
| 52 | D | Dépend stockage |
| 53 | B | Dangereux |
| 54 | D | Changer + invalider |
| 55 | B | Jamais |
| 56 | B | Oui multi-device |
| 57 | B | Refresh long |
| 58 | B | Snapshot |
| 59 | B | Oui valides |
| 60 | B | Non pas secret |

---

## Erreurs Fréquentes

### Top 10 Confusions

| Erreur | Réalité |
|--------|---------|
| "JWT est encrypté" | Non, encodé base64 (lisible) |
| "localStorage est sécurisé" | Non, vulnérable XSS |
| "sessionStorage plus sécurisé" | Aussi vulnérable XSS |
| "JWT ne peut pas être lu" | Si, n'importe qui peut décoder |
| "Access token dure 7 jours" | Non, court (15 min) |
| "Refresh token pour chaque requête" | Non, seulement pour refresh |
| "JWT peut être modifié" | Non sans secret (signature invalide) |
| "Cookies toujours plus sécurisés" | Si httpOnly oui, sinon non |
| "JWT protège contre tout" | Non, juste intégrité |
| "Pas besoin refresh token" | Si, sinon re-login constant |

---

## Cas Pratiques (Mini-Quiz)

<details>
<summary><h3>Cas 1 : User change de rôle</h3></summary>

**Situation** :
- User connecté avec role "user"
- Admin change son role → "admin" dans DB
- Access token encore valide 10 minutes

**Question** : User a-t-il accès admin immédiatement ?

**Réponse : Non**

JWT contient snapshot. Nouveau role effectif après :
- Access token expire (10 min) ET refresh
- OU re-login

</details>

---

<details>
<summary><h3>Cas 2 : Token volé</h3></summary>

**Situation** :
- Attaquant vole access token (XSS)
- Token valide encore 12 minutes

**Question** : Peut-on invalider immédiatement ?

**Réponse : Difficile**

JWT stateless = Pas en DB. Solutions :
- Attendre expiration (12 min max)
- Blacklist token (complexe, nécessite DB check)
- Invalider refresh token (empêche renouvellement)

**Leçon** : Access token court = Important

</details>

---

<details>
<summary><h3>Cas 3 : Multi-devices</h3></summary>

**Situation** :
- User connecté sur laptop (access + refresh)
- User connecté sur phone (access + refresh)
- Logout sur laptop

**Question** : Phone reste connecté ?

**Réponse : Oui (par défaut)**

Chaque device a ses propres tokens. Logout laptop n'affecte pas phone.

**Si vouloir logout partout** : Invalider TOUS refresh tokens user en DB.

</details>

---

## Conclusion Quiz

### Concepts Critiques

**Structure** :
- 3 parties : Header.Payload.Signature
- Base64 encodé (pas encrypté)
- Signature garantit intégrité

**Tokens** :
- Access : Court (15 min), usage constant
- Refresh : Long (7 j), usage rare

**Stockage** :
- localStorage : Permanent, vulnérable XSS
- sessionStorage : Temporaire, vulnérable XSS
- httpOnly cookie : Sécurisé XSS

**Sécurité** :
- Secret fort obligatoire (256 bits)
- HTTPS obligatoire
- Expiration obligatoire
- Pas de données sensibles

**Flow** :
1. Login → 2 tokens
2. Use access (15 min)
3. Expire → Refresh
4. Repeat
5. Logout → Clear

---

**Quiz complet JWT - 60 questions couvrant tous aspects.**


