Quiz de Révision - Jours 1, 2 & 3
📋 Instructions d'utilisation
Ce document contient des questions de révision orales organisées par jour et par niveau de difficulté. Posez les questions aux étudiants et utilisez les réponses fournies comme guide d'évaluation.

🌐 JOUR 1 - Fondamentaux des Web Services
Questions Niveau Débutant ⭐
Q1: Qu'est-ce qu'un web service et quel problème résout-il ?
<details>
<summary>Réponse attendue</summary>
Un web service est un système logiciel qui permet à deux applications de communiquer entre elles via un réseau en utilisant des standards ouverts.
Problème résolu : Permettre à des applications écrites dans différents langages (Java, PHP, Python, etc.) de communiquer ensemble sans avoir à tout réécrire.
Exemple concret : Une application météo sur smartphone qui interroge un service météorologique pour obtenir les prévisions. L'app ne mesure rien elle-même, elle consomme un web service.
Les 3 composants :

Provider (fournisseur) : expose les fonctionnalités
Consumer (consommateur) : utilise les fonctionnalités
Contract (contrat) : les règles du jeu

</details>

Q2: Citez les 5 méthodes HTTP principales et leur rôle.
<details>
<summary>Réponse attendue</summary>

GET : Récupérer une ressource (lecture, safe, idempotent)
POST : Créer une nouvelle ressource (ni safe ni idempotent)
PUT : Remplacer complètement une ressource (idempotent)
PATCH : Modifier partiellement une ressource
DELETE : Supprimer une ressource (idempotent)

Règle importante : Le verbe HTTP exprime l'action, pas l'URL.
</details>

Q3: Quelle est la différence entre JSON et XML ? Lequel utilise-t-on aujourd'hui et pourquoi ?
<details>
<summary>Réponse attendue</summary>
JSON :

Format léger et compact (30-50% plus petit que XML)
Facile à lire pour les humains
Natif en JavaScript
80% des nouvelles APIs publiques

XML :

Format plus verbeux avec balises
Validation stricte possible (XSD)
Utilisé dans systèmes legacy, finance, santé
Standard SOAP

Aujourd'hui on préfère JSON pour :

Simplicité et légèreté
Performance supérieure
Adoption universelle

XML reste pour :

Systèmes legacy obligatoires
Domaines réglementés (banque, santé)

</details>

Q4: Expliquez la différence entre une méthode HTTP "safe" et "idempotente".
<details>
<summary>Réponse attendue</summary>
Safe (sûre) :

Ne modifie JAMAIS l'état du serveur
Peut être appelée autant de fois qu'on veut sans danger
Exemple : GET est safe

Idempotente :

Peut être appelée plusieurs fois avec le même résultat final
L'état du serveur sera identique après 1 ou 100 appels
Exemples :

PUT est idempotent (remplacer 10 fois = même résultat)
DELETE est idempotent (supprimer 10 fois = supprimé)
POST N'EST PAS idempotent (10 appels = 10 ressources)



Tableau récapitulatif :

GET : Safe ✅ | Idempotent ✅
POST : Safe ❌ | Idempotent ❌
PUT : Safe ❌ | Idempotent ✅
DELETE : Safe ❌ | Idempotent ✅

</details>

Questions Niveau Intermédiaire ⭐⭐
Q5: Quels codes de statut HTTP utiliseriez-vous pour ces situations ?

Un utilisateur essaie de créer un compte avec un email déjà utilisé
Un token JWT est expiré
Un utilisateur authentifié essaie de supprimer un admin sans avoir les droits
Le serveur a planté avec une exception non gérée

<details>
<summary>Réponse attendue</summary>
422 Unprocessable Entity - Email déjà utilisé

Requête syntaxiquement correcte mais sémantiquement incorrecte
Règle métier violée

401 Unauthorized - Token JWT expiré

Authentification manquante ou invalide
Le client doit se ré-authentifier

403 Forbidden - Droits insuffisants

Utilisateur authentifié mais sans permission
Manque le rôle requis (ex: admin)

500 Internal Server Error - Exception non gérée

Erreur serveur imprévue
NE JAMAIS révéler les détails techniques au client
Inclure correlation_id pour le debugging

