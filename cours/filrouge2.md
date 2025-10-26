🚀 PROJET FIL ROUGE - DEMI-JOURNÉE 2 (J2 Après-midi)
SmartCity API Platform - Implémentation & Mocks
Durée : 3h30 (13h30-17h00)
Contexte : Ce matin vous avez vu l'API REST avancé (versioning, pagination avancée, documentation OpenAPI). Cet après-midi, vous allez implémenter vos premiers endpoints fonctionnels avec des données en dur (tableaux en mémoire) et générer des mocks automatiques.

📋 RAPPEL DE VOTRE SITUATION (J1 → J2)
✅ Ce que vous avez (depuis J1)

Contrat OpenAPI avec 2-3 endpoints définis
Structure de projet initialisée
README avec roadmap
Repository Git avec commit initial
Stack technique choisie

🎯 Ce qu'on vise aujourd'hui (réaliste pour 3h30)
À 17h00, chaque équipe doit avoir :
ÉQUIPES BACK-END :

✅ 2-3 endpoints fonctionnels avec données en dur (tableaux)
✅ Pagination implémentée (page/limit)
✅ Gestion d'erreurs RFC 7807 centralisée
✅ Headers de corrélation (X-Correlation-Id)
✅ 3-5 tests unitaires qui passent
✅ Mock Prism généré et accessible

ÉQUIPE FRONT :

✅ 1-2 pages qui consomment des mocks Prism
✅ Gestion d'erreurs côté client
✅ Affichage des données avec pagination

ÉQUIPE AUTH :

✅ Endpoints register/login avec réponses mock
✅ Structure JWT définie (pas encore signés, juste la forme)
✅ Format d'erreurs d'auth standardisé

❌ CE QUI N'EST PAS ATTENDU AUJOURD'HUI

❌ Base de données connectée (J3)
❌ Authentification fonctionnelle avec vrais JWT (J3-J4)
❌ Tests exhaustifs (J6-J7)
❌ Cache Redis (J7)
❌ Validation complexe (J3)


🏁 PHASE 1 : DAILY & ALIGNEMENT (13h30-13h45) - 15 min
Daily Stand-up par équipe (10 min)
Chaque équipe fait son stand-up interne :

Ce qui a été fait depuis J1 (installs, lectures...)
Blockers éventuels
Plan pour aujourd'hui

Synchronisation inter-équipes (5 min)
Point rapide tous ensemble :

Équipes back : "Nos endpoints seront accessibles à quelle URL ?"
Front : "Quels services on consomme en premier ?"
Auth : "Format de token défini ?"

Convention d'URLs locales (à documenter) :
AUTH      → http://localhost:3001/api/v1
EVENTS    → http://localhost:3002/api/v1
TRANSPORT → http://localhost:3003/api/v1
SERVICE_3 → http://localhost:3004/api/v1
SERVICE_4 → http://localhost:3005/api/v1
FRONT     → http://localhost:3000

📦 PHASE 2 : DONNÉES EN DUR & MIDDLEWARE (13h45-15h00) - 75 min
A. Créer vos données en dur (20 min)
IMPORTANT : Pas de base de données aujourd'hui ! Utilisez des tableaux/listes en mémoire.
Exemple pour EVENTS
Structure recommandée :
javascript// src/data/events.js (Node.js)
const events = [
  {
    id: "550e8400-e29b-41d4-a716-446655440000",
    title: "Jazz sous les étoiles",
    type: "concert",
    description: "Soirée jazz en plein air avec des artistes locaux",
    startDate: "2025-07-15T20:00:00Z",
    endDate: "2025-07-15T23:30:00Z",
    location: {
      name: "Place du Capitole",
      address: "Place du Capitole",
      city: "Toulouse",
      coordinates: { lat: 43.6043, lng: 1.4437 }
    },
    capacity: 500,
    availableSeats: 320
  },
  {
    id: "660e8400-e29b-41d4-a716-446655440001",
    title: "Exposition Picasso",
    type: "exposition",
    description: "Rétrospective des œuvres de jeunesse",
    startDate: "2025-06-01T10:00:00Z",
    endDate: "2025-08-31T18:00:00Z",
    location: {
      name: "Musée des Augustins",
      address: "21 Rue de Metz",
      city: "Toulouse"
    }
  },
  // ... Ajoutez 20-30 événements pour tester la pagination
];

module.exports = events;
Même structure pour Python :
python# src/data/events.py
events = [
    {
        "id": "550e8400-e29b-41d4-a716-446655440000",
        "title": "Jazz sous les étoiles",
        "type": "concert",
        # ...
    },
    # ...
]
Même structure pour Java :
java// src/main/java/com/smartcity/data/EventsData.java
public class EventsData {
    private static final List<Event> events = Arrays.asList(
        new Event("550e8400-...", "Jazz sous les étoiles", "concert", ...),
        // ...
    );
    
