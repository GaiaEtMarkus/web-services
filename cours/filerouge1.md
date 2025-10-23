🚀 PROJET FIL ROUGE - DEMI-JOURNÉE 1 (J1 Après-midi)
SmartCity API Platform - Kickoff & Fondations
Durée : 3h30 (13h30-17h00)
Contexte : Vous avez eu ce matin 3h30 de théorie sur REST, design d'APIs, et gestion d'erreurs.

📋 CONTEXTE & OBJECTIFS (13h30-13h50) - 20 min
Vue d'ensemble du projet
Vous allez construire SmartCity, une plateforme d'APIs REST pour une ville intelligente sur 8 demi-journées.
Les 5 équipes

Équipe FRONT-END (3-4 personnes) - SPA qui consomme les APIs
Équipe AUTH (3-4 personnes) - Service d'authentification (obligatoire)
Équipes SERVICES (3 équipes de 3-4 personnes) - Chacune choisit SON domaine métier

🎯 Objectif de CETTE demi-journée (réaliste)
À 17h00, chaque équipe doit avoir :

✅ Choisi sa stack technique
✅ Rédigé un contrat OpenAPI minimal (2-3 endpoints max)
✅ Initialisé un projet Git avec structure de base
✅ Créé un README avec le plan de route

CE QUI N'EST PAS ATTENDU AUJOURD'HUI :

❌ Code fonctionnel complet
❌ Base de données connectée
❌ Tests exhaustifs
❌ Mock en ligne (on fera ça J2)


🎮 PHASE 1 : CONSTITUTION & CHOIX (13h50-14h20) - 30 min
Réunion plénière - Tous ensemble (15 min)
1. Attribution des rôles (5 min)
Chaque équipe désigne pour ce sprint :

Product Owner : Interface avec les autres équipes, priorise
Tech Lead : Décisions techniques, architecture
Dev 1 & 2 : Implémentation
(Les rôles tourneront à chaque sprint)

2. Conventions transverses OBLIGATOIRES (10 min)
À noter par TOUS - Ce sont des règles non négociables :
yaml# NAMING
- URIs: toujours au pluriel → /events, /users, /transports
- Format: kebab-case → ?page-size=20 OU ?limit=20
- IDs: UUID v4
- Dates: ISO 8601 → "2025-06-21T14:30:00Z"

# PAGINATION (standard à respecter)
Request: GET /resources?page=1&limit=20
Response:
{
  "data": [...],
  "page": 1,
  "limit": 20,
  "total": 156
}

# ERREURS (RFC 7807 - vu ce matin)
Content-Type: application/problem+json
{
  "type": "https://smartcity.api/errors/validation-error",
  "title": "Validation Failed",
  "status": 422,
  "detail": "Message détaillé de l'erreur",
  "correlationId": "uuid-pour-traçabilité"
}

# HEADERS OBLIGATOIRES
X-Correlation-Id: uuid  (pour tracer les requêtes)
Content-Type: application/json
Travail en équipes (15 min)
ÉQUIPES BACK-END : Choisissez votre domaine
Voici 8 domaines possibles (choisissez-en 1 par équipe) :
1. EVENTS (Événements culturels)

Gérer concerts, festivals, expositions, conférences
Endpoints futurs :

GET /events - Liste avec filtres (date, type, lieu)
GET /events/{id} - Détails d'un événement
POST /events - Créer un événement (authentifié)
PUT /events/{id} - Modifier
DELETE /events/{id} - Supprimer
POST /events/{id}/register - S'inscrire à un événement
GET /events/{id}/attendees - Liste des inscrits
GET /events/calendar - Vue calendrier
GET /events/popular - Événements populaires



2. TRANSPORT (Mobilité urbaine)

Lignes de bus/métro/tram, horaires, itinéraires
Endpoints futurs :

GET /lines - Liste des lignes de transport
GET /lines/{id} - Détails d'une ligne
GET /lines/{id}/stops - Arrêts d'une ligne
GET /stops - Liste des arrêts
GET /stops/{id}/schedules - Horaires d'un arrêt
POST /itineraries/calculate - Calculer un itinéraire
GET /traffic/alerts - Alertes trafic en cours
GET /lines/{id}/realtime - Position temps réel
GET /stops/nearby?lat=X&lng=Y - Arrêts à proximité



3. TROC (Échange entre citoyens)

Plateforme d'échange d'objets entre habitants
Endpoints futurs :