Règle clé : 4xx = faute du client | 5xx = faute du serveur
</details>

Q6: Pourquoi utiliser le format ISO 8601 pour les dates ? Donnez un exemple.
<details>
<summary>Réponse attendue</summary>
Raisons d'utiliser ISO 8601 :

Évite l'ambiguïté internationale

02/03/2025 = 2 mars ou 3 février ?
ISO : 2025-03-02 = toujours le 2 mars


Inclut le fuseau horaire

14:30:00Z = 14h30 UTC
14:30:00+02:00 = 14h30 heure de Paris


Tri naturel

Les dates ISO se trient alphabétiquement


Support universel

Tous les langages parsent ISO 8601 nativement



Format complet : 2025-10-24T14:30:00Z

Date : 2025-10-24
Heure : 14:30:00
Z = UTC (Zulu)

</details>

Q7: Qu'est-ce que la RFC 7807 et pourquoi l'utiliser pour les erreurs ?
<details>
<summary>Réponse attendue</summary>
RFC 7807 : "Problem Details for HTTP APIs"
Standard qui définit un format JSON uniforme pour décrire les erreurs HTTP.
Avant RFC 7807 : Chaque API inventait son format d'erreur

API A : { "error": "Bad things" }
API B : { "message": "Nope", "code": 123 }
→ Impossible d'écrire un client robuste

Avec RFC 7807 (structure):

type : URI qui identifie le type d'erreur
title : Titre générique du problème
status : Code HTTP
detail : Explication spécifique
instance : URI de la requête qui a causé l'erreur
Champs custom : errors, correlation_id...

Pourquoi l'utiliser ?

Standardisation entre tous les endpoints
Meilleure expérience développeur
Debugging facilité avec correlation_id
Documentation claire
Parsing uniforme côté client

Content-Type important : application/problem+json
</details>

Questions Niveau Avancé ⭐⭐⭐
Q8: Expliquez la différence entre PUT et PATCH avec un exemple concret.
<details>
<summary>Réponse attendue</summary>
PUT - Remplacement COMPLET :

TOUS les champs doivent être fournis
Remplace complètement la ressource
Idempotent (répéter = même résultat)
Si vous oubliez un champ, il sera supprimé/null

PATCH - Modification PARTIELLE :

Seuls les champs à modifier
Les autres champs restent inchangés
Plus économe en bande passante
Généralement pas idempotent

Exemple concret :
Scénario : Changer juste l'email d'un utilisateur
PUT (obligé de tout renvoyer) :
jsonPUT /users/123
{
  "name": "Marie Dubois",
  "email": "nouveau@example.com",  // ce qu'on veut changer
  "phone": "+33612345678",
  "address": "10 rue...",
  "bio": "..."
  // tous les autres champs obligatoires
}
PATCH (seulement ce qui change) :
jsonPATCH /users/123
{
  "email": "nouveau@example.com"
}
Quand utiliser quoi ?

PUT : Formulaire complet avec tous les champs
PATCH : Toggle, changement d'un seul champ

</details>

Q9: Expliquez le principe "Stateless" de REST. Pourquoi est-ce crucial pour la scalabilité ?
<details>
<summary>Réponse attendue</summary>
Définition Stateless :
Chaque requête du client doit contenir TOUTES les informations nécessaires pour la traiter. Le serveur ne stocke AUCUN contexte de session entre les requêtes.
Approche Stateful (ancienne - problématique) :

Client se connecte → serveur crée session en mémoire
Session stockée sur UN serveur spécifique
Nécessite "sticky sessions" (toujours parler au même serveur)
Si serveur crash → sessions perdues
Scaling difficile

Approche Stateless (moderne - REST) :

Client envoie JWT token dans chaque requête
Serveur décode le token → sait qui est le client
Aucune mémoire de session
N'importe quel serveur peut traiter la requête

Pourquoi crucial pour la scalabilité ?

Scaling horizontal trivial

Ajoutez 100 serveurs, ça fonctionne immédiatement
Pas de synchronisation de sessions
Pas de sticky sessions


Résilience améliorée

Serveur crash → aucune session perdue
Pas de point de défaillance unique


Microservices friendly