    public static List<Event> getAll() {
        return new ArrayList<>(events);
    }
}
💡 Données pour les autres services
TRANSPORT - Lignes mock :
javascriptconst lines = [
  {
    id: "line-a-metro",
    name: "Ligne A - Métro",
    type: "metro",
    number: "A",
    status: "operational",
    color: "#FF0000"
  },
  {
    id: "line-b-metro",
    name: "Ligne B - Métro",
    type: "metro",
    number: "B",
    status: "operational",
    color: "#FFFF00"
  },
  // ... 10-15 lignes
];

const stops = [
  {
    id: "stop-capitole",
    name: "Capitole",
    address: "Place du Capitole",
    lat: 43.6043,
    lng: 1.4437
  },
  // ... 30-40 arrêts
];
TROC - Items mock :
javascriptconst items = [
  {
    id: "item-001",
    title: "Vélo de ville",
    category: "sports",
    condition: "good",
    description: "Vélo en bon état, peu utilisé",
    photos: ["https://example.com/bike1.jpg"],
    ownerId: "user-123",
    status: "available"
  },
  // ... 20-30 items
];
DÉPANNAGE - Providers mock :
javascriptconst providers = [
  {
    id: "provider-001",
    name: "Martin Plomberie",
    services: ["plumbing", "heating"],
    rating: 4.5,
    reviewsCount: 127,
    phone: "+33 6 12 34 56 78",
    available: true
  },
  // ... 15-20 prestataires
];
ALERTE - Alerts mock :
javascriptconst alerts = [
  {
    id: "alert-001",
    type: "fire",
    severity: "high",
    description: "Incendie signalé rue de la République",
    location: { lat: 43.6, lng: 1.44, address: "Rue de la République" },
    status: "open",
    reportedAt: "2025-01-15T14:30:00Z",
    reportedBy: "user-456"
  },
  // ... 20-30 alertes
];
PARKING - Parkings mock :
javascriptconst parkings = [
  {
    id: "parking-001",
    name: "Parking Victor Hugo",
    address: "Place Victor Hugo",
    lat: 43.6,
    lng: 1.45,
    totalSpaces: 450,
    availableSpaces: 127,
    hourlyPrice: 2.5,
    dailyPrice: 15,
    type: "covered"
  },
  // ... 10-15 parkings
];
BIBLIOTHÈQUE - Books mock :
javascriptconst books = [
  {
    id: "book-001",
    isbn: "978-2-1234-5680-3",
    title: "Le Seigneur des Anneaux",
    author: "J.R.R. Tolkien",
    category: "fantasy",
    publishedYear: 1954,
    totalCopies: 5,
    availableCopies: 2
  },
  // ... 50-100 livres
];

B. Middleware de gestion d'erreurs RFC 7807 (25 min)
Objectif : Centraliser la génération d'erreurs standardisées
1. Fonction utilitaire pour créer des erreurs
Pseudo-code (adapter à votre langage) :
javascript// src/utils/problem-details.js

function createProblem(req, res, status, type, title, detail, extensions = {}) {
  const correlationId = req.headers['x-correlation-id'] || generateUUID();
  
  const problem = {
    type: `https://smartcity.api/errors/${type}`,
    title,
    status,
    detail,
    instance: req.originalUrl || req.path,
    correlationId,
    ...extensions
  };
  
  res.status(status)
     .type('application/problem+json')
     .json(problem);
}

// Raccourcis pratiques
function problem404(req, res, detail = "Resource not found") {
  return createProblem(req, res, 404, "not-found", "Not Found", detail);
}

function problem422(req, res, detail, errors = {}) {
  return createProblem(req, res, 422, "validation-error", "Validation Failed", detail, { errors });
}

function problem400(req, res, detail = "Bad request") {
  return createProblem(req, res, 400, "bad-request", "Bad Request", detail);
}

module.exports = { createProblem, problem404, problem422, problem400 };
2. Middleware de corrélation
javascript// src/middleware/correlation.js

function correlationMiddleware(req, res, next) {
  const correlationId = req.headers['x-correlation-id'] || generateUUID();
  req.correlationId = correlationId;
  res.setHeader('X-Correlation-Id', correlationId);
  next();
}

module.exports = correlationMiddleware;
3. Intégration dans votre app
javascript// src/app.js (ou équivalent)

const correlationMiddleware = require('./middleware/correlation');

// Appliquer le middleware à toutes les routes
app.use(correlationMiddleware);

// Vos routes...

C. Implémenter les endpoints avec pagination (30 min)
Endpoint GET /resources (liste paginée)
Checklist d'implémentation :

✅ Récupérer paramètres page et limit de la query string
✅ Valeurs par défaut : page=1, limit=20
✅ Valider : page >= 1, limit entre 1 et 100
✅ Calculer offset : (page - 1) * limit
✅ Extraire la tranche de données du tableau en mémoire
✅ Retourner format standardisé

