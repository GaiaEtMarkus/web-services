Mastère 2 - 63h | Format Journées Complètes (9h-17h)

🎯 Projet Fil Rouge : "SmartCity API Platform" - Version Simplifiée
Concept : Plateforme d'APIs REST pour une ville intelligente - Version simplifiée et réalisable
Organisation :

Groupes de 3-4 étudiants (rôles tournants : PO, Dev Lead, DevOps, QA)
4 sprints de 2 jours avec planning/retro
Progression guidée après-midi par après-midi
Démos client régulières

🌆 Vision Produit — SmartCity (version pédagogique)

- Objectif: offrir à des citoyens une application simple pour consulter les événements locaux, les lignes de transport et la météo, avec authentification basique pour des actions futures (favoris, inscriptions).
- Parcours utilisateur minimal (J1‑J2):
  - “Je vois la liste des événements cet été”, “je clique sur un événement pour voir les détails”, “je consulte les lignes de transport à proximité”, “je vois la météo de ma ville”.
  - Auth apparaît J4‑J5 (login/register) pour déverrouiller des actions ultérieures (favoris).
- Done quand (Sprint 1):
  - Front affiche listes/détails réels via services Events/Transport; météo en mock (puis adapter J3).
  - Performances correctes (pagination) et erreurs lisibles (Problem Details).

🧱 Rôles des services (clarifiés)

- Events: référentiel d’événements (titre, date, lieu, description). CRUD complet, tri par date, pagination.
- Transport: catalogue de lignes/arrêts (simplifié). Lecture paginée, bientôt filtres par ligne.
- Weather: façade (adapter) sur fournisseur externe. Normalise la réponse et gère timeout/retry/fallback.
- Auth: registres, login, refresh, logout. JWT (access/refresh) à partir de J4, intégré Front J5.
- Front: SPA qui consomme les services via contrats OpenAPI, ergonomie et gestion des erreurs.

🔀 Répartition des équipes (nouveau) — 5 groupes

- 1 groupe <strong>Front‑end</strong> (SPA) consomme les microservices exposés par les autres groupes.
- 4 groupes <strong>Back‑end</strong> implémentent chacun 1 service: <strong>Events</strong>, <strong>Transport</strong>, <strong>Weather (adapter)</strong>, <strong>Auth</strong>.
- Contrats d’API <strong>OpenAPI</strong> partagés et versionnés (PR obligatoire pour tout breaking change).
- <strong>Tests de contrats</strong> (CDC) avec Pact: le Front est Consumer; chaque Back est Provider.
- Mocks générés depuis les contrats (Stoplight/Prism ou WireMock) pour débloquer le Front en avance.
- Calendrier d’intégration: synchro quotidienne 15 min Front ↔ Back (contrats, endpoints, schémas).
- Critères de démo: scénarios e2e Front qui traversent au moins 2 services avec métriques (latence/erreurs).

📦 Rôles, responsabilités et Definition of Done (DoD) par groupe

- <strong>Front</strong>
  - UI: Shell app, routing, layout, pages Sandbox (Events list/detail, Transport list) + état de chargement/erreurs.
  - Client API: génération à partir d’OpenAPI (autogen types), interceptors (<code>x-correlation-id</code>, auth, retry simple).
  - Observabilité: logs client (niveau debug), traçage <code>x-correlation-id</code> affiché en console/devtools.
  - DoD: pages fonctionnent contre <em>mocks publiés</em>, 1 test d’intégration UI, Readme “Front Quick Start”.

- <strong>Events</strong>
  - Contrat: <code>GET /events</code> (pagination page/limit, tri par date), <code>GET /events/{id}</code>, <code>POST /events</code>, <code>PUT</code>, <code>DELETE</code>.
  - Erreurs: RFC 7807 (400/404/409/422), schéma <code>errors</code> pour validation.
  - Observabilité: health <code>/health</code> (liveness), <code>/ready</code> (readiness), logs JSON + correlation.
  - DoD: endpoints squelette répondent correctement (200/201/204/4xx) + tests unitaires + mock aligné.

- <strong>Transport</strong>
  - Contrat: <code>GET /transports</code> (pagination, <code>sort</code>, <code>filter</code> minimal), <code>GET /transports/{id}</code>.
  - Performance: prévoir champs pour indexation (code ligne), futur ETag.
  - DoD: contrat validé, mock fourni, service squelette, tests.

- <strong>Weather (adapter)</strong>
  - Contrat: <code>GET /weather?city</code>, timeouts (<code>5s</code>), retry (<code>2</code>), fallback (message). Documenter erreurs 504/502.
  - DoD: mock local, design d’adapter externe écrit (ADR spécifique), endpoints squelette + mapping d’erreurs.

- <strong>Auth</strong>
  - Contrat: <code>POST /auth/register</code>, <code>POST /auth/login</code>, <code>POST /auth/refresh</code>, <code>POST /auth/logout</code>; format JWT.
  - Sécurité: mots de passe hashés (bcrypt), rate‑limit basique sur auth, messages d’erreurs sobres (pas de fuite).
  - DoD: schémas d’entrées/sorties documentés, mock, squelette endpoints + tests unitaires de validation.

🧩 Conventions transverses (obligatoires)

- <strong>Naming</strong>: URI pluriels (<code>/events</code>), <code>kebab-case</code> pour paramètres (<code>page-size</code> optionnel, sinon <code>limit</code>), IDs UUID v4.
- <strong>Pagination</strong>: <code>?page=1&limit=20</code>; réponse: <code>{ data: [], page, limit, total }</code>.
- <strong>Tri</strong>: <code>?sort=date</code> ou <code>?sort=-date</code> (desc), multi‑clé supporté plus tard.
- <strong>Filtrage</strong>: <code>?type=concert</code>; pas d’opérateurs complexes J1.
- <strong>Erreurs</strong>: <code>application/problem+json</code>, <code>type</code> stable, <code>correlationId</code> partout.
- <strong>Cache HTTP</strong>: <code>ETag</code> fort pour <code>GET /events/{id}</code> et <code>GET /transports/{id}</code> (prévu; à activer J2/J3).
- <strong>Santé</strong>: <code>/health</code> (liveness, 200) ; <code>/ready</code> (readiness, dépendances prêtes) par service.

🕒 Timeboxing après‑midi J1 (suggestion)