Tous les services valident le JWT indépendamment
Pas de session partagée


Infrastructure simplifiée

Pas besoin de Redis pour les sessions
Moins de latence réseau



Exemple concret : Netflix gère 230M+ utilisateurs avec stateless

Peuvent déployer 100 fois par jour sans casser les sessions
N'importe quel datacenter peut traiter n'importe quelle requête

Trade-off : Token plus gros qu'un cookie, mais bénéfices >> coûts
</details>

🎨 JOUR 2 - Introduction aux API REST
Questions Niveau Débutant ⭐
Q10: Citez au moins 4 des 6 contraintes architecturales REST.
<details>
<summary>Réponse attendue</summary>
Les 6 contraintes REST :

Client-Serveur

Séparation des responsabilités
Évolution indépendante


Stateless (Sans état)

Chaque requête contient tout le contexte
Pas de session serveur


Cacheable

Réponses indiquent si elles peuvent être cachées
Code 304 Not Modified


Interface uniforme

URIs pour identifier les ressources
Manipulation via représentations
Messages auto-descriptifs


Système en couches

Client ne sait pas s'il parle au serveur final ou à un proxy
Permet CDN, load balancer transparent


Code on demand (optionnel)

Serveur peut envoyer du code exécutable



Les 2 plus importantes : Stateless + Interface uniforme
</details>

Q11: Quelle est la différence entre une collection et une ressource unique dans une URL REST ?
<details>
<summary>Réponse attendue</summary>
Collection (pluriel) :

Ensemble de ressources
Toujours au pluriel
Sans identifiant spécifique
Exemples :

GET /users → liste tous les utilisateurs
POST /users → crée un utilisateur
GET /events → liste les événements



Ressource unique :

UNE ressource spécifique
Avec identifiant unique
Exemples :

GET /users/123 → récupère l'utilisateur 123
PATCH /users/123 → modifie l'utilisateur 123
DELETE /events/550e8400 → supprime l'événement



Ressources imbriquées :

GET /users/123/orders → commandes de l'utilisateur 123
GET /events/789/attendees → participants de l'événement

Erreur fréquente à éviter :

❌ GET /user/123 (singulier)
✅ GET /users/123 (pluriel même pour 1 ressource)

Règle d'or : Toujours pluriel pour la collection !
</details>

Q12: Pourquoi ne devrait-on JAMAIS renvoyer un tableau nu au top-level d'une réponse JSON ?
<details>
<summary>Réponse attendue</summary>
Problèmes d'un tableau nu :

Pas de métadonnées possibles

Impossible d'ajouter pagination, total, etc.


Breaking change futur

Ajouter des métadonnées casse tous les clients
response[0] ne pointe plus sur le premier élément


Pas d'extensibilité

Impossible d'ajouter des champs sans breaking change