Format de réponse attendu :
json{
  "data": [...],
  "page": 1,
  "limit": 20,
  "total": 156
}
Pseudo-code (multi-langage) :
// Importer vos données
IMPORT events FROM './data/events'

FUNCTION getEvents(request, response):
  // 1. Extraire et valider params
  page = parseInt(request.query.page) || 1
  limit = parseInt(request.query.limit) || 20
  
  IF page < 1:
    RETURN problem422(request, response, "page must be >= 1")
  
  IF limit < 1 OR limit > 100:
    RETURN problem422(request, response, "limit must be between 1 and 100")
  
  // 2. Pagination sur le tableau en mémoire
  offset = (page - 1) * limit
  paginatedData = events.slice(offset, offset + limit)
  
  // 3. Réponse standardisée
  RETURN response.status(200).json({
    data: paginatedData,
    page: page,
    limit: limit,
    total: events.length
  })
Exemple concret Node.js/Express :
javascript// src/routes/events.js
const express = require('express');
const router = express.Router();
const events = require('../data/events');
const { problem422 } = require('../utils/problem-details');

router.get('/events', (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 20;
  
  if (page < 1) {
    return problem422(req, res, "page must be >= 1");
  }
  
  if (limit < 1 || limit > 100) {
    return problem422(req, res, "limit must be between 1 and 100");
  }
  
  const offset = (page - 1) * limit;
  const paginatedData = events.slice(offset, offset + limit);
  
  res.json({
    data: paginatedData,
    page,
    limit,
    total: events.length
  });
});

module.exports = router;
Endpoint GET /resources/{id} (détail)
Checklist :

✅ Extraire id du path
✅ Chercher dans le tableau en mémoire
✅ Retourner 404 si non trouvé
✅ Retourner la ressource si trouvée

Pseudo-code :
FUNCTION getEventById(request, response):
  id = request.params.id
  
  // Chercher dans le tableau
  event = events.find(e => e.id === id)
  
  IF NOT event:
    RETURN problem404(request, response, "Event not found")
  
  RETURN response.status(200).json(event)
Exemple Node.js :
javascriptrouter.get('/events/:id', (req, res) => {
  const { id } = req.params;
  
  const event = events.find(e => e.id === id);
  
  if (!event) {
    return problem404(req, res, "Event not found");
  }
  
  res.json(event);
});

☕ Pause (15h00-15h15) - 15 min

🧪 PHASE 3 : TESTS & MOCKS PRISM (15h15-16h15) - 60 min
A. Tests unitaires basiques (30 min)
Objectif : 3-5 tests pour valider le comportement de base
Tests à écrire (priorisés)
Pour GET /resources :

✅ Retourne 200 avec données paginées
✅ Respecte les paramètres page/limit
✅ Retourne 422 si page < 1
✅ Retourne 422 si limit > 100

Pour GET /resources/{id} :

✅ Retourne 200 avec la ressource si elle existe
✅ Retourne 404 si ressource inexistante

Exemple de structure de test (Node.js/Jest)
javascript// tests/events.test.js
const request = require('supertest');
const app = require('../src/app');

describe('GET /api/v1/events', () => {
  test('should return paginated events with default params', async () => {
    const response = await request(app)
      .get('/api/v1/events');
    
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('data');
    expect(response.body).toHaveProperty('page', 1);
    expect(response.body).toHaveProperty('limit', 20);
    expect(response.body).toHaveProperty('total');
    expect(Array.isArray(response.body.data)).toBe(true);
  });
  
  test('should respect custom page and limit', async () => {
    const response = await request(app)
      .get('/api/v1/events?page=2&limit=5');
    
    expect(response.status).toBe(200);
    expect(response.body.page).toBe(2);
    expect(response.body.limit).toBe(5);
    expect(response.body.data.length).toBeLessThanOrEqual(5);
  });
  
  test('should return 422 for invalid page', async () => {
    const response = await request(app)
      .get('/api/v1/events?page=0');
    
    expect(response.status).toBe(422);
    expect(response.body.type).toContain('validation-error');
    expect(response.body).toHaveProperty('correlationId');
  });
});

describe('GET /api/v1/events/:id', () => {
  test('should return event details for valid ID', async () => {
    const response = await request(app)
      .get('/api/v1/events/550e8400-e29b-41d4-a716-446655440000');
    
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('id');
    expect(response.body).toHaveProperty('title');
  });
  
  test('should return 404 for non-existent ID', async () => {
    const response = await request(app)
      .get('/api/v1/events/00000000-0000-0000-0000-000000000000');
    
    expect(response.status).toBe(404);
    expect(response.body.type).toContain('not-found');
  });
});
Pour Python/pytest :
python# tests/test_events.py
import pytest
from fastapi.testclient import TestClient
from src.main import app

client = TestClient(app)

def test_get_events_returns_paginated_results():
    response = client.get("/api/v1/events")
    assert response.status_code == 200
    data = response.json()
    assert "data" in data
    assert "page" in data
    assert "limit" in data
    assert "total" in data

