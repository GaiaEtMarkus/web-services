🚀 PROJET FIL ROUGE - SmartCity API Platform
Planning complet des 8 demi-journées
📅 JOUR 1 - Kickoff & Fondations
Théorie matin : Fondamentaux Web Services, HTTP, JSON/XML
Durée PM : 3h30 (13h30-17h00)

🎯 Objectifs J1
Constitution des 5 équipes (1 Front + 1 Auth + 3 Services)
Choix des domaines métiers
Contrat OpenAPI minimal (2-3 endpoints/équipe)
Initialisation Git + README
📋 Livrables J1
✅ Fichier openapi.yaml avec 2-3 endpoints
✅ Structure projet initialisée
✅ README avec stack + roadmap
✅ 1er commit Git

📅 JOUR 2 - Implémentation Basique & Mock
Théorie matin : API REST avancé, Versioning, Documentation OpenAPI
Durée PM : 3h30

🎯 Objectifs J2
Implémenter 2-3 endpoints avec données en dur (tableaux)
Générer mocks automatiques avec Prism
Front : consommer les premiers mocks
Middleware de gestion d'erreurs RFC 7807
Tests unitaires basiques (1-2/endpoint)
📋 Livrables J2
✅ Endpoints fonctionnels (mock data en mémoire)
✅ Server Prism déployé et accessible
✅ Middleware d'erreurs RFC 7807
✅ 3-5 tests unitaires
✅ Front : 1-2 pages consommant les mocks

Exemple données en dur :

javascript
// Node.js/Express
const events = [
  { id: '550e8400-...', title: 'Jazz Festival', type: 'concert' },
  { id: '660e8400-...', title: 'Art Expo', type: 'exposition' }
];

app.get('/api/v1/events', (req, res) => {
  res.json({ data: events, page: 1, limit: 20, total: 2 });
});
📅 JOUR 3 - Base de Données & CRUD Complet
Théorie matin : Sécurité API (Partie 1) - Auth, JWT, OAuth
Durée PM : 3h30

🎯 Objectifs J3
Connecter une base de données (PostgreSQL/MongoDB)
Remplacer les données en dur par de vraies requêtes DB
CRUD complet sur la ressource principale
Équipe Auth : JWT fonctionnel (register/login/refresh)
Validation des données entrantes (Joi/Zod/Pydantic)
📋 Livrables J3
✅ Base de données créée + migrations
✅ ORM/ODM configuré (Prisma/Sequelize/Mongoose/SQLAlchemy)
✅ CRUD complet (GET, POST, PUT, DELETE)
✅ Validation des inputs
✅ Équipe Auth : JWT généré et vérifié
✅ Tests d'intégration DB (2-3)

Point d'intégration :

Les autres équipes peuvent commencer à appeler l'API Auth pour obtenir des tokens
Front : formulaire login/register fonctionnel
📅 JOUR 4 - Sécurité & Intégration Auth
Théorie matin : Sécurité API (Partie 2) - OWASP Top 10, Vulnérabilités
Durée PM : 3h30

🎯 Objectifs J4
Protéger les endpoints avec authentification JWT
Middleware de vérification de token
RBAC basique (user/admin)
Rate limiting
Audit de sécurité entre équipes (checklist OWASP)
📋 Livrables J4
✅ Endpoints protégés (header Authorization: Bearer <token>)
✅ Middleware d'authentification
✅ 2 rôles minimum (user, admin)
✅ Rate limiting configuré (express-rate-limit/équivalent)
✅ Headers de sécurité (Helmet.js/équivalent)
✅ Rapport d'audit sécurité (1 page/équipe)

Exemple protection :

javascript
app.post('/api/v1/events', authenticateJWT, (req, res) => {
  // Seuls les utilisateurs authentifiés peuvent créer
});

app.delete('/api/v1/events/:id', authenticateJWT, requireRole('admin'), (req, res) => {
  // Seuls les admins peuvent supprimer
});
📅 JOUR 5 - Intégration Services Externes & Résilience
Théorie matin : SOA/SOAP, Architecture distribuée
Durée PM : 3h30

🎯 Objectifs J5
1 équipe intègre un service externe (météo, maps, paiement en mock)
Patterns de résilience : Circuit Breaker, Retry, Timeout
Communication inter-services (HTTP ou events)
Gestion des erreurs externes
Logging structuré
📋 Livrables J5
✅ Intégration 1 API externe (Axios/Fetch avec gestion erreur)
✅ Circuit Breaker implémenté (opossum/resilience4j)
✅ Logs structurés (Winston/Pino/Loguru)
✅ Correlation ID propagé entre services
✅ Tests avec API externe mockée (nock/wiremock)

Exemple Circuit Breaker :

javascript
const CircuitBreaker = require('opossum');

const getWeather = () => axios.get('https://api.weather.com/...');
const breaker = new CircuitBreaker(getWeather, { timeout: 3000 });