Solution : Enveloppe objet
json{
  "data": [...],
  "meta": {
    "total": 156,
    "page": 1,
    "per_page": 20
  },
  "links": {
    "next": "/products?page=2"
  }
}
```

**Avantages :**
- Métadonnées immédiates
- Extensible sans breaking change
- Structure cohérente partout
</details>

---

### Questions Niveau Intermédiaire ⭐⭐

**Q13: Expliquez la différence entre pagination offset-based et cursor-based.**

<details>
<summary>Réponse attendue</summary>

## **OFFSET-BASED (classique)**

**Principe :**
- `GET /products?page=1&per_page=20` (éléments 1-20)
- `GET /products?offset=20&limit=20` (sauter 20, prendre 20)

**Avantages :**
- Simple à comprendre
- Permet de sauter directement à n'importe quelle page
- Facile de calculer le nombre total de pages

**Inconvénients :**
- Performance dégradée sur grandes tables (offset 10000 est lent)
- Problème de cohérence si données ajoutées/supprimées pendant pagination

**Exemple du problème :**
1. Client demande page 1 (éléments 1-20)
2. 100 nouveaux produits ajoutés en tête
3. Client demande page 2
   → Obtient des produits déjà vus ! 😱

---

## **CURSOR-BASED (moderne)**

**Principe :**
- `GET /products?cursor=eyJpZCI6MTIzfQ==&limit=20`
- Cursor = pointeur opaque (base64) vers la position

**Avantages :**
- Performance constante (utilise les index)
- Cohérence garantie même si données changent
- Parfait pour feeds infinis (scroll Instagram/Twitter)

**Inconvénients :**
- Impossible de sauter à la page 5 directement
- Plus complexe à implémenter
- Pas de "total de pages"

---

## **QUAND UTILISER QUOI ?**

**Offset-based pour :**
- Backoffice admin avec pages numérotées
- Tableaux avec navigation claire
- Petits datasets (<10k éléments)

**Cursor-based pour :**
- Feeds sociaux (scroll infini)
- Messagerie/chat
- Grands datasets (>10k éléments)
- APIs publiques haute performance
</details>

---

**Q14: Quelles sont les conventions de nommage pour les URLs REST ?**

<details>
<summary>Réponse attendue</summary>

## **RÈGLES FONDAMENTALES**

**1. Toujours PLURIEL**
- ✅ `GET /users/123`
- ❌ `GET /user/123`

**2. NOMS, pas VERBES**
- ✅ `GET /users`, `POST /orders`
- ❌ `GET /getUsers`, `POST /createOrder`
- Le verbe est déjà dans la méthode HTTP !

**3. Kebab-case en minuscules**
- ✅ `/users/123/order-history`
- ❌ `/users/123/orderHistory` (camelCase)
- ❌ `/users/123/order_history` (snake_case)

**4. Hiérarchie max 2-3 niveaux**
- ✅ `GET /users/123/orders`
- ✅ `GET /orders/456/items`
- ❌ `GET /users/123/orders/456/items/789/options/012` (trop profond)

**5. Actions complexes**

*Approche 1 : Sous-ressource (préférée)*
- ✅ `POST /orders/123/payment` (créer un paiement)
- ✅ `POST /users/456/activation`

*Approche 2 : Verbe explicite (dernier recours)*
- ⚠️ `POST /orders/123/cancel`
- ⚠️ `POST /users/456/reset-password`

**Pattern SmartCity :**
```
GET    /events
POST   /events
GET    /events/{id}
PATCH  /events/{id}
DELETE /events/{id}
POST   /events/{id}/register
GET    /events?type=concert&city=Paris&sort=-date
</details>

Questions Niveau Avancé ⭐⭐⭐
Q15: Comment structurer une réponse de collection avec pagination ?
<details>
<summary>Réponse attendue</summary>
STRUCTURE COMPLÈTE RECOMMANDÉE
3 sections principales :
1. Section data (obligatoire)

Toujours un tableau
Contient les ressources de la page actuelle
Même vide : "data": []

2. Section meta (fortement recommandée)
Métadonnées de pagination :

total : Nombre TOTAL d'éléments (toutes pages)
count : Nombre d'éléments dans CETTE page
page : Numéro de page actuelle
per_page : Éléments par page
total_pages : Nombre total de pages
has_more : Y a-t-il une page suivante ?

3. Section links (recommandée)
Liens HATEOAS pour la navigation :

self : Page actuelle
first : Première page
prev : Page précédente (null si première)
next : Page suivante (null si dernière)
last : Dernière page