def test_get_events_respects_custom_pagination():
    response = client.get("/api/v1/events?page=2&limit=5")
    assert response.status_code == 200
    data = response.json()
    assert data["page"] == 2
    assert data["limit"] == 5

def test_get_events_returns_422_for_invalid_page():
    response = client.get("/api/v1/events?page=0")
    assert response.status_code == 422
    assert "validation-error" in response.json()["type"]

def test_get_event_by_id_returns_details():
    response = client.get("/api/v1/events/550e8400-e29b-41d4-a716-446655440000")
    assert response.status_code == 200
    assert "id" in response.json()

def test_get_event_by_id_returns_404_when_not_found():
    response = client.get("/api/v1/events/00000000-0000-0000-0000-000000000000")
    assert response.status_code == 404

B. Génération Mock Prism (30 min)
Prism = Serveur mock généré automatiquement depuis votre OpenAPI
1. Installation de Prism
bash# Avec npm (global)
npm install -g @stoplight/prism-cli

# Ou avec npx (sans install)
npx @stoplight/prism-cli mock contracts/openapi.yaml

# Ou avec Docker
docker pull stoplight/prism
2. Lancer le mock Prism
bash# Dans le dossier de votre service
prism mock contracts/openapi.yaml --port 4010

# Vous verrez :
# [CLI] …  awaiting  Starting Prism…
# [CLI] ℹ  info      GET http://127.0.0.1:4010/api/v1/events
# [CLI] ℹ  info      GET http://127.0.0.1:4010/api/v1/events/{id}
3. Tester le mock
bash# Depuis un autre terminal
curl http://localhost:4010/api/v1/events

# Retourne un exemple généré depuis votre contrat OpenAPI
{
  "data": [
    {
      "id": "497f6eca-6276-4993-bfeb-53cbbbba6f08",
      "title": "string",
      "type": "concert"
    }
  ],
  "page": 1,
  "limit": 20,
  "total": 0
}
4. Publier le mock pour le Front
Option 1 : Laisser Prism tourner en local
bash# Dans un terminal dédié (tmux/screen ou onglet séparé)
prism mock contracts/openapi.yaml --port 4010

# Le Front accède à : http://localhost:4010/api/v1
Option 2 : Script npm
json// package.json
{
  "scripts": {
    "dev": "node src/server.js",
    "mock": "prism mock contracts/openapi.yaml --port 4010",
    "test": "jest"
  }
}
Mettre à jour votre README :
markdown## Modes de développement

### Mode Normal (avec serveur réel)
```bash
npm run dev
# API disponible sur http://localhost:3002
```

### Mode Mock (avec Prism)
```bash
npm run mock
# Mock API disponible sur http://localhost:4010
# Utile pour le Front quand le back n'est pas prêt
```
5. Coordination avec le Front
Convention d'URLs pour les mocks :
# Mocks Prism (générés automatiquement)
AUTH      → http://localhost:4010
EVENTS    → http://localhost:4020
TRANSPORT → http://localhost:4030
SERVICE_3 → http://localhost:4040

# Serveurs réels (implémentés par vous)
AUTH      → http://localhost:3001/api/v1
EVENTS    → http://localhost:3002/api/v1
TRANSPORT → http://localhost:3003/api/v1
SERVICE_3 → http://localhost:3004/api/v1
L'équipe Front peut basculer facilement :
javascript// front/src/config/api.js
const USE_MOCKS = process.env.REACT_APP_USE_MOCKS === 'true';

const API_URLS = {
  events: USE_MOCKS ? 'http://localhost:4020' : 'http://localhost:3002/api/v1',
  transport: USE_MOCKS ? 'http://localhost:4030' : 'http://localhost:3003/api/v1',
  // ...
};

🎨 PHASE 4 : INTÉGRATION FRONT (15h15-16h15) - Parallel
Cette phase est spécifique à l'équipe FRONT (pendant que les backs font les tests/mocks)
A. Consommer les mocks Prism (30 min)
1. Configuration des URLs
javascript// front/src/config/api.js

// Pour l'instant, utiliser uniquement les mocks Prism
const API_BASE_URLS = {
  events: 'http://localhost:4020',
  transport: 'http://localhost:4030',
  auth: 'http://localhost:4010',
  // Ajoutez les autres services
};

export default API_BASE_URLS;
2. Créer un client API
javascript// front/src/api/client.js

import API_BASE_URLS from '../config/api';