breaker.fire()
  .then(data => res.json(data))
  .catch(err => res.status(503).json({ error: 'Service temporairement indisponible' }));
📅 JOUR 6 - Microservices & API Gateway
Théorie matin : Microservices, API Gateway, Service Discovery
Durée PM : 3h30

🎯 Objectifs J6
Mise en place d'une API Gateway simple (Express Gateway/KrakenD/Kong)
Routing centralisé vers les 4 services back
Agrégation de requêtes (optionnel)
Front : passe uniquement par la Gateway
📋 Livrables J6
✅ API Gateway déployée (1 point d'entrée unique)
✅ Routes configurées vers tous les services
✅ Load balancing basique (si plusieurs instances)
✅ CORS configuré sur la Gateway
✅ Front : URL de base changée vers Gateway
✅ Documentation architecture (schéma)

Architecture cible :

Front (localhost:3000)
    ↓
API Gateway (localhost:8080)
    ├─→ Auth Service (localhost:3001)
    ├─→ Events Service (localhost:3002)
    ├─→ Transport Service (localhost:3003)
    └─→ Parking Service (localhost:3004)
📅 JOUR 7 - Tests, Performance & Observabilité
Théorie matin : Performance, Tests, DevOps
Durée PM : 3h30

🎯 Objectifs J7
Tests de charge (K6/JMeter)
Optimisation performance (caching, indexation DB)
Monitoring (Health checks, Metrics)
Documentation Postman/Insomnia complète
📋 Livrables J7
✅ Tests de charge réalisés (rapport de résultats)
✅ Caching implémenté (Redis ou in-memory)
✅ Index DB sur colonnes critiques
✅ Health check endpoint (GET /health)
✅ Collection Postman/Insomnia exportée
✅ OpenAPI finalisé à 100%

Exemple Health Check :

javascript
app.get('/health', async (req, res) => {
  const dbOk = await checkDatabase();
  const cacheOk = await checkRedis();
  
  res.status(dbOk && cacheOk ? 200 : 503).json({
    status: dbOk && cacheOk ? 'healthy' : 'unhealthy',
    services: { database: dbOk, cache: cacheOk }
  });
});
📅 JOUR 8 - Finalisation & Déploiement
Théorie matin : Conteneurisation, CI/CD, Observabilité
Durée PM : 3h30

🎯 Objectifs J8
Dockerisation de tous les services
Docker Compose pour orchestration locale
Pipeline CI basique (GitHub Actions/GitLab CI)
Préparation soutenance J9
📋 Livrables J8
✅ Dockerfile par service
✅ docker-compose.yml global
✅ Tous les services démarrables avec docker-compose up
✅ Pipeline CI (linting + tests)
✅ README de déploiement
✅ Slides de présentation J9 (template fourni)

docker-compose.yml minimal :

yaml
version: '3.8'
services:
  gateway:
    build: ./gateway
    ports: ["8080:8080"]
  
  auth:
    build: ./auth-service
    environment:
      - DB_HOST=postgres
  
  events:
    build: ./events-service
  
  postgres:
    image: postgres:15
    environment:
      - POSTGRES_DB=smartcity
📅 JOUR 9 - SOUTENANCES
Matin complet : 9h00-12h30

Format de présentation (15 min/équipe)
Structure imposée (10 min présentation + 5 min Q&A)
1. Contexte & Démo (4 min)

Quel service ? Quelle valeur métier ?
Démo live de 2-3 use cases
2. Architecture & Choix techniques (3 min)

Schéma d'architecture
Stack justifiée
Patterns utilisés (Circuit Breaker, RBAC, etc.)
3. Défis & Apprentissages (2 min)

1-2 difficultés rencontrées
Comment résolues ?
Ce que vous referiez différemment
4. Métriques (1 min)

Couverture de tests
Résultats tests de charge
Uptime / Health checks
Grille d'évaluation
Critère	Points	Détails
Fonctionnalités	/5	CRUD complet, Auth, endpoints avancés
Sécurité	/4	JWT, RBAC, headers, validation
Architecture	/3	Code propre, patterns, découplage
Tests	/2	Unitaires, intégration, charge
Documentation	/2	OpenAPI, README, Postman
Déploiement	/2	Docker, CI/CD
Présentation	/2	Clarté, démo, défense
TOTAL	/20	
📊 RÉCAPITULATIF DES ÉQUIPES
Configuration finale (15-20 étudiants)
Équipe 1 : FRONT-END (3-4 pers)
Stack : React/Vue/Angular
Livrables :

SPA consommant les 4 services via Gateway
Pages : Login, Dashboard, Liste ressources, Détail, Création
Gestion état (Redux/Pinia/NgRx)
Gestion erreurs + Loading states
Équipe 2 : AUTH (3-4 pers)
Stack : Node.js/Python/Java
Endpoints :

POST /auth/register
POST /auth/login
POST /auth/refresh
GET /users/me
PUT /users/me
(Optionnel) OAuth Google
Équipe 3 : SERVICE 1 (3-4 pers)
Exemple : EVENTS
Endpoints :

CRUD complet sur /events
POST /events/{id}/register
GET /events/{id}/attendees
GET /events/popular
Équipe 4 : SERVICE 2 (3-4 pers)
Exemple : TRANSPORT
Endpoints :

GET /lines + GET /lines/{id}
GET /stops/nearby
POST /itineraries/calculate
GET /traffic/alerts
Équipe 5 : SERVICE 3 (3-4 pers)
Exemple : PARKING
Endpoints :

GET /parkings + GET /parkings/{id}
GET /parkings/{id}/availability
POST /parkings/{id}/reserve
GET /reservations
🛠️ STACK TECHNIQUE RECOMMANDÉE
Back-End (choix libre par équipe)
Node.js: Express + Prisma + PostgreSQL
Python: FastAPI + SQLAlchemy + PostgreSQL
Java: Spring Boot + JPA + PostgreSQL
Front-End
React + React Router + Axios + Tailwind
Outils communs
API Gateway: Express Gateway ou KrakenD
Base de données: PostgreSQL (conteneurisé)
Cache: Redis (optionnel)
Tests: Jest/Pytest/JUnit + Supertest/TestClient
Tests charge: K6
Mock: Prism
Logs: Winston/Pino/Loguru
CI/CD: GitHub Actions
Conteneurs: Docker + Docker Compose
📚 RESSOURCES FOURNIES
Templates & Starters
 Template OpenAPI avec exemples
 Structure de projet par langage (Node/Python/Java)
 docker-compose.yml de base
 GitHub Actions workflow template
 Collection Postman de démarrage
Documentation
 Guide des conventions (naming, erreurs, pagination)
 Checklist sécurité OWASP
 Guide intégration Auth (pour services consommateurs)
 Template slides soutenance
Outils
 Serveur GitLab/GitHub pour chaque équipe
 Accès Prism Cloud (optionnel)
 Environnement de déploiement (VMs ou K8s)
✅ CHECKLIST GLOBALE PROJET
Technique
 4 services back + 1 front déployés
 API Gateway fonctionnelle
 Authentification JWT sur tous les services
 Base de données persistante
 Tests > 60% de couverture
 Documentation OpenAPI complète
 Docker Compose opérationnel
 CI/CD avec au moins linting + tests
Fonctionnel
 CRUD complet sur chaque ressource
 Pagination sur toutes les listes
 Gestion d'erreurs RFC 7807
 Recherche/filtres sur au moins 1 service
 Relations entre ressources (ex: events → attendees)
 Au moins 1 intégration externe
Sécurité
 HTTPS en production (ou expliqué)
 Headers de sécurité (Helmet)
 Rate limiting
 Validation des inputs
 RBAC (2 rôles minimum)
 Pas de secrets en clair dans Git
Documentation
 README par service (install, run, test)
 Architecture diagram
 OpenAPI 3.0 par service
 Collection Postman exportée
 Guide de déploiement
🎯 CRITÈRES DE SUCCÈS
Minimum Viable (pour valider le projet)
✅ 3 services back fonctionnels (Auth + 2 métiers)
✅ Front consommant au moins 1 service
✅ Auth JWT opérationnelle
✅ CRUD complet sur 1 ressource
✅ Tests basiques présents
✅ Dockerisation réussie
✅ Soutenance cohérente
Pour viser l'excellence (16+/20)
✅ 4 services métiers + API Gateway
✅ Intégration externe avec résilience
✅ Caching Redis
✅ Tests de charge réalisés
✅ CI/CD complet (deploy auto)
✅ Monitoring (Prometheus/Grafana)
✅ Documentation exemplaire
📞 SUPPORT ENSEIGNANT
Pendant les séances
Tour des équipes toutes les 30 min
Q&A collective les 15 dernières minutes
Validation des choix techniques J1-J2
Entre les séances
Canal Discord/Slack dédié
Réponse sous 24h max
Office hours (optionnel) : 30 min avant chaque cours
Points de blocage anticipés
J3 : Connexion BDD → Prévoir 30 min de debugging collectif
J4 : Intégration JWT entre services → Session de pair programming
J6 : Configuration Gateway → Demo live de troubleshooting
J8 : Docker networking → Checklist de debug fournie
🎓 COMPÉTENCES VISÉES
À l'issue de ce projet, les étudiants sauront :

✅ Concevoir une API REST complète (design, versioning, doc)
✅ Implémenter une authentification JWT sécurisée
✅ Gérer les erreurs selon RFC 7807
✅ Écrire des tests unitaires et d'intégration
✅ Conteneuriser des applications microservices
✅ Mettre en place un pipeline CI/CD basique
✅ Auditer la sécurité d'une API (OWASP)
✅ Documenter avec OpenAPI 3.0
✅ Collaborer en équipe sur un projet distribué
✅ Présenter et défendre des choix techniques