GET /items - Liste des objets disponibles
GET /items/{id} - Détails d'un objet
POST /items - Proposer un objet (authentifié)
PUT /items/{id} - Modifier
DELETE /items/{id} - Retirer
POST /items/{id}/propose - Proposer un échange
GET /exchanges - Mes échanges en cours
POST /exchanges/{id}/accept - Accepter une proposition
GET /items/categories - Catégories disponibles



4. DÉPANNAGE (Services d'urgence)

Plombiers, électriciens, serruriers disponibles
Endpoints futurs :

GET /services - Types de services disponibles
GET /providers - Liste des prestataires
GET /providers/{id} - Profil d'un prestataire
POST /appointments - Prendre rendez-vous (authentifié)
GET /appointments - Mes rendez-vous
PUT /appointments/{id} - Modifier/annuler
POST /providers/{id}/reviews - Laisser un avis
GET /providers/available?service=X&date=Y - Dispo
GET /appointments/{id}/status - Suivi d'intervention



5. ALERTE INCENDIE (Signalements citoyens)

Signaler incidents, suivre leur traitement
Endpoints futurs :

GET /alerts - Liste des alertes
GET /alerts/{id} - Détails d'une alerte
POST /alerts - Créer une alerte (authentifié)
PUT /alerts/{id}/status - Mettre à jour le statut (admin)
GET /alerts/map - Alertes sur une carte
POST /alerts/{id}/comments - Commenter
GET /alerts/nearby?lat=X&lng=Y - Alertes à proximité
GET /alerts/statistics - Stats par type/zone
POST /alerts/{id}/close - Clôturer (admin)



6. MÉTÉO (Adapter API externe)

Récupérer et normaliser données météo
Endpoints futurs :

GET /weather/current?city=X - Météo actuelle
GET /weather/forecast?city=X&days=7 - Prévisions
GET /weather/alerts?city=X - Alertes météo
GET /weather/history?city=X&date=Y - Historique
GET /cities - Villes disponibles
GET /weather/air-quality?city=X - Qualité de l'air
POST /weather/subscribe - S'abonner aux alertes
GET /weather/compare?cities=X,Y,Z - Comparer villes



7. PARKING (Stationnement intelligent)

Places disponibles, réservations, tarifs
Endpoints futurs :

GET /parkings - Liste des parkings
GET /parkings/{id} - Détails d'un parking
GET /parkings/{id}/availability - Places disponibles
POST /parkings/{id}/reserve - Réserver (authentifié)
GET /reservations - Mes réservations
DELETE /reservations/{id} - Annuler
GET /parkings/nearby?lat=X&lng=Y - Parkings proches
GET /parkings/{id}/prices - Grille tarifaire
POST /parkings/{id}/checkin - Arrivée
POST /parkings/{id}/checkout - Sortie et paiement



8. BIBLIOTHÈQUE (Médiathèque municipale)

Catalogue, emprunts, réservations
Endpoints futurs :

GET /books - Catalogue avec recherche
GET /books/{id} - Détails d'un livre
POST /loans - Emprunter (authentifié)
GET /loans - Mes emprunts en cours
PUT /loans/{id}/extend - Prolonger
POST /books/{id}/reserve - Réserver si emprunté
GET /loans/history - Historique d'emprunts
POST /books/{id}/review - Laisser un avis
GET /books/recommendations - Recommandations



🎯 Action : Chaque équipe back-end choisit 1 domaine (pas de doublon !)
ÉQUIPE FRONT-END : Définissez vos besoins
Questions à vous poser :

Quelle stack ? (React, Vue, Angular, Svelte...)
Quelles pages prioritaires pour Sprint 1 ?
Quels services back-end voulez-vous consommer en premier ?

Livrables attendus aujourd'hui :

Liste des user stories principales
Wireframes ultra-simples (papier/Figma)
Décisions techniques (framework, routing, state management)

ÉQUIPE AUTH : Cadrez votre périmètre
Votre mission :

Service d'authentification centralisé pour toute la plateforme
À livrer progressivement sur les 8 demi-journées

Endpoints prioritaires (pour cette demi-journée, choisissez-en 2-3) :

POST /auth/register - Inscription
POST /auth/login - Connexion
POST /auth/refresh - Rafraîchir token
POST /auth/logout - Déconnexion
GET /users/me - Profil utilisateur
(Vous ajouterez OAuth, RBAC, etc. plus tard)


📝 PHASE 2 : RÉDACTION CONTRAT OPENAPI (14h20-15h20) - 60 min
Mini-rappel théorique (5 min)
OpenAPI = Contrat entre Front et Back
Structure minimale :
yamlopenapi: 3.0.3
info:
  title: Mon Service
  version: 1.0.0
paths:
  /ma-ressource:
    get:
      summary: Description
      responses:
        '200':
          description: Succès
🎯 Consignes pour TOUS
Aujourd'hui, rédigez UNIQUEMENT :

✅ 2-3 endpoints maximum (les plus critiques)
✅ 1 exemple de réponse success par endpoint
✅ 1 exemple d'erreur (422 ou 404)
✅ Schéma de votre ressource principale

NE PAS faire aujourd'hui :

❌ Tous les endpoints possibles
❌ Tous les cas d'erreur
❌ Schémas hyper détaillés

Exemple guidé : Service EVENTS (structure à adapter)
yamlopenapi: 3.0.3
info:
  title: SmartCity Events API
  version: 1.0.0
  description: Gestion des événements culturels de la ville
  contact:
    name: Équipe Events
    email: events@smartcity.local

servers:
  - url: http://localhost:3002/api/v1
    description: Développement local

paths:
  /events:
    get:
      summary: Liste des événements à venir
      description: Retourne les événements triés par date
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            minimum: 1
            default: 1
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: type
          in: query
          description: Filtrer par type d'événement
          schema:
            type: string
            enum: [concert, exposition, conference, festival, sport]
      responses:
        '200':
          description: Liste paginée
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Event'
                  page:
                    type: integer
                  limit:
                    type: integer
                  total:
                    type: integer
              example:
                data:
                  - id: "550e8400-e29b-41d4-a716-446655440000"
                    title: "Jazz sous les étoiles"
                    type: "concert"
                    startDate: "2025-07-15T20:00:00Z"
                    location:
                      name: "Place du Capitole"
                      city: "Toulouse"
                page: 1
                limit: 20
                total: 45

  /events/{id}:
    get:
      summary: Détails d'un événement
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Détails complets
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Event'
        '404':
          description: Événement non trouvé
          content:
            application/problem+json:
              schema:
                $ref: '#/components/schemas/ProblemDetails'
              example:
                type: "https://smartcity.api/errors/not-found"
                title: "Not Found"
                status: 404
                detail: "Événement avec l'ID spécifié introuvable"
                correlationId: "123e4567-e89b-12d3-a456-426614174000"

components:
  schemas:
    Event:
      type: object
      required: [id, title, type, startDate]
      properties:
        id:
          type: string
          format: uuid
        title:
          type: string
        description:
          type: string
        type:
          type: string
          enum: [concert, exposition, conference, festival, sport]
        startDate:
          type: string
          format: date-time
        endDate:
          type: string
          format: date-time
        location:
          type: object
          properties:
            name:
              type: string
            address:
              type: string
            city:
              type: string

    ProblemDetails:
      type: object
      properties:
        type:
          type: string
        title:
          type: string
        status:
          type: integer
        detail:
          type: string
        correlationId:
          type: string
```

### 💡 Guides par type de service

**Pour TRANSPORT** : Commencez par `GET /lines` et `GET /lines/{id}`  
**Pour TROC** : Commencez par `GET /items` et `POST /items`  
**Pour DÉPANNAGE** : Commencez par `GET /providers` et `POST /appointments`  
**Pour ALERTE** : Commencez par `GET /alerts` et `POST /alerts`  
**Pour MÉTÉO** : Commencez par `GET /weather/current` et `GET /weather/forecast`  
**Pour PARKING** : Commencez par `GET /parkings` et `GET /parkings/{id}/availability`  
**Pour BIBLIOTHÈQUE** : Commencez par `GET /books` et `POST /loans`

### Validation inter-équipes (15 dernières min)

1. **Chaque équipe back** publie son YAML sur un canal partagé (Slack/Discord/Miro)
2. **L'équipe FRONT** commente : "Est-ce que ces endpoints me permettent de construire mes premières pages ?"
3. **Ajustements** si nécessaire

---

## 🏗️ PHASE 3 : INITIALISATION PROJET (15h20-16h15) - 55 min

### Pause (15h20-15h35) - 15 min ☕

### Setup technique (15h35-16h15) - 40 min

#### Consignes GÉNÉRALES

**1. Choix de stack (5 min par équipe)**

Décidez :
- Langage : Node.js ? Python ? Java ? Go ? (libre)
- Framework : Express ? FastAPI ? Spring Boot ? (libre)
- Base de données : PostgreSQL ? MongoDB ? MySQL ? (on connectera J2-J3)

**2. Structure de projet recommandée**
```
mon-service/
├── src/ ou app/          # Code source
├── tests/                # Tests (à remplir progressivement)
├── contracts/            # Votre fichier OpenAPI
├── docs/                 # Documentation
├── .env.example          # Template de config
├── .gitignore
├── README.md
└── package.json (ou équivalent selon stack)
3. Ce que vous devez avoir dans le README
markdown# [NOM DU SERVICE]

## Description
[1-2 phrases sur le rôle du service]

## Stack Technique
- Langage : 
- Framework :
- Base de données (prévue) :

## Prérequis
- [Liste des outils nécessaires]

## Installation
```bash
# Commandes pour installer
```

## Lancement
```bash
# Commandes pour démarrer
```

## Endpoints (v0.1 - Sprint 1)
- [ ] `GET /ressources` - Liste
- [ ] `GET /ressources/{id}` - Détail
- [ ] `POST /ressources` - Création (J2-J3)

## Roadmap
### Sprint 1 (J1-J2)
- Contrat OpenAPI ✅
- Structure projet ✅
- Endpoints basiques (mock data)

### Sprint 2 (J3-J4)
- Base de données
- CRUD complet
- Auth integration

[...]
4. Git : 1 commit initial
bashgit init
git add .
git commit -m "chore: initial project setup with OpenAPI contract"
```

---

## 🎤 PHASE 4 : REVIEW & PLANIFICATION (16h15-17h00) - 45 min

### Format

**Chaque équipe présente (7 min max) :**

1. **Domaine choisi** (1 min)
   - Quel service ?
   - Pourquoi ce choix ?

2. **Décisions techniques** (2 min)
   - Stack retenue
   - Structure de projet
   - Justification

3. **Contrat OpenAPI** (3 min)
   - Montrer le fichier YAML
   - Expliquer les 2-3 endpoints choisis pour Sprint 1
   - Points d'attention / questions

4. **Roadmap** (1 min)
   - Plan pour J2 (demain après-midi)
   - Blockers potentiels

### Questions à l'enseignant

Dernières 10 minutes : Q&A collective

---

## ✅ CHECKLIST DE FIN DE JOURNÉE

**Chaque équipe vérifie avant de partir :**

- [ ] Domaine / stack choisi et documenté
- [ ] Fichier OpenAPI avec 2-3 endpoints (dans `contracts/`)
- [ ] Structure de projet initialisée
- [ ] README avec description, installation, roadmap
- [ ] Git init + 1er commit
- [ ] Identifié les dépendances à installer pour J2

**Équipe FRONT spécifiquement :**
- [ ] Framework choisi (React/Vue/Angular/Svelte)
- [ ] Liste des services back à consommer en priorité
- [ ] Wireframes papier des 2-3 premières pages
- [ ] User stories principales identifiées

**RIEN DE PLUS N'EST ATTENDU AUJOURD'HUI !**

---

## 📚 POUR DEMAIN (J2 Après-midi)

**Théorie du matin** : Performance, Caching, Pagination avancée

**Pratique de l'après-midi** :
- Implémenter les 2-3 endpoints avec données **en dur** (tableau JS/Python/Java)
- Générer et publier les mocks avec Prism
- Front : consommer les premiers mocks
- Middleware de gestion d'erreurs RFC 7807
- Tests unitaires basiques (1-2 par endpoint)

**À faire ce soir (optionnel mais recommandé) :**
- Installer les dépendances de votre stack
- Regarder 1 vidéo sur votre framework choisi si besoin de refresh
- Lire la doc de Prism : https://stoplight.io/open-source/prism

---

## 🎯 PROGRESSION SUR LES 8 DEMI-JOURNÉES
```
J1 PM ✅ : Contrat OpenAPI + Structure projet
J2 PM : Endpoints avec mock data + Tests basiques
J3 PM : Intégration SOAP/externe + Résilience patterns
J4 PM : Sécurité (audit + auth integration)
J5 PM : OAuth + Hardening
J6 PM : Documentation complète + Tests exhaustifs
J7 PM : Observabilité + API Gateway
J8 PM : Containerisation + CI/CD
J9 AM : Soutenances