function generateUUID() {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    const r = Math.random() * 16 | 0;
    const v = c === 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

export async function apiGet(service, endpoint, params = {}) {
  const baseUrl = API_BASE_URLS[service];
  const queryString = new URLSearchParams(params).toString();
  const url = `${baseUrl}${endpoint}${queryString ? '?' + queryString : ''}`;
  
  const response = await fetch(url, {
    headers: {
      'Accept': 'application/json',
      'X-Correlation-Id': generateUUID()
    }
  });
  
  if (!response.ok) {
    const problem = await response.json();
    throw new ApiError(problem);
  }
  
  return response.json();
}

class ApiError extends Error {
  constructor(problem) {
    super(problem.detail || 'API Error');
    this.problem = problem;
    this.status = problem.status;
  }
}

export { ApiError };
3. Client spécifique pour Events (exemple)
javascript// front/src/api/events.js

import { apiGet, ApiError } from './client';

export async function getEvents(page = 1, limit = 20) {
  return apiGet('events', '/api/v1/events', { page, limit });
}

export async function getEventById(id) {
  return apiGet('events', `/api/v1/events/${id}`);
}
B. Page de liste avec pagination (20 min)
Pseudo-code framework-agnostic :
COMPONENT EventsList:
  STATE:
    events = []
    page = 1
    limit = 20
    total = 0
    loading = false
    error = null
  
  ON_MOUNT:
    loadEvents()
  
  ASYNC FUNCTION loadEvents():
    loading = true
    error = null
    
    TRY:
      result = AWAIT getEvents(page, limit)
      events = result.data
      total = result.total
    CATCH error:
      error = error.message
    FINALLY:
      loading = false
  
  FUNCTION nextPage():
    IF page * limit < total:
      page = page + 1
      loadEvents()
  
  FUNCTION prevPage():
    IF page > 1:
      page = page - 1
      loadEvents()
  
  RENDER:
    <div>
      IF loading:
        <Spinner />
      ELSE IF error:
        <ErrorDisplay error={error} />
      ELSE:
        <EventsGrid events={events} />
        <Pagination 
          page={page} 
          total={total} 
          limit={limit}
          onNext={nextPage}
          onPrev={prevPage}
        />
    </div>
C. Gestion d'erreurs côté client (10 min)
javascript// front/src/components/ErrorDisplay.jsx

export function ErrorDisplay({ error, onRetry }) {
  // Si c'est une erreur RFC 7807
  if (error?.problem) {
    return (
      <div className="error-card bg-red-50 border border-red-200 p-4 rounded">
        <h3 className="font-bold text-red-800">{error.problem.title}</h3>
        <p className="text-red-700">{error.problem.detail}</p>
        {error.problem.correlationId && (
          <small className="text-red-600">
            Correlation ID: {error.problem.correlationId}
          </small>
        )}
        {onRetry && (
          <button onClick={onRetry} className="mt-2 btn btn-secondary">
            Réessayer
          </button>
        )}
      </div>
    );
  }
  
  // Erreur générique
  return (
    <div className="error-card">
      <p>{error?.message || 'Une erreur est survenue'}</p>
      {onRetry && <button onClick={onRetry}>Réessayer</button>}
    </div>
  );
}

🔐 PHASE 5 : ÉQUIPE AUTH SPÉCIFIQUE (15h15-16h15) - Parallel
Pendant que les autres font tests/front, l'équipe AUTH prépare la structure JWT
A. Définir le format JWT (mock) - 20 min
Aujourd'hui, pas encore de vraie signature, juste définir la structure
Structure du token (claims standards)
json{
  "sub": "user-123",
  "email": "alice@example.com",
  "name": "Alice Dupont",
  "role": "user",
  "iat": 1704067200,
  "exp": 1704070800
}
Claims expliqués :

sub (subject) : ID de l'utilisateur
email : Email de l'utilisateur
name : Nom complet
role : Rôle (user/admin)
iat (issued at) : Timestamp de création
exp (expiration) : Timestamp d'expiration (1h par défaut)

B. Endpoints avec réponses mock - 25 min
POST /auth/register (mock)
javascript// src/routes/auth.js

// Tableau en mémoire pour stocker les utilisateurs (temporaire J2)
const users = [];

router.post('/auth/register', (req, res) => {
  const { email, password, name } = req.body;
  
  // Validation basique
  if (!email || !password) {
    return problem422(req, res, "Email and password are required");
  }
  
  // Vérifier si email existe déjà
  const existing = users.find(u => u.email === email);
  if (existing) {
    return problem409(req, res, "Email already registered");
  }
  
  // Créer l'utilisateur (mot de passe en clair pour l'instant, hash J3)
  const user = {
    id: generateUUID(),
    email,
    password, // ⚠️ TEMPORAIRE ! Hash avec bcrypt J3
    name,
    role: 'user',
    createdAt: new Date().toISOString()
  };
  
  users.push(user);
  
  // Réponse (ne pas retourner le password !)
  res.status(201).json({
    id: user.id,
    email: user.email,
    name: user.name,
    role: user.role
  });
});
POST /auth/login (mock avec JWT non signé)
javascriptrouter.post('/auth/login', (req, res) => {
  const { email, password } = req.body;
  
  // Validation
  if (!email || !password) {
    return problem422(req, res, "Email and password are required");
  }
  
  // Trouver l'utilisateur
  const user = users.find(u => u.email === email);
  if (!user) {
    return problem401(req, res, "Invalid credentials");
  }
  
  // Vérifier le mot de passe (simple comparaison pour J2, bcrypt J3)
  if (user.password !== password) {
    return problem401(req, res, "Invalid credentials");
  }
  
  // Créer un "faux JWT" (structure correcte mais pas signé)
  // On signera vraiment avec JWT library J3
  const now = Math.floor(Date.now() / 1000);
  
  const accessTokenPayload = {
    sub: user.id,
    email: user.email,
    name: user.name,
    role: user.role,
    iat: now,
    exp: now + 3600 // 1 heure
  };
  
  const refreshTokenPayload = {
    sub: user.id,
    type: 'refresh',
    iat: now,
    exp: now + (7 * 24 * 3600) // 7 jours
  };
  
  // Encoder en base64 pour simuler un JWT (pas sécurisé, juste pour la forme)
  // Format JWT : header.payload.signature
  const fakeAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.' + 
                          Buffer.from(JSON.stringify(accessTokenPayload)).toString('base64') +
                          '.SIGNATURE_PLACEHOLDER';
  
  const fakeRefreshToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.' +
                           Buffer.from(JSON.stringify(refreshTokenPayload)).toString('base64') +
                           '.SIGNATURE_PLACEHOLDER';
  
  res.json({
    accessToken: fakeAccessToken,
    refreshToken: fakeRefreshToken,
    expiresIn: 3600,
    tokenType: 'Bearer',
    user: {
      id: user.id,
      email: user.email,
      name: user.name,
      role: user.role
    }
  });
});
POST /auth/refresh (mock)
javascriptrouter.post('/auth/refresh', (req, res) => {
  const { refreshToken } = req.body;
  
  if (!refreshToken) {
    return problem400(req, res, "Refresh token is required");
  }
  
  // Pour J2, on accepte n'importe quel token et on renvoie un nouveau
  // J3 : vérifier vraiment le token
  
  // Décoder le payload (simpliste)
  try {
    const parts = refreshToken.split('.');
    const payload = JSON.parse(Buffer.from(parts[1], 'base64').toString());
    
    const now = Math.floor(Date.now() / 1000);
    const newAccessTokenPayload = {
      sub: payload.sub,
      email: payload.email,
      role: payload.role,
      iat: now,
      exp: now + 3600
    };
    
    const newAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.' +
                          Buffer.from(JSON.stringify(newAccessTokenPayload)).toString('base64') +
                          '.SIGNATURE_PLACEHOLDER';
    
    res.json({
      accessToken: newAccessToken,
      expiresIn: 3600,
      tokenType: 'Bearer'
    });
  } catch (error) {
    return problem401(req, res, "Invalid refresh token");
  }
});
GET /users/me (mock)
javascriptrouter.get('/users/me', (req, res) => {
  // Pour J2, on simule sans vérifier le token
  // J4 : vérifier vraiment le token Authorization
  
  const authHeader = req.headers.authorization;
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    return problem401(req, res, "Missing or invalid authorization header");
  }
  
  // Simuler un utilisateur (J4 : extraire du token)
  const mockUser = {
    id: "user-123",
    email: "alice@example.com",
    name: "Alice Dupont",
    role: "user"
  };
  
  res.json(mockUser);
});
C. Documentation du format JWT - 15 min
Créer un document pour les autres équipes :
markdown# Auth Service - Guide d'intégration (J2)