Exemple complet :
json{
  "data": [
    {"id": "550e8400", "title": "Concert de Jazz", ...}
  ],
  "meta": {
    "total": 156,
    "count": 20,
    "page": 1,
    "per_page": 20,
    "total_pages": 8,
    "has_more": true
  },
  "links": {
    "self": "/events?page=1&per_page=20",
    "first": "/events?page=1&per_page=20",
    "prev": null,
    "next": "/events?page=2&per_page=20",
    "last": "/events?page=8&per_page=20"
  }
}
Pour cursor-based :
json{
  "data": [...],
  "meta": {
    "count": 20,
    "has_more": true,
    "next_cursor": "eyJpZCI6MTQzfQ=="
  },
  "links": {
    "next": "/events?cursor=eyJpZCI6MTQzfQ==&limit=20"
  }
}
```

**Cohérence = 🔑** Utilisez la même structure partout !
</details>

---

## 🚀 JOUR 3 - Développement d'API REST Avancé

### Questions Niveau Débutant ⭐

**Q16: Quand devrait-on créer une nouvelle version d'API ? Donnez 3 exemples de breaking changes.**

<details>
<summary>Réponse attendue</summary>

On crée une nouvelle version uniquement pour des **breaking changes** (changements incompatibles).

## **BREAKING CHANGES (nécessitent v2)**

**1. Renommer un champ**
- v1 : `"customer_name": "Marie"`
- v2 : `"customer": {"name": "Marie"}`
- Les clients qui lisent `customer_name` ne le trouveront plus

**2. Changer le type d'un champ**
- v1 : `"price": 99.99` (number)
- v2 : `"price": "99.99 EUR"` (string)
- Les clients qui parsent un number crasheront

**3. Supprimer un champ**
- v1 : `{"id": 123, "legacy_field": "data"}`
- v2 : `{"id": 123}` (legacy_field supprimé)
- Les clients qui dépendent de ce champ cassent

**4. Rendre un champ optionnel obligatoire**
- v1 : email optionnel
- v2 : email obligatoire
- Les clients qui n'envoient pas d'email auront des 422

**5. Changer la structure**
- v1 : `"items": ["Pizza", "Pasta"]`
- v2 : `"items": [{"name": "Pizza", "price": 12.50}]`
- Les clients qui itèrent sur strings crasheront

## **NON-BREAKING CHANGES (pas besoin de v2)**

**1. Ajouter un nouveau champ optionnel**
- Les clients l'ignorent simplement

**2. Ajouter un nouvel endpoint**
- Pas d'impact sur les clients existants

**3. Ajouter une valeur enum**
- `"status": "pending" | "completed" | "cancelled"` (nouveau)

**4. Rendre obligatoire → optionnel**
- Les clients qui l'envoient toujours fonctionnent

**Règle d'or :** En cas de doute → nouvelle version !
</details>

---

**Q17: Quelles sont les 3 stratégies principales de versioning d'API ? Laquelle est la plus courante ?**

<details>
<summary>Réponse attendue</summary>

## **1. VERSIONING DANS L'URI** 🏆 (90% des APIs)

**Exemple :** `https://api.example.com/v1/users`

**Avantages :**
- Visible et explicite
- Cache-friendly
- Simple à tester
- Compatible navigateur

**Inconvénients :**
- Pas très "RESTful pur"
- URLs multiples pour même ressource

**Qui l'utilise :** Google, Twitter, Facebook, Amazon, Netflix, Stripe...

---

## **2. VERSIONING PAR HEADER**

**Exemple :**
```
GET /users/123
Accept: application/vnd.example.v2+json
```

**Avantages :**
- RESTful pur
- URL unique

**Inconvénients :**
- Moins visible
- Difficile à tester dans navigateur
- Complexité du caching

**Qui l'utilise :** GitHub API (partiellement)

---

## **3. VERSIONING PAR QUERY PARAMETER**

**Exemple :** `GET /users/123?version=2`

**Avantages :**
- Visible dans l'URL
- Facile à tester

**Inconvénients :**
- Mélange version et filtres
- Moins propre
- Rarement utilisé

---

## **RECOMMANDATION**

