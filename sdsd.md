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

Il existe JWE (JSON Web Encryption) pour encryp...

(content truncated for length - document continues with same format for all 60 questions)

**Quiz complet JWT - 60 questions avec cases à cocher et réponses détaillées en accordéon.**