- 13:30–13:45: cadrage inter‑équipes (contrats cibles, champs, erreurs).
- 13:45–14:30: rédaction OpenAPI v1 + exemples (success/erreurs) → PR + review croisée Front/Back.
- 14:30–15:00: génération des mocks et publication d’URL mocks; Front branche la Sandbox.
- 15:00–16:00: création squelettes services (routes, middlewares RFC 7807, health/ready) + tests unitaires initiaux.
- 16:00–16:30: CI minimale (lint/tests/spectral) + mise à jour Readme + ADRs.
- 16:30–17:00: daily/mini‑review: démos mocks + Sandbox + endpoints squelette.


📅 Structure : 9 Journées (7h/jour = 63h)
Format Optimisé :

Matin (9h-12h30) : Théorie + Mini-exercices interactifs → 3h30
Pause déjeuner (12h30-13h30) : 1h
Après-midi (13h30-17h) : Pratique intensive sur projet → 3h30


📋 PLANNING DÉTAILLÉ AMÉLIORÉ
JOUR 1 : Fondamentaux REST & Lancement Agile
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h30 : Kick-off & Contexte

Présentation du cours et enjeux professionnels
Tour de table : expériences API réelles
Présentation du projet SmartCity

9h30-10h45 : Fondamentaux REST

Évolution : monolithique → SOA → microservices → serverless
Architecture REST : 6 contraintes de Fielding
Richardson Maturity Model (niveaux 0-3)
💡 Mini-exercice (15min) : Évaluer la maturité REST de 3 APIs publiques (Twitter, Stripe, GitHub)

10h45-11h : ☕ Pause
11h-12h15 : Design d'APIs & Standards

Ressources, URI design, naming conventions
Verbes HTTP : sémantique précise (GET, POST, PUT, PATCH, DELETE)
Codes de statut détaillés et quand les utiliser
Idempotence et implications pratiques
💡 Mini-exercice (15min) : Corriger des URLs mal conçues

12h15-12h30 : Gestion d'Erreurs Standardisée ⭐ NOUVEAU

Format d'erreur cohérent (RFC 7807 Problem Details)
Codes d'erreur métier vs HTTP
Messages d'erreur localisés
Envelope JSON vs pas d'envelope

APRÈS-MIDI (13h30-17h) - PRATIQUE + AGILE
13h30-14h15 : Sprint Planning #1 🏃 NOUVEAU

🎮 Présentation détaillée du projet et architecture cible
Constitution des équipes + attribution des rôles
Création du backlog initial (user stories)
Estimation en story points (planning poker)
Sélection des stories du Sprint 1
Definition of Done

🧭 Guidage Fil Rouge — Démarrage coordonné (Front + Micro‑services)

- Gouvernance contrat d’API
  - Choix des services v1: Events, Transport, Weather (adapter), Auth.
  - Pour chaque service: rédiger un premier <strong>OpenAPI</strong> minimal (GET list/detail, POST, erreurs RFC 7807).
  - Review croisée: Front (consumer) commente les schémas et exemples; Back (provider) valide la faisabilité.
  - Politique breaking change: PR obligatoire, version mineure si addition; majeure si suppression/renommage.
  - Génération de <strong>mocks</strong> (Prism/WireMock) publiés pour le Front (URL stables).

- Organisation des dépôts Git
  - 1 mono‑repo ou multi‑repos? Recommandation: mono‑repo <code>smartcity/</code> avec dossiers <code>services/events</code>, <code>services/transport</code>, <code>services/weather</code>, <code>services/auth</code>, <code>apps/front</code>, <code>contracts/</code>.
  - Branching: trunk‑based (branche <code>main</code>) + feature branches; PR + review obligatoire; CI sur PR.
  - Conventions commit: Conventional Commits (ex: <code>feat(events): add POST /events with validation</code>).

- CI de base (sur PR)
  - Lint + Tests unitaires + Validation OpenAPI (spectral) + Pact consumer/provider (si applicable).
  - Build du Front contre les mocks (run e2e smoke minimal si possible).

- Setup technique par groupe
  - Back: template Node/Express (ou stack choisie) avec structure standard, gestion d’erreurs RFC 7807, tests.
  - Front: SPA (framework libre) avec dossier <code>api/</code> typé depuis OpenAPI, gestion d’auth (placeholder), routing.
  - Observabilité minimale: logs structurés JSON, <code>x-correlation-id</code> propagé, middleware de timing (latence).

- Décisions d’architecture (ADR 001‑005)
  - ADR‑001: Format d’erreur (RFC 7807) et champs d’extension communs.
  - ADR‑002: Politique de versioning (URL vs media type) — v1 en URL pour simplicité.
  - ADR‑003: Stratégie d’identifiants (UUID v4) et horodatage ISO 8601.
  - ADR‑004: Pagination: <code>?page</code>/<code>?limit</code> (cursor plus tard si besoin) + schéma de réponse documenté.
  - ADR‑005: Politique de cache HTTP de base (ETag/If‑None‑Match pour GET list/detail).

- Livrables simples fin J1 (DoD Sprint 1)
  - Contrats OpenAPI v1 pour Events & Transport approuvés par Front + mocks publiés.
  - Squelettes de services en place (endpoints vides mais répondent 200/501 avec Problem Details cohérents).
  - Repo initialisé, CI basique OK, Readme de chaque service (run, tests, env). 
  - Front: page “sandbox d’intégration” avec appels aux mocks (liste d’événements + détail).

📑 Contrats OpenAPI — squelettes minimaux (extraits)

Events (extrait):
```yaml
openapi: 3.0.3
info: { title: Events API, version: 1.0.0 }
paths:
  /events:
    get:
      summary: List events
      parameters:
        - in: query; name: page; schema: { type: integer, minimum: 1 }
        - in: query; name: limit; schema: { type: integer, minimum: 1, maximum: 100 }
        - in: query; name: sort; schema: { type: string, enum: [date, -date] }
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaginatedEvents'
              examples:
                sample:
                  value: { data: [ { id: 'uuid', title: 'Concert', date: '2025-06-21', location: 'Toulouse' } ], page: 1, limit: 20, total: 1 }
        '422':
          $ref: '#/components/responses/Problem422'
components:
  schemas:
    Event: { type: object, required: [id,title,date], properties: { id: { type: string, format: uuid }, title: { type: string }, date: { type: string, format: date-time }, location: { type: string }, description: { type: string } } }
    PaginatedEvents: { type: object, properties: { data: { type: array, items: { $ref: '#/components/schemas/Event' } }, page: { type: integer }, limit: { type: integer }, total: { type: integer } } }
  responses:
    Problem422:
      description: Unprocessable Entity
      content:
        application/problem+json:
          schema: { type: object, properties: { type: {type: string}, title: {type: string}, status: {type: integer}, detail: {type: string}, instance: {type: string}, errors: { type: object } } }
```