## Format de réponse POST /auth/login
```json
{
  "accessToken": "eyJhbGci...",
  "refreshToken": "eyJhbGci...",
  "expiresIn": 3600,
  "tokenType": "Bearer",
  "user": {
    "id": "uuid",
    "email": "alice@example.com",
    "name": "Alice Dupont",
    "role": "user"
  }
}
```

## Structure du Access Token (JWT)

Quand décodé (utilisez jwt.io pour visualiser) :

### Header
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload
```json
{
  "sub": "user-id",
  "email": "alice@example.com",
  "name": "Alice Dupont",
  "role": "user",
  "iat": 1704067200,
  "exp": 1704070800
}
```

## Comment utiliser (pour les autres services)

### 1. Le Front obtient un token via /auth/login

### 2. Le Front envoie le token dans les requêtes
Authorization: Bearer eyJhbGci...

### 3. Les services back vérifient le token (J4)
⚠️ Pour J2-J3 : pas encore de vérification
⚠️ À partir de J4 : middleware de vérification fourni

## Erreurs possibles

**401 Unauthorized** - Token manquant, invalide ou expiré
```json
{
  "type": "https://smartcity.api/errors/unauthorized",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Invalid or expired token",
  "correlationId": "..."
}
```

**422 Validation Error** - Champs manquants
**409 Conflict** - Email déjà utilisé (register)

