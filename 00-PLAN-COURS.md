# Plan de Cours : Autorisation et Sécurité API

**Formation complète - Authentification, Autorisation, Protection**

---

## Vue d'Ensemble

Ce dossier contient 10 documents sélectionnés pour un cours complet et cohérent sur l'autorisation et la sécurité des APIs.

**Durée totale** : 4-6 heures (modulable)

**Public** : Développeurs, étudiants en informatique

**Objectifs** :
- Comprendre différence Auth vs Authz
- Maîtriser concepts RBAC, ABAC, RLS
- Identifier vulnérabilités API
- Choisir solutions appropriées
- Pratiquer attaques et défenses

---

## Documents Inclus (Ordre Pédagogique)

### Partie 1 : Introduction et Concepts (1h)

**01-INTRODUCTION-RAPIDE.md** (15 min)
- Vue d'ensemble rapide
- Pourquoi l'autorisation est importante
- Comparaison solutions

**02-FAQ-QUESTIONS-REPONSES.md** (30 min)
- Questions fréquentes
- Format accordéon interactif
- Clarifications concepts

**03-CONCEPTS-RBAC-ABAC.md** (15 min)
- RBAC (Role-Based Access Control)
- ABAC (Attribute-Based Access Control)
- 20 études de cas réels

---

### Partie 2 : Sécurité et Vulnérabilités (2h)

**04-DANGERS-CLIENT-VS-SERVEUR.md** (30 min)
- Protection côté client (dangereuse)
- Protection côté serveur (acceptable)
- Protection database RLS (maximale)
- Exemples hacking concrets

**05-GUIDE-HACKING-API.md** (30 min)
- Tableau exploits courants
- IDOR, Mass Assignment, Price Manipulation
- Cas réels de failles (2021-2023)
- Protections recommandées

**06-EXERCICES-NMAP-SQLMAP.md** (1h)
- Installation nmap et sqlmap
- 7 exercices pratiques
- API vulnérable fournie
- Reconnaissance → Exploitation

---

### Partie 3 : Exercices Pratiques (1h30)

**07-EXERCICES-PRATIQUES.md** (1h)
- 5 exercices progressifs
- Tableaux permissions à compléter
- Cas réels à analyser
- Démonstration Supabase live

**08-COURS-JWT.md** (30 min)
- Structure JWT complète
- Access vs Refresh tokens
- localStorage vs sessionStorage vs cookies
- Flow authentification complet

---

### Partie 4 : Évaluation et Conclusion (30 min)

**09-QUIZ-JWT.md** (20 min)
- 60 questions JWT
- Format accordéon
- Auto-évaluation

**10-CONCLUSION-RECOMMANDATIONS.md** (10 min)
- Recommandations pour ton projet
- Solutions par cas d'usage
- Action plan

---

## Ordre de Lecture Recommandé

### Parcours Court (2h - Essentiel)

Pour cours ce matin, focus sur :

1. **01-INTRODUCTION-RAPIDE** (15 min)
   - Vue d'ensemble solutions

2. **04-DANGERS-CLIENT-VS-SERVEUR** (30 min)
   - Démonstrations hacking
   - Pourquoi RLS nécessaire

3. **05-GUIDE-HACKING-API** (20 min)
   - Tableaux exploits
   - Cas réels

4. **07-EXERCICES-PRATIQUES** (45 min)
   - Exercices interactifs
   - Démo Supabase live

5. **08-COURS-JWT** (10 min)
   - Concepts JWT essentiels

**Total : 2h** (parfait pour cours matinal)

---

### Parcours Complet (6h - Formation)

Tous les documents dans l'ordre 01 à 10

**Matin (3h)** :
- Documents 01-05 (théorie + sécurité)
- Pause 15 min

**Après-midi (3h)** :
- Documents 06-07 (exercices pratiques)
- Document 08 (JWT)
- Documents 09-10 (quiz + conclusion)

---

## Structure Pédagogique

### Progression

```
Introduction (Comprendre)
    ↓
Concepts (RBAC, ABAC)
    ↓
Dangers (Client vs Serveur)
    ↓
Vulnérabilités (Hacking)
    ↓
Pratique (nmap, sqlmap)
    ↓
Exercices (Hands-on)
    ↓
JWT (Protocole)
    ↓
Évaluation (Quiz)
    ↓
Recommandations (Action)
```

---

## Matériel Requis

### Pour Enseignant

**Logiciels** :
- Node.js (pour API vulnérable)
- nmap
- sqlmap
- Compte Supabase (démo)
- Projecteur/écran

**Préparation** :
- Tester API vulnérable (doc 06)
- Préparer démo Supabase (doc 07)
- Vérifier outils installés

---

### Pour Étudiants

**Minimum** :
- Navigateur (DevTools)
- Compte Supabase gratuit (optionnel)

**Idéal** :
- Node.js installé
- nmap + sqlmap
- Python 3
- Postman

---

## Timing Suggéré

### Cours 2h (Ce Matin)

| Temps | Document | Activité |
|-------|----------|----------|
| **08h00-08h15** | 01 | Introduction concepts |
| **08h15-08h45** | 04 | Démo hacking client/serveur |
| **08h45-09h05** | 05 | Tableaux exploits API |
| **09h05-09h15** | Pause | - |
| **09h15-10h00** | 07 | Exercices pratiques groupes |
| **10h00-10h10** | 10 | Conclusion + questions |

---

### Cours 4h (Complet)

| Temps | Documents | Activité |
|-------|-----------|----------|
| **08h00-09h00** | 01-03 | Théorie + concepts |
| **09h00-10h00** | 04-05 | Sécurité + vulnérabilités |
| **10h00-10h15** | Pause | - |
| **10h15-11h15** | 06-07 | Exercices pratiques |
| **11h15-12h00** | 08-10 | JWT + Quiz + Conclusion |