Transport (extrait):
```yaml
openapi: 3.0.3
info: { title: Transport API, version: 1.0.0 }
paths:
  /transports:
    get:
      summary: List transport lines
      parameters:
        - in: query; name: page; schema: { type: integer }
        - in: query; name: limit; schema: { type: integer }
      responses:
        '200': { description: OK }
```

🧪 Mocks & CI rapides

- Prism: <code>prism mock contracts/events.yaml --port 4010</code> (URL communiquée au Front).
- CI: action minimaliste sur PR: <em>npm test</em> (ou équivalent), <em>spectral lint contracts/*.yaml</em>.

14h15-14h30 : Setup Technique

Choix de stack (Node.js/Express, Python/FastAPI, Java/Spring Boot)
Template de projet avec structure recommandée
Git workflow (GitFlow ou trunk-based)

14h30-16h30 : Développement - Events API

🛠️ Implémentation CRUD complète
Format de réponse standardisé avec envelope
Gestion d'erreurs selon RFC 7807
Validation des inputs (Joi, Pydantic, Bean Validation)
Tests unitaires avec mocking
Collection Postman avec tests automatisés

16h30-17h : Daily Stand-up + Code Review ⭐ NOUVEAU

🎬 Stand-up: What done? Blockers? Next?
Code review croisée rapide (2 groupes en binôme)
Feedback instantané

📦 Livrable J1 :

Events API avec gestion d'erreurs standardisée
Tests unitaires (>70% coverage)
Collection Postman
Backlog sur outil Agile (Trello/Jira/GitHub Projects)
Contrat OpenAPI validé par le Front (review croisée) + mock disponible


JOUR 2 : Performance, Caching & API Avancé
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h15 : Daily Stand-up + Recap J1
9h15-10h30 : Design Avancé & Versioning

API-First avec OpenAPI dès la conception
Pagination: offset vs cursor vs keyset (comparaison performance)
Filtrage, tri, recherche full-text
Field selection (sparse fieldsets)
HATEOAS et maturité niveau 3
Stratégies de versioning détaillées
💡 Mini-exercice (15min) : Concevoir une API paginée optimale pour 10M de records

10h30-10h45 : ☕ Pause
10h45-12h30 : Performance & Caching ⭐ NOUVEAU / AMÉLIORÉ

HTTP Caching :

Headers: ETag, Cache-Control, Last-Modified
Validation vs Expiration
CDN et edge caching


Server-side Caching :

Redis : patterns (cache-aside, write-through)
Cache invalidation strategies
TTL optimization


Database Optimization :

Indexing stratégique
Query profiling et EXPLAIN
N+1 queries problem
Connection pooling


Compression & Response Size :

Gzip, Brotli
Partial responses


💡 Mini-exercice (20min) : Diagnostiquer une API lente (cas pratique avec requêtes SQL)

APRÈS-MIDI (13h30-17h) - PRATIQUE
13h30-14h30 : Transport API avec Optimisations

🎨 Conception collaborative (whiteboard/Miro)
Modélisation des ressources et relations
Implémentation avec focus performance :

Pagination cursor-based
Field selection (?fields=id,name,location)
Filtres composites
Eager loading pour éviter N+1



14h30-16h30 : Caching Multi-niveaux ⭐ NOUVEAU

🚀 Setup Redis
Implémentation cache pour Events & Transport
HTTP caching headers
Cache warming strategy
Monitoring des hit rates
Benchmark avant/après avec Apache Bench ou k6

16h30-17h : Sprint Review #1 + Retrospective 🏁

🎬 Démo client (enseignant = product owner)
Métriques : velocity, burndown
Retrospective : What went well? What to improve? Actions
Calcul de la vélocité pour Sprint 2

📦 Livrable J2 :

Transport API optimisée
Redis caching opérationnel
Rapport de performance (avant/après)
Retrospective documentée
Front intégré sur endpoints Events/Transport via mocks puis vrais services (switch sans régression)


JOUR 3 : SOAP/SOA & Intégration Multi-protocoles
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h15 : Daily + Sprint Planning #2
9h15-10h45 : Architecture SOA & SOAP

Paradigme SOA : principes SOLID appliqués aux services
ESB et patterns d'intégration
SOAP : structure détaillée
WSDL : lecture et compréhension
WS-Security, WS-ReliableMessaging
💡 Mini-exercice (20min) : Analyser un WSDL réel et identifier les opérations

10h45-11h : ☕ Pause
11h-12h30 : Patterns d'Intégration ⭐ NOUVEAU

Adapter pattern pour intégration hétérogène
Circuit breaker (Resilience4j, Hystrix)
Retry avec backoff exponentiel
Timeout strategies
Fallback et graceful degradation
Comparaison REST vs SOAP vs GraphQL vs gRPC (tableau décisionnel)
💡 Mini-exercice (15min) : Choisir le bon protocole pour 5 scénarios

APRÈS-MIDI (13h30-17h) - PRATIQUE
13h30-15h : Weather Service Multi-protocoles

🌦️ Consommation API SOAP externe (météo)
Création d'un adaptateur REST
Implémentation patterns de résilience :

Circuit breaker
Retry avec exponential backoff
Timeout configuration
Fallback sur cache



15h-15h15 : ☕ Pause
15h15-16h30 : Orchestration & Communication Inter-services ⭐ AMÉLIORÉ

Service Mesh concepts (Istio basics)
Sync vs Async communication
Message queue introduction (RabbitMQ/Kafka concepts)
Traçabilité distribuée : correlation IDs
Propagation de contexte (trace headers)

16h30-17h : Debug Challenge ⭐ NOUVEAU

🔍 Exercice pratique : Une requête échoue à travers 3 services
Utiliser les logs et correlation IDs pour trouver le problème
Introduction au tracing distribué

📦 Livrable J3 :

Weather Service avec résilience
Architecture multi-services communicante
Logs avec correlation IDs
Documentation des patterns utilisés
CDC (Pact) front↔weather validés; fallback côté front si service météo indisponible


JOUR 4 : Sécurité - Fondamentaux & Authentification
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h15 : Daily + Security Mindset Discussion
9h15-10h45 : Menaces & Fondamentaux

🔒 OWASP API Security Top 10 (2023) détaillé avec exemples :

Broken Object Level Authorization (BOLA)
Broken Authentication
Broken Object Property Level Authorization
Unrestricted Resource Consumption
Broken Function Level Authorization
Mass Assignment
Security Misconfiguration
Injection
Improper Assets Management
Unsafe Consumption of APIs


💡 Mini-exercice (20min) : Identifier les vulnérabilités dans 3 endpoints de code

10h45-11h : ☕ Pause
11h-12h30 : Authentification & JWT

Authentification vs Autorisation vs Identification
Comparaison : Sessions, Cookies, Tokens
JWT en profondeur :

Anatomie (header, payload, signature)
Claims standards (iss, sub, aud, exp, iat, nbf, jti)
Algorithmes : HS256 vs RS256 vs ES256
Access vs Refresh tokens
Token storage (où et comment)
Révocation strategies


💡 Mini-exercice (15min) : Décoder un JWT et identifier les problèmes de sécurité

APRÈS-MIDI (13h30-17h) - PRATIQUE
13h30-14h30 : Security Audit Complet ⭐ AMÉLIORÉ

🔍 Audit systématique avec checklist OWASP
Utilisation d'un scanner automatique (OWASP ZAP) ⭐ NOUVEAU
Identification et classification des vulnérabilités (CVSS)
Rapport d'audit avec priorités et plan de remédiation
Matrice de risques

14h30-16h30 : Auth Service & Protection

🛠️ Implémentation complète :

Register avec validation stricte
Login avec bcrypt (work factor 12+)
Refresh token rotation
Logout et blacklist
Rate limiting sur auth endpoints
Account lockout après tentatives


Middleware de protection avec RBAC
Tests de sécurité automatisés

16h30-17h : Penetration Testing Workshop ⭐ NOUVEAU

🎯 Attaques simulées entre groupes :

Tentatives de BOLA
Token tampering
Brute force (avec rate limiting en place)


Défense et analyse post-mortem

📦 Livrable J4 :

Auth Service robuste
APIs protégées avec RBAC
Rapport d'audit + scan OWASP ZAP
Tests de sécurité automatisés
Front connecté à Auth (login/logout), gestion de session/token côté UI, routes protégées


JOUR 5 : OAuth, Hardening & Sprint Review
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h15 : Daily + Security Recap
9h15-11h : OAuth 2.0 & OpenID Connect Approfondi

🎫 Flows détaillés avec diagrammes :

Authorization Code (le plus sûr)
PKCE (pour SPAs et mobile)
Client Credentials (M2M)
Implicit (déprécié, pourquoi?)


Rôles et responsabilités
Scopes granulaires et consent
OpenID Connect :

ID Token vs Access Token
UserInfo endpoint
Claims standard


💡 Mini-exercice (20min) : Diagrammer le flow Authorization Code avec PKCE

11h-11h15 : ☕ Pause
11h15-12h30 : Security Hardening Avancé ⭐ AMÉLIORÉ

Rate Limiting :

Algorithmes : Token Bucket, Leaky Bucket, Fixed/Sliding Window
Par IP, par user, par endpoint
Distributed rate limiting (Redis)


Headers de sécurité :

HSTS, CSP, X-Content-Type-Options, X-Frame-Options
Referrer-Policy, Permissions-Policy


CORS configuration stricte
Input Validation & Sanitization
Secrets Management (environment variables, Vault)
Security.txt et responsible disclosure
💡 Mini-exercice (15min) : Configurer des headers de sécurité optimaux

APRÈS-MIDI (13h30-17h) - PRATIQUE
13h30-15h : OAuth 2.0 Integration ⭐ AMÉLIORÉ

🔐 Setup avec provider réel (Auth0, Keycloak, ou Supabase)
Authorization Code + PKCE flow
Social login (Google + GitHub)
Scopes par service de SmartCity
Token introspection et userinfo
Logout universel (frontchannel/backchannel)

15h-15h15 : ☕ Pause
15h15-16h30 : Hardening Final & Automated Security Testing ⭐ NOUVEAU

🛡️ Implémentation complète :

Rate limiting distribué avec Redis
Tous les headers de sécurité
Validation stricte avec schemas
Logging de sécurité (auth failures, suspicious activity)
WAF rules basiques


Tests de sécurité automatisés dans CI/CD :

SAST (Static Analysis)
Dependency scanning (Snyk, Dependabot)
Secrets scanning



16h30-17h : 🏁 Sprint Review #2 + Retrospective

Démo : système d'auth complet avec OAuth
Simulation d'attaque et défense
Review des vulnérabilités corrigées
Retrospective sprint 2

📦 Livrable J5 :

OAuth complet avec social login
Hardening total avec rate limiting
Security testing automatisé
Rapport de sécurité final
Front: parcours utilisateur complet (register/login/refresh/logout) + erreurs UX localisées


JOUR 6 : Documentation, Tests & Qualité
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h15 : Daily + Sprint Planning #3
9h15-10h30 : Documentation Professionnelle ⭐ AMÉLIORÉ

OpenAPI 3.0/3.1 exhaustif :

Composants réutilisables (schemas, responses, parameters)
Exemples multiples par endpoint
Webhooks et callbacks
Security schemes
Links pour HATEOAS


Documentation interactive : Swagger UI, Redoc, Stoplight
Documentation externe : Readme.io, GitBook
Changelog et versioning de doc
Guides d'intégration et tutorials
💡 Mini-exercice (15min) : Critiquer la doc d'une API publique

10h30-10h45 : ☕ Pause
10h45-12h30 : Stratégies de Tests Complètes ⭐ AMÉLIORÉ

Pyramide des tests détaillée
Tests unitaires : mocking, stubs, fixtures
Tests d'intégration : bases de données de test, containers
Tests de contrats (Pact, Spring Cloud Contract) ⭐ NOUVEAU

Consumer-driven contracts
Provider verification


Tests E2E avec scénarios métier
Tests de charge : k6, JMeter, Gatling
Tests de sécurité : ZAP automation, Burp Suite
Test data management et anonymisation
💡 Mini-exercice (20min) : Écrire des tests de contrats pour une API

APRÈS-MIDI (13h30-17h) - PRATIQUE
13h30-15h : Documentation OpenAPI Complète ⭐ AMÉLIORÉ

📚 Génération automatique depuis le code (annotations)
Enrichissement manuel :

Descriptions détaillées
Exemples multiples (success, errors)
Try-it-out fonctionnel


Versioning : doc pour v1 et v2 côte à côte
Guides d'intégration :

Quick start (5 min)
Authentication flow
Error handling
Best practices


Changelog automatique depuis Git

15h-15h15 : ☕ Pause
15h15-16h45 : Suite de Tests Exhaustive ⭐ AMÉLIORÉ

🧪 Tests multi-niveaux :

Unitaires (>80% coverage target)
Intégration avec DB test
Tests de contrats entre services ⭐ NOUVEAU
E2E avec scénarios utilisateur
Tests de charge : simulation 1000 users/sec
Tests de sécurité automatisés


CI/CD avancé :

Tests sur Pull Request
Code review automatique (SonarQube)
Blocage si coverage < 70%
Déploiement automatique si tests OK



16h45-17h : Documentation Challenge ⭐ NOUVEAU

📖 Swap entre groupes :

Groupe A doit intégrer l'API du groupe B uniquement avec la doc
Feedback sur clarté et complétude


Review croisée et amélioration

📦 Livrable J6 :

Documentation OpenAPI exhaustive avec guides
Suite de tests complète (>80% coverage)
Tests de contrats entre services
CI/CD avec qualité gates
Rapport de charge testing
Front: doc intégration (Quick Start) et storybook de composants connectés aux mocks


JOUR 7 : Observabilité, Monitoring & Architecture Distribuée
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h15 : Daily + Observability Discussion
9h15-10h45 : Observabilité Moderne ⭐ AMÉLIORÉ

📊 Les 3 piliers en détail :

Logs : Structured logging (JSON), niveaux, contexte
Metrics : RED (Rate/Errors/Duration) & USE (Utilization/Saturation/Errors)
Traces : Distributed tracing, spans, context propagation


OpenTelemetry : standard unifié
Correlation : trace IDs à travers les services
Dashboards : design efficace, golden signals
Alerting : SLI, SLO, SLA, error budgets
Incident response : on-call, post-mortems
💡 Mini-exercice (20min) : Créer un dashboard pour monitorer une API (paper design)

10h45-11h : ☕ Pause
11h-12h30 : API Gateway & Architecture Distribuée ⭐ AMÉLIORÉ

🏗️ API Gateway pattern :

Responsabilités centralisées
Routing intelligent
Rate limiting global
Authentication/Authorization
Response caching
Request/response transformation
Analytics et logging


Service Mesh (Istio, Linkerd) :

Sidecar pattern
mTLS automatique
Traffic management
Observability intégrée


Load Balancing : Round-robin, Least connections, Sticky sessions
Service Discovery : DNS, Consul, Eureka
Resilience patterns :

Circuit Breaker (états, thresholds)
Bulkhead isolation
Retry avec jitter
Timeout cascades


💡 Mini-exercice (15min) : Dessiner une architecture résiliente pour 10 microservices

APRÈS-MIDI (13h30-17h) - PRATIQUE
13h30-15h : Instrumentation Complète ⭐ AMÉLIORÉ

📈 Logs structurés :

Format JSON avec contexte
Corrélation IDs partout
Niveaux appropriés
Redaction de données sensibles


Métriques custom :

Business metrics (registrations, bookings)
Technical metrics (response time, DB queries)
Export Prometheus format


Distributed Tracing : ⭐ NOUVEAU

OpenTelemetry instrumentation
Spans et traces
Jaeger/Zipkin visualization


Health checks :

Liveness (app alive?)
Readiness (ready for traffic?)
Startup probes



15h-15h15 : ☕ Pause
15h15-16h45 : API Gateway & Debugging Distribué ⭐ NOUVEAU

🚪 Implémentation API Gateway (Kong, KrakenD, ou custom)
Configuration du routing
Centralisation auth et rate limiting
Response caching
Dashboard Grafana avec métriques réelles
Exercice de debugging distribué :

Scénario : timeout intermittent
Utiliser traces distribuées pour identifier le service coupable
Analyser les métriques pour comprendre la cause racine



16h45-17h : Observability Review

🗺️ Architecture finale avec tous les points d'observation
Discussion sur les trade-offs
SLO definition pour SmartCity

📦 Livrable J7 :

Logs structurés avec correlation
Métriques Prometheus + dashboard Grafana
Distributed tracing opérationnel
API Gateway configuré
Health checks complets


JOUR 8 : GraphQL, Real-time & gRPC
MATIN (9h-12h30) - THÉORIE + EXERCICES
9h-9h15 : Daily + Sprint Planning #4
9h15-10h15 : GraphQL Approfondi ⭐ AMÉLIORÉ

🔄 Problèmes que GraphQL résout (over/under-fetching)
Schema Definition Language (SDL)
Type system : scalars, objects, interfaces, unions, enums
Queries, mutations, subscriptions
Resolver pattern et exécution
DataLoader : solution au N+1 problem ⭐ FOCUS
Pagination : connections et edges
Federation : distributed GraphQL
Comparaison REST vs GraphQL : tableau décisionnel
💡 Mini-exercice (15min) : Schéma GraphQL pour un blog

10h15-10h30 : ☕ Pause
10h30-11h30 : Communication Real-time ⭐ AMÉLIORÉ

🔴 Comparaison des technologies :

Long Polling (simple mais inefficace)
Server-Sent Events (unidirectionnel)
WebSockets (bidirectionnel full-duplex)
WebRTC (peer-to-peer)


WebSocket protocol en détail
Scalabilité : sticky sessions, Redis pub/sub
Reconnection et heartbeat
Use cases : chat, notifications, live updates
💡 Mini-exercice (15min) : Choisir la bonne techno pour 4 scénarios

11h30-12h30 : gRPC & Performance ⭐ AMÉLIORÉ

⚡ Protocol Buffers : sérialisation binaire
Avantages : performance, type safety, code generation
gRPC architecture : client-server, bidirectionnel
Streaming :

Unary (simple request-response)
Server streaming
Client streaming
Bidirectional streaming


Use cases : IoT, microservices internes, mobile
Comparaison performance REST vs gRPC (benchmarks)
💡 Mini-exercice (10min) : Définir un .proto file

APRÈS-MIDI (13h30-17h) - PRATIQUE
13h30-15h : API GraphQL avec DataLoader ⭐ AMÉLIORÉ

🛠️ Setup Apollo Server ou similar
Schema complet pour SmartCity
Resolvers pour queries et mutations
DataLoader implementation : ⭐ FOCUS

Batching des requêtes
Caching per-request
Résolution du N+1
Monitoring des performances


Playground GraphQL interactif
Tests : queries, mutations, error handling
Comparaison perfs : GraphQL vs REST equivalent

15h-15h15 : ☕ Pause
15h15-16h30 : Real-time & gRPC ⭐ AMÉLIORÉ

🚌 Service WebSocket : positions GPS des bus en temps réel

Rooms par ligne de bus
Broadcasting efficace
Reconnection handling
Rate limiting des messages


Simulateur IoT : devices envoyant des données
Service gRPC : ⭐ NOUVEAU

Communication ultra-rapide entre microservices internes
Streaming bidirectionnel pour IoT devices
Benchmark de performance


Subscriptions GraphQL pour notifications

16h30-17h : 🏁 Sprint Review #4 + Demo

API GraphQL complète
Démo real-time : carte avec bus en mouvement
Performance comparison entre technologies
Discussion use cases appropriés

📦 Livrable J8 :

API GraphQL avec DataLoader (N+1 résolu)
Service WebSocket real-time
Service gRPC pour microservices
Benchmarks de performance
Documentation des trade-offs


JOUR 9 : Production, DevOps & Finalisation
MATIN (9h-12h30) - THÉORIE + FINALISATION
9h-9h15 : Daily + Sprint Final #5 Planning
9h15-10h30 : Containerisation & Orchestration ⭐ AMÉLIORÉ

🐳 Docker best practices :

Multi-stage builds pour réduire taille
Layer caching optimization
Security scanning (Trivy, Snyk)
Non-root users
.dockerignore
Health checks dans Dockerfile


Docker Compose : orchestration dev/test
Kubernetes essentials :

Pods, Services, Deployments
ConfigMaps & Secrets
Ingress controllers
Horizontal Pod Autoscaling
Resource limits et requests


Helm charts : templating et réutilisabilité

10h30-10h45 : ☕ Pause
10h45-11h45 : CI/CD Avancé & Deployment Strategies ⭐ AMÉLIORÉ

🚀 Pipeline complet :

Build → Test → Security Scan → Deploy
Pull Request workflow : ⭐ FOCUS

Tests automatiques sur PR
Code review obligatoire
Merge checks (tests, coverage, security)
Preview environments par PR


Environnements : dev, staging, prod


Deployment strategies :

Blue-Green : switch instantané
Canary : déploiement progressif (5% → 50% → 100%)
Rolling updates : zero downtime
Feature flags (LaunchDarkly, Unleash)


Rollback automatique si health check fail
Infrastructure as Code (Terraform concepts)

11h45-12h30 : Scalabilité & Performance Production ⭐ AMÉLIORÉ

📈 Scaling strategies :

Horizontal (plus d'instances) vs Vertical (bigger instances)
Auto-scaling basé sur métriques (CPU, latency, custom)
Warm-up et cooldown periods


Caching distribué : Redis Cluster
Database scaling :

Read replicas
Connection pooling optimal
Query optimization
Sharding strategies


CDN pour assets statiques
Géo-distribution et latence

APRÈS-MIDI (13h30-17h) - FINALISATION & SOUTENANCES
13h30-14h00 : Dockerisation Complète

✨ Docker Compose multi-services
Configuration production-ready
Secrets management
Health checks everywhere

14h00-15h00 : CI/CD Production & Final Touches

🚀 Pipeline GitHub Actions / GitLab CI complet avec :

Tests multi-niveaux
Security scanning (SAST, dependencies, secrets)
Code quality gates (SonarQube)
Auto-deploy sur succès
Canary deployment simulation


Derniers ajustements :

Bug fixes critiques
Documentation finale
README professionnel
Architecture diagrams



15h00-15h15 : ☕ Pause + Setup Salle de Soutenance
15h15-17h : 🎓 SOUTENANCES FINALES
Format par groupe (20 minutes) :
1. Démo Live (10 minutes)

Scénario utilisateur complet
Fonctionnalités
RéessayerTTContinuerclés en action

Performance et scalabilité démontrées
Sécurité et résilience illustrées

2. Présentation Technique (7 minutes)

Architecture globale et choix techniques
Patterns et best practices implémentés
Challenges rencontrés et solutions
Métriques clés (performance, qualité, sécurité)
Stack technologique et justifications

3. Q&A Jury (3 minutes)

Questions techniques approfondies
Défense des choix architecturaux
Discussion sur les trade-offs

Critères d'Évaluation (100 points) :
Fonctionnalités & Complexité (25 points)

Complétude des services (10)
Qualité des implémentations (10)
Innovation et créativité (5)

Architecture & Design (20 points)

Qualité de l'architecture (8)
Patterns et best practices (7)
Scalabilité et résilience (5)

Sécurité (15 points)

Authentification/Autorisation (6)
Protection contre OWASP Top 10 (5)
Secrets management (4)

Qualité & Tests (15 points)

Coverage et qualité des tests (8)
Tests de contrats (4)
Tests de performance/sécurité (3)

Documentation (10 points)

OpenAPI complète (5)
Guides d'intégration (3)
README et architecture (2)

DevOps & Production (10 points)

CI/CD complet (5)
Dockerisation (3)
Monitoring et observabilité (2)

Présentation (5 points)

Clarté et structure (3)
Démo efficace (2)

17h-17h30 : Clôture & Certificats 🎉

Annonce des résultats et feedback
Remise des "badges" :

🏆 Best Architecture
🔒 Security Champion
📚 Documentation Master
⚡ Performance Guru
🎨 Innovation Award


Rétrospective collective du cours
Ressources pour continuer l'apprentissage
Photo de groupe et célébration

📦 Livrable Final Complet :

Code : Repository Git avec historique propre
Documentation :

OpenAPI specs complètes pour tous les services
README professionnel avec architecture diagrams
Guides d'intégration (Quick Start, Auth Flow, Error Handling)
ADR (Architecture Decision Records) pour choix importants


Tests : Suite complète avec >80% coverage
Infrastructure :

Docker Compose pour développement
Dockerfiles optimisés
CI/CD pipeline fonctionnel


Monitoring :

Dashboard Grafana avec métriques essentielles
Distributed tracing configuré
Alerting rules définies


Sécurité :

Rapport d'audit OWASP ZAP
Documentation des mesures de sécurité
Secrets management


Présentation :

Slides de soutenance (PDF)
Vidéo de démo (5 min, optionnel mais valorisé)


Post-Mortem :

Document de rétrospective (3-5 pages)
Lessons learned
Améliorations futures




📊 Répartition Optimisée Théorie/Pratique
JourThéorieMini-ExercicesPratiqueAgile/ReviewJ12h4530min3h1530minJ22h3035min3h30minJ32h4535min3h15minJ42h3035min3h30-J52h3035min3h30minJ62h4535min3h1530minJ72h4535min3h1530minJ82h3035min3h30minJ92h45-3h1h15Total23h154h2028h154h30
Note : Mini-exercices intégrés pendant les sessions théoriques maintiennent l'attention et l'engagement

🎯 Livrables par Sprint (Approche Agile)
Sprint 1 (J1-J2) - Foundation
Goal : APIs REST basiques avec optimisation performance

Events API + Transport API
Gestion d'erreurs standardisée
Caching multi-niveaux (Redis + HTTP headers)
Tests unitaires + Postman collection
Demo : APIs fonctionnelles avec benchmarks performance

Sprint 2 (J3-J4) - Integration & Security Foundation
Goal : Intégration multi-protocoles et sécurité de base

Weather Service (SOAP/REST adapter)
Résilience patterns (circuit breaker, retry)
Auth Service complet (JWT)
RBAC et protection des endpoints
Audit de sécurité OWASP
Demo : Système d'auth + services résilients

Sprint 3 (J5-J6) - Security & Quality
Goal : Sécurité avancée et qualité professionnelle

OAuth 2.0 avec social login
Hardening complet (rate limiting, headers, validation)
Documentation OpenAPI exhaustive
Suite de tests complète (unitaires, intégration, contrats, E2E)
CI/CD avec quality gates
Demo : Système sécurisé de bout en bout + documentation

Sprint 4 (J7-J8) - Advanced Architecture
Goal : Observabilité et alternatives architecturales

Observabilité complète (logs, metrics, traces)
API Gateway
GraphQL API avec DataLoader
Service real-time WebSocket
Service gRPC pour microservices internes
Demo : Architecture distribuée observable + alternatives REST

Sprint 5 (J9) - Production Ready
Goal : Déploiement et finalisation

Dockerisation complète
CI/CD production-ready avec canary
Polissage et bug fixes
Documentation finale
Demo : Soutenance finale avec présentation professionnelle


🎓 Supports Pédagogiques & Ressources
Avant le Cours

Prérequis à réviser : HTTP, JSON, bases de données, Git
Installation checklist : IDE, Postman, Docker, Git, Redis
Lecture recommandée : "RESTful Web APIs" (Richardson & Ruby)

Pendant le Cours

Slides interactifs avec quiz intégrés (Mentimeter/Slido)
Code samples repository : templates et exemples
Cheat sheets : REST verbs, HTTP codes, JWT, OAuth flows
Decision trees : Quand utiliser REST/GraphQL/gRPC/WebSocket
Checklist OWASP : version imprimable
Architecture templates : diagrammes Miro/Excalidraw

Après Chaque Jour

Quiz de révision (5 min, Kahoot style)
Lecture complémentaire (articles, docs officielles)
Vidéos recommandées : talks de conférences

Ressources Continues

Discord/Slack : canal dédié pour questions
Stack technique recommandée :

Node.js : Express, Fastify
Python : FastAPI, Flask
Java : Spring Boot
Tools : Postman, Insomnia, k6, Redis, PostgreSQL


APIs publiques pour tests : GitHub, Stripe, Twilio, OpenWeather


🏆 Gamification & Engagement
Système de Points

Daily challenges (50 pts) : mini-défis techniques quotidiens
Code reviews (25 pts) : feedback constructif aux autres groupes
Security findings (100 pts) : trouver une vulnérabilité réelle
Performance optimization (75 pts) : améliorer latence >50%
Innovation bonus (150 pts) : fonctionnalité wow factor

Badges à Débloquer

🔒 Security Guardian : 0 vulnérabilités critiques
⚡ Speed Demon : API <100ms p95
📚 Doc Master : Documentation exemplaire
🧪 Test Champion : Coverage >90%
🎨 Innovation Award : Fonctionnalité la plus créative
🤝 Team Player : Meilleures code reviews
🐛 Bug Hunter : Trouver 5+ bugs dans d'autres projets
🚀 DevOps Hero : Pipeline CI/CD parfait

Challenges Hebdomadaires

Week 1 : API la plus performante (benchmark)
Week 2 : Système d'auth le plus robuste (pen test)
Mid-course : Easter egg hunt (fonctionnalités cachées à découvrir dans la doc)

Leaderboard

Classement affiché quotidiennement
Prix symboliques pour le podium final


🔧 Outils & Technologies (Stack Complète)
Développement

Languages : Node.js/TypeScript, Python, Java (choix libre)
Frameworks : Express, FastAPI, Spring Boot
Bases de données : PostgreSQL, MongoDB
Caching : Redis, Redis Cluster
Message Queue : RabbitMQ ou Kafka (concepts)

Documentation

OpenAPI : Swagger UI, Redoc, Stoplight
Diagrammes : Mermaid, Draw.io, Excalidraw

Tests

Unit : Jest, Pytest, JUnit
Integration : Supertest, TestContainers
E2E : Postman/Newman, REST Assured
Load : k6, Apache Bench, Gatling
Security : OWASP ZAP, Snyk

Observabilité

Logs : Winston, Pino (structured JSON)
Metrics : Prometheus, StatsD
Tracing : OpenTelemetry, Jaeger
Dashboards : Grafana
APM : Elastic APM (demo), New Relic (concepts)

Sécurité

Auth : Passport.js, Auth0, Keycloak
Secrets : dotenv, HashiCorp Vault (concepts)
Scanning : Trivy, Snyk, GitGuardian

DevOps

Containers : Docker, Docker Compose
Orchestration : Kubernetes (Minikube local)
CI/CD : GitHub Actions, GitLab CI
Quality : SonarQube, ESLint, Prettier
IaC : Terraform (introduction)

API Gateway

Kong, KrakenD, ou implémentation custom

Autres

Git : GitFlow ou trunk-based development
Project Management : Trello, Jira, ou GitHub Projects
Communication : Discord/Slack pour le projet


📝 Évaluation Détaillée
Évaluation Continue (40%)
1. Participation & Implication (10%)

Présence et ponctualité
Participation aux discussions
Entraide et collaboration
Daily stand-ups

2. Sprint Demos (15%)

5 démos × 3% chacune
Préparation et clarté
Fonctionnalités démontrées
Réponses aux questions

3. Livrables Intermédiaires (15%)

Qualité du code (clean code)
Respect des deadlines
Documentation incrémentale
Tests progressifs
Code reviews données

Projet Final (60%)
1. Code & Architecture (25%)

Qualité du code (10%)

Clean code principles
Design patterns appropriés
Pas de code smells


Architecture (10%)

Cohérence globale
Scalabilité
Résilience


Innovation (5%)

Fonctionnalités originales
Utilisation créative des technologies



2. Sécurité (15%)

Protection OWASP Top 10 (8%)
Auth/OAuth implémentation (4%)
Secrets management (3%)

3. Documentation (10%)

OpenAPI complète (5%)
Guides d'intégration (3%)
README et architecture (2%)

4. Tests & Qualité (10%)

Coverage >80% (4%)
Tests de contrats (3%)
Tests performance/sécurité (3%)

5. DevOps & Production (10%)

CI/CD fonctionnel (5%)
Dockerisation (3%)
Observabilité (2%)

6. Présentation & Démo (15%)

Démo live fonctionnelle (8%)
Présentation claire (4%)
Réponses aux questions (3%)

7. Travail d'Équipe (5%)

Collaboration efficace
Répartition équitable
Gestion Agile
Retrospectives

Bonus Possible (+10%)

Badges collectés
Challenges gagnés
Contributions exceptionnelles
Open source contribution (si applicable)


🎯 Objectifs d'Apprentissage Mesurables
À la fin du cours, les étudiants seront capables de :
Niveau Connaissance (Remember/Understand)
✅ Expliquer les principes REST et les contraintes de Fielding
✅ Différencier REST, SOAP, GraphQL et gRPC
✅ Comprendre OWASP API Security Top 10
✅ Décrire OAuth 2.0 et ses flows
Niveau Application (Apply)
✅ Concevoir une API RESTful respectant les best practices
✅ Implémenter JWT et OAuth 2.0
✅ Écrire des tests unitaires, d'intégration et E2E
✅ Créer une documentation OpenAPI complète
✅ Configurer un pipeline CI/CD basique
Niveau Analyse (Analyze)
✅ Auditer une API et identifier les vulnérabilités
✅ Analyser les performances et identifier les bottlenecks
✅ Comparer différentes architectures et choisir la plus appropriée
✅ Débugger des problèmes dans une architecture distribuée
Niveau Synthèse (Create)
✅ Concevoir une architecture microservices complète
✅ Développer une API production-ready sécurisée et scalable
✅ Implémenter observabilité complète (logs, metrics, traces)
✅ Créer un système résilient avec patterns appropriés
Niveau Évaluation (Evaluate)
✅ Évaluer les trade-offs entre différentes technologies
✅ Critiquer et améliorer des architectures existantes
✅ Justifier des choix techniques avec arguments solides
✅ Proposer des améliorations basées sur des métriques

📚 Plan de Continuité Post-Formation
Ressources pour Aller Plus Loin

Livres recommandés :

"Building Microservices" - Sam Newman
"Designing Data-Intensive Applications" - Martin Kleppmann
"Release It!" - Michael Nygard
"API Security in Action" - Neil Madden


Certifications suggérées :

AWS Certified Solutions Architect
Certified Kubernetes Administrator (CKA)
OWASP API Security Certification


Cours en ligne :

Microservices Architecture (Udemy/Coursera)
API Design Patterns (Pluralsight)
Security for Developers (OWASP)



Projets Personnels Suggérés

Contribuer à un projet open source avec des APIs
Créer un side-project avec architecture microservices
Participer à des bug bounty programs (HackerOne, Bugcrowd)
Développer un outil DevOps pour la communauté

Communautés à Rejoindre

API Design Community (Slack)
OWASP Local Chapter
Cloud Native Computing Foundation (CNCF)
Dev.to, Hashnode (partager ses apprentissages)


🚀 Améliorations Clés vs Version Initiale
✨ Nouveautés Majeures

🏃 Approche Agile Complète

Sprints structurés avec planning/review/retro
Story points et velocity tracking
Rôles tournants dans les équipes
Backlog management


💡 Mini-Exercices Interactifs

Intégrés pendant la théorie pour maintenir l'engagement
Cas pratiques immédiats
Gamification des apprentissages


⭐ Nouveaux Sujets Critiques

Gestion d'erreurs standardisée (RFC 7807)
Performance & caching en profondeur
Tests de contrats (consumer-driven)
Observabilité distribuée avec tracing
PR workflow et code review automation
OWASP ZAP automatisé dans CI/CD


🔍 Exercices Pratiques Avancés

Debugging distribué avec correlation IDs
Penetration testing workshop entre groupes
Documentation challenge (swap & integrate)
Performance benchmarking systématique
DataLoader pour résoudre N+1 en GraphQL


🛡️ Sécurité Renforcée

OWASP Top 10 détaillé avec exemples
Scanning automatique (OWASP ZAP, Snyk)
Pen testing pratique
Secrets management
Security testing dans CI/CD


📊 Qualité & Professionnalisation

Tests de contrats entre services
Quality gates dans CI/CD
SonarQube integration
Architecture Decision Records (ADR)
Post-mortem et retrospectives


🎯 Évaluation Améliorée

Critères détaillés et transparents
Badges et gamification
Feedback continu
Bonus pour innovation