🎤 PHASE 6 : DÉMO & SYNCHRONISATION (16h15-17h00) - 45 min
Format de démo par équipe (5-6 min chacune)
ÉQUIPES BACK-END
1. Démo live des endpoints (3 min)
bash# Test avec curl ou Postman

# GET liste paginée
curl http://localhost:3002/api/v1/events

# GET avec pagination custom
curl "http://localhost:3002/api/v1/events?page=2&limit=5"

# GET détail
curl http://localhost:3002/api/v1/events/550e8400-e29b-41d4-a716-446655440000

# Erreur 404
curl http://localhost:3002/api/v1/events/00000000-0000-0000-0000-000000000000

# Erreur 422 (page invalide)
curl "http://localhost:3002/api/v1/events?page=0"
2. Montrer le format d'erreur RFC 7807 (1 min)

Afficher une réponse 404 ou 422
Montrer le correlationId
Montrer le Content-Type: application/problem+json

3. Montrer les tests (1 min)
bashnpm test
# ou pytest
# ou mvn test

# Montrer que 3-5 tests passent au vert
4. Démonstration du mock Prism (1 min)
bash# Dans un terminal
prism mock contracts/openapi.yaml --port 4020

# Dans un autre terminal
curl http://localhost:4020/api/v1/events

# Expliquer : "Le Front peut utiliser ce mock pendant qu'on développe"
ÉQUIPE FRONT
1. Démo de la page de liste (2 min)

Ouvrir l'application dans le navigateur
Montrer la liste d'événements (consommée depuis Prism)
Naviguer entre les pages (pagination)
Afficher le nombre total d'éléments

2. Démo de la page de détail (1 min)

Cliquer sur un élément
Afficher le détail complet
Montrer le chargement (spinner)

3. Gestion d'erreurs (1 min)

Simuler une erreur (éteindre Prism ou URL invalide)
Montrer l'affichage de l'erreur avec le message RFC 7807
Montrer le bouton "Réessayer"

4. DevTools - Correlation ID (1 min)

Ouvrir DevTools → Network
Faire une requête
Montrer le header X-Correlation-Id dans les requêtes et réponses

ÉQUIPE AUTH
1. Démo des endpoints (3 min)
bash# Register
curl -X POST http://localhost:3001/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "alice@example.com",
    "password": "secure123",
    "name": "Alice Dupont"
  }'

# Réponse 201 avec user créé

# Login
curl -X POST http://localhost:3001/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "alice@example.com",
    "password": "secure123"
  }'

# Réponse avec accessToken et refreshToken

# Refresh
curl -X POST http://localhost:3001/api/v1/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{
    "refreshToken": "eyJhbGci..."
  }'

# GET /users/me avec token
curl http://localhost:3001/api/v1/users/me \
  -H "Authorization: Bearer eyJhbGci..."
2. Décodage du JWT (2 min)

Copier un accessToken de la réponse
Aller sur https://jwt.io
Coller le token
Montrer le payload décodé (sub, email, role, exp...)
Expliquer : "Pour l'instant ce n'est pas vraiment signé, on fera ça J3"

3. Erreurs d'authentification (1 min)
bash# Login avec mauvais password
curl -X POST http://localhost:3001/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "alice@example.com",
    "password": "wrong"
  }'

# Réponse 401 avec format RFC 7807

Discussion collective (15 min)
Points à valider tous ensemble :
1. Compatibilité des contrats (5 min)

Front : "Les réponses des mocks Prism correspondent à nos attentes ?"
Back : "Le format de pagination est bien le même partout ?"
Auth : "Le format du JWT est clair pour tout le monde ?"

2. Blockers identifiés (5 min)

Quels problèmes techniques avez-vous rencontrés ?
Quelles décisions restent à prendre ?
Besoin d'aide de l'enseignant pour débloquer ?

3. Préparation J3 (5 min)
Ce qui arrive demain :

Bases de données : Vous allez remplacer vos tableaux en mémoire par de vraies requêtes DB
Auth avec vrais JWT : Signature avec une clé secrète, hash bcrypt des passwords
CRUD complet : POST/PUT/DELETE en plus de GET
Validation : Valider les données en entrée avec des schémas

Questions à l'enseignant :

Quelle base de données recommandez-vous pour notre service ?
Comment gérer les migrations de schéma ?
Comment tester avec une vraie DB sans polluer les données ?


✅ CHECKLIST DE FIN DE JOURNÉE
Chaque équipe BACK vérifie :

 2-3 endpoints fonctionnels avec données en dur (tableaux)
 Pagination implémentée avec validation (page/limit)
 Middleware RFC 7807 en place et testé
 Header X-Correlation-Id propagé dans toutes les réponses
 3-5 tests unitaires qui passent (npm test ou équivalent)
 Mock Prism généré et accessible sur un port dédié
 README mis à jour avec :

 Commandes pour lancer le serveur (npm run dev)
 Commandes pour lancer les tests (npm test)
 Commandes pour lancer le mock (npm run mock)
 URL du mock Prism pour le Front


 Au moins 2 commits Git aujourd'hui
 Code formaté et sans erreurs de lint