---

## Points Forts de Cette Sélection

### Cohérence

**Flow logique** :
1. Vue d'ensemble (pourquoi c'est important)
2. Concepts théoriques (RBAC, ABAC)
3. Dangers concrets (hacking démos)
4. Vulnérabilités détaillées (exploits)
5. Pratique offensive (nmap, sqlmap)
6. Pratique défensive (exercices)
7. Protocoles (JWT)
8. Évaluation (quiz)
9. Action (recommandations)

---

### Pédagogique

**Variété formats** :
- Texte explicatif
- Tableaux comparatifs
- Diagrammes Mermaid
- Code exemples
- Exercices pratiques
- Démos live
- Quiz interactif
- Cas réels

**Progression** :
- Débutant → Avancé
- Théorie → Pratique
- Comprendre → Faire

---

### Pratique

**Exercices hands-on** :
- API vulnérable fournie (doc 06)
- Scripts ready-to-use
- Commandes exactes
- Résultats attendus

**Démos live** :
- Hacking console (30s)
- SQL injection (10 min)
- Supabase RLS (15 min)

---

## Adaptation Selon Temps

### Si 1h Seulement

**Documents essentiels** :
- 04 (Dangers) : 20 min
- 05 (Hacking) : 20 min
- 07 (Exercices) : 20 min

**Messages clés** :
- Protection client = dangereux
- RLS = solution
- Démo rapide

---

### Si 3h Disponibles

**Parcours recommandé** :
- 01, 04, 05, 06, 07, 10

**Répartition** :
- 1h : Théorie (01, 04, 05)
- 1h30 : Pratique (06, 07)
- 30 min : Conclusion (10)

---

### Si 6h (Formation Complète)

**Tous documents 01-10**

**Avec pauses** :
- Pause 15 min après 1h30
- Pause déjeuner 1h après 3h
- Pause 10 min après 1h

---

## Objectifs Apprentissage

### À la Fin du Cours, Étudiants Peuvent :

**Comprendre** :
- Différence authentification vs autorisation
- RBAC vs ABAC vs RLS
- Dangers protection client
- Vulnérabilités API courantes

**Identifier** :
- Failles de sécurité
- Exploits potentiels
- Solutions appropriées

**Faire** :
- Scanner API (nmap)
- Tester injections (sqlmap)
- Créer policies RLS
- Choisir stack sécurisée

**Décider** :
- Quelle solution pour quel projet
- Supabase vs Firebase vs autres
- Protections minimales requises

---

## Ressources Complémentaires

### Liens Externes

**Documentation** :
- [Supabase RLS Guide](https://supabase.com/docs/guides/auth/row-level-security)
- [OWASP API Security Top 10](https://owasp.org/www-project-api-security/)
- [JWT.io](https://jwt.io) - Décodeur JWT

**Outils** :
- [nmap.org](https://nmap.org)
- [sqlmap.org](https://sqlmap.org)
- [Burp Suite](https://portswigger.net/burp)

**Pratique** :
- [HackTheBox](https://hackthebox.com)
- [TryHackMe](https://tryhackme.com)
- [PortSwigger Academy](https://portswigger.net/web-security)

---

## Checklist Préparation Cours

### Avant le Cours

**Technique** :
- [ ] Tester API vulnérable (doc 06)
- [ ] Installer nmap, sqlmap
- [ ] Créer compte Supabase démo
- [ ] Préparer exemples RLS
- [ ] Tester toutes commandes

**Pédagogique** :
- [ ] Imprimer exercices (doc 07)
- [ ] Préparer slides résumé
- [ ] Quiz papier (optionnel)
- [ ] Exemples code projetés

**Logistique** :
- [ ] Vérifier projecteur
- [ ] WiFi fonctionnel
- [ ] Tableau blanc/marqueurs
- [ ] Chronomètre (timing)

---

## Évaluation Étudiants

### Méthodes

**Pendant cours** :
- Participation exercices
- Questions/réponses
- Démos pratiques

**Fin cours** :
- Quiz JWT (doc 09) : 60 questions
- Exercice pratique : Scanner API
- Projet : Implémenter RLS

**Critères** :
- Compréhension concepts
- Capacité identifier failles
- Choix solutions appropriées

---

## Adaptation par Public

### Développeurs Junior

**Focus** :
- Documents 01, 02, 04, 07
- Moins de technique offensive
- Plus d'exercices guidés

**Éviter** :
- sqlmap avancé
- Concepts ABAC complexes

---

### Développeurs Senior

**Focus** :
- Documents 03, 05, 06
- Techniques avancées
- Études de cas complexes

**Ajouter** :
- Discussion architecture
- Trade-offs solutions

---

### Étudiants Sécurité

**Focus** :
- Documents 05, 06
- Maximum pratique offensive
- nmap, sqlmap, Burp Suite

**Ajouter** :
- Challenge CTF
- Compétition temps

---

## Conclusion

### Documents Sélectionnés

**10 documents essentiels** couvrant :
- Introduction et FAQ
- Concepts autorisation
- Sécurité et dangers
- Vulnérabilités API
- Exercices pratiques (nmap, sqlmap)
- Protocoles (JWT)
- Quiz évaluation
- Recommandations

**Ordre cohérent** :
- Progression logique débutant → avancé
- Théorie → Pratique
- Comprendre → Faire

**Prêt pour cours ce matin** :
- Documents 01, 04, 05, 07 (2h)
- Exercices interactifs
- Démos live

**Bon cours !**

---

**Dossier COURS-AUTORISATION prêt à l'emploi.**