**Pour 90% des cas : VERSIONING URI**
```
GET /api/v1/events
GET /api/v2/events
C'est le standard de l'industrie. Simple, clair, tout le monde comprend.
</details>

Q18: À quoi sert OpenAPI (Swagger) ? Citez 3 bénéfices concrets.
<details>
<summary>Réponse attendue</summary>
OpenAPI Specification (OAS) = Standard pour décrire des APIs REST en YAML/JSON machine-readable.
3 BÉNÉFICES CONCRETS
1. Documentation interactive automatique 📚
Swagger UI génère une interface web où les développeurs peuvent :

Voir tous les endpoints
Voir les schémas avec exemples
Tester les endpoints directement (Try it out)
Copier des exemples

Impact :

Développeurs explorent l'API immédiatement
Économise des heures de documentation manuelle
Toujours à jour


2. Validation automatique ✅
Bibliothèques comme express-openapi-validator valident automatiquement :

Format des requêtes
Types de données
Champs obligatoires
Réponses (en dev)

Impact :

Plus besoin de coder la validation manuellement
Détecte les bugs avant production
Validation ultra-rapide


3. Génération automatique de clients 🤖
À partir d'OpenAPI, générez des SDKs dans 50+ langages :

TypeScript avec auto-complétion
Python avec types
Java, Go, Swift...

Impact :

Clients ont un SDK type-safe immédiatement
Regénération à chaque changement d'API
Support multi-langages


BONUS :

Contrat entre équipes (Frontend/Backend)
Mocking d'API pour développement parallèle
Tests de contrat automatisés

OpenAPI transforme votre API en contrat explicite, testable et documenté.
</details>

Questions Niveau Intermédiaire ⭐⭐
Q19: Qu'est-ce que HATEOAS ? Donnez un exemple concret.
<details>
<summary>Réponse attendue</summary>
HATEOAS = Hypermedia As The Engine Of Application State
Principe : Une API devrait être découvrable. Le serveur envoie des liens vers les actions possibles, et le client suit ces liens.
Analogie : Comme Wikipedia - vous cliquez sur des liens, vous ne connaissez pas toutes les URLs à l'avance.

EXEMPLE CONCRET
Sans HATEOAS :
json{
  "id": "550e8400",
  "title": "Concert de Jazz",
  "status": "open",
  "current_attendees": 45,
  "max_attendees": 200
}
Le client doit connaître la logique :

"Si open ET places disponibles → POST /events/{id}/register"
Logique codée en dur côté client


Avec HATEOAS :
json{
  "id": "550e8400",
  "title": "Concert de Jazz",
  "status": "open",
  "current_attendees": 45,
  "max_attendees": 200,
  "_links": {
    "self": "/events/550e8400",
    "attendees": "/events/550e8400/attendees",
    "register": {
      "href": "/events/550e8400/register",
      "method": "POST",
      "title": "S'inscrire"
    },
    "update": {
      "href": "/events/550e8400",
      "method": "PATCH",
      "title": "Modifier"
    }
  }
}
Le serveur dit : "Voici les actions possibles"

Si événement COMPLET :
json{
  "status": "full",
  "_links": {
    "self": "/events/550e8400",
    "attendees": "/events/550e8400/attendees"
    // Pas de "register" → inscription impossible
  }
}

AVANTAGES

Découvrabilité : Client explore l'API en suivant les liens
Logique métier côté serveur : Pas de code en dur côté client
Évolution sans breaking change : Nouveaux liens ignorés par anciens clients

INCONVÉNIENTS

Verbosité (réponses plus grosses)
Complexité d'implémentation
Adoption faible (peu d'APIs l'implémentent complètement)

APPROCHE PRAGMATIQUE
Niveau minimal : Au moins self et liens vers ressources liées
json{
  "_links": {
    "self": "/events/550e8400",
    "attendees": "/events/550e8400/attendees"
  }
}
Pas besoin d'aller full HAL/JSON:API pour avoir les bénéfices !
</details>

Q20: Comment gérer la dépréciation d'une version d'API ? Décrivez le processus.
<details>
<summary>Réponse attendue</summary>
TIMELINE RECOMMANDÉE (12 MOIS)
T-12 mois : Annonce publique 📢

Blog officiel
Newsletter développeurs
Documentation avec banner
Changelog avec date de sunset

Contenu :

Date de retrait
Raisons du changement
Guide de migration
Timeline détaillée


T-6 mois : Headers de dépréciation ⚠️
Ajouter dans toutes les réponses v1 :
httpDeprecation: Sat, 01 Jan 2025 00:00:00 GMT
Sunset: Mon, 31 Dec 2025 23:59:59 GMT
Link: <https://api.example.com/docs/migration>; rel="deprecation"
Impact : Clients bien conçus loggent ces headers automatiquement

T-3 mois : Contact direct 📞

Identifier les gros clients encore sur v1
Email personnalisé aux top 100
Offrir support de migration gratuit
Session 1-on-1 si nécessaire


T-1 mois : Derniers rappels 🚨

Email hebdomadaire aux clients v1
Banner rouge dans documentation
Message dans les réponses API (optionnel)


T-0 : Jour J - Sunset ☠️
Arrêter v1 et renvoyer 410 Gone 