Équipe FRONT vérifie :

 Configuration API avec URLs des mocks Prism
 1-2 pages qui affichent des données depuis les mocks
 Pagination côté UI (boutons Suivant/Précédent)
 Gestion d'erreurs avec affichage RFC 7807
 Correlation ID visible dans DevTools (Network tab)
 Spinner de chargement sur toutes les requêtes
 README avec instructions de lancement
 Au moins 2 commits Git aujourd'hui

Équipe AUTH vérifie :

 POST /auth/register implémenté avec validation
 POST /auth/login retourne un token (même si non signé)
 POST /auth/refresh implémenté
 GET /users/me implémenté
 Structure JWT documentée (claims, format)
 Erreurs d'auth standardisées (401, 422, 409)
 Document "Guide d'intégration" créé pour les autres équipes
 Tests basiques (3-5 tests)
 README avec exemples d'utilisation
 Au moins 2 commits Git aujourd'hui


📚 POUR DEMAIN (J3 Après-midi)
Théorie du matin (J3)

Sécurité API (Partie 1) : Authentification, JWT, OAuth 2.0
Hash de mots de passe (bcrypt)
Stratégies de stockage de tokens
Bonnes pratiques de sécurité

Pratique de l'après-midi (J3)
ÉQUIPES BACK (sauf Auth) :

Installer et configurer une base de données (PostgreSQL/MongoDB)
Créer le schéma/les migrations
Remplacer les tableaux en mémoire par des requêtes DB
Implémenter POST/PUT/DELETE (CRUD complet)
Ajouter validation des données entrantes (Joi/Zod/Pydantic)
Tests d'intégration avec DB

ÉQUIPE AUTH :

Créer la table users en base de données
Implémenter hash bcrypt pour les mots de passe
Générer de vrais JWT signés avec une clé secrète
Implémenter la vérification de tokens
Créer un middleware d'authentification exportable
Tests avec utilisateurs réels en DB

ÉQUIPE FRONT :

Basculer des mocks Prism vers les vrais services
Implémenter des formulaires de création (POST)
Gérer les erreurs de validation côté UI
Commencer l'intégration avec Auth (login/register)
Stocker le token (localStorage/sessionStorage)

À préparer ce soir (optionnel mais recommandé)
Pour toutes les équipes BACK :
bash# Installer votre SGBD
# PostgreSQL
docker run --name smartcity-postgres -e POSTGRES_PASSWORD=secret -p 5432:5432 -d postgres:15

# MongoDB
docker run --name smartcity-mongo -p 27017:27017 -d mongo:7

# Ou installer en local selon votre OS
Pour l'équipe AUTH :

Lire la documentation de votre bibliothèque JWT (jsonwebtoken pour Node, PyJWT pour Python, java-jwt pour Java)
Lire sur bcrypt et les work factors recommandés (12+)

Pour tous :

Lire sur les ORMs/ODMs de votre stack :

Node.js : Prisma, Sequelize, TypeORM, Mongoose
Python : SQLAlchemy, Django ORM, Tortoise ORM
Java : Hibernate, Spring Data JPA


Réfléchir au schéma de votre base de données


📈 PROGRESSION GLOBALE
J1 PM ✅ : Contrat OpenAPI + Structure projet
J2 PM ✅ : Endpoints mock + Tests + Prism
J3 PM 🎯 : Base de données + CRUD complet + JWT réels
J4 PM    : Protection endpoints + RBAC + Audit sécurité
J5 PM    : Intégration externe + Résilience + Communication
J6 PM    : API Gateway + Architecture distribuée
J7 PM    : Performance + Tests de charge + Observabilité
J8 PM    : Containerisation + CI/CD + Finalisation
Excellent travail aujourd'hui ! Vous avez des endpoints fonctionnels et des mocks pour débloquer le Front. 🚀

💡 RETOUR D'EXPÉRIENCE DE L'ENSEIGNANT
Ce qui a bien marché aujourd'hui
✅ Les équipes qui ont commencé par les données mock simples
✅ L'utilisation de Prism pour débloquer le Front immédiatement
✅ Les tests écrits en parallèle du code
✅ La coordination Front ↔ Back sur les formats de réponse
Points d'attention pour la suite
⚠️ Ne pas sous-estimer J3 : La connexion DB prend du temps, prévoyez des marges
⚠️ Commiter souvent : 3-4 commits minimum par demi-journée
⚠️ Documenter au fur et à mesure : Pas tout à la fin
⚠️ Tester avec des données réalistes : 20-30 items mock minimum pour tester la pagination
Conseils pour J3

Commencez par un schéma DB simple, vous pourrez l'enrichir plus tard
Utilisez des migrations versionnées dès le début
Créez une base de test séparée (smartcity_test)
L'équipe Auth est critique : les autres dépendent d'elle pour J4