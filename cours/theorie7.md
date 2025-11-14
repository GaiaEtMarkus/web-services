📚 JOUR 7 - MICROSERVICES ET ARCHITECTURE DISTRIBUÉE
Cours Théorique - Mastère 2 (Durée : 3h30)
🎯 OBJECTIFS PÉDAGOGIQUES
À la fin de cette journée, vous serez capables de :

Comprendre l'architecture microservices et ses différences avec le monolithe
Maîtriser le rôle et l'implémentation d'une API Gateway
Mettre en œuvre la découverte de services (Service Discovery)
Appliquer les patterns essentiels : Circuit Breaker et Saga


📖 PARTIE 1 : ARCHITECTURE MICROSERVICES (50 min)
1.1 Du monolithe aux microservices
Le monolithe : Simple mais limité
Qu'est-ce qu'un monolithe ?
Une application où tout le code est dans un seul projet, déployé comme une seule unité.
Application SmartCity Monolithe
    ┌─────────────────────────────────┐
    │  Un seul projet Node.js         │
    │  ├─ routes/events.js            │
    │  ├─ routes/users.js             │
    │  ├─ routes/payments.js          │
    │  ├─ routes/notifications.js     │
    │  └─ routes/auth.js              │
    │                                 │
    │  Une seule base de données      │
    │  Un seul déploiement            │
    │  Un seul serveur                │
    └─────────────────────────────────┘
Avantages du monolithe :

✅ Simple à développer : Un repo, une technologie
✅ Simple à déployer : npm start, c'est tout
✅ Simple à tester : Tests d'intégration faciles
✅ Performance : Pas d'appels réseau, tout est local
✅ Parfait pour démarrer : MVP, petites équipes (< 10 personnes)

Inconvénients à grande échelle :

❌ Scalabilité limitée : Scaler tout ou rien
❌ Déploiements risqués : Un bug dans Payments → toute l'app plante
❌ Équipes bloquées : Tout le monde travaille sur le même code
❌ Technologie figée : Difficile de changer de stack
❌ Build lent : 20-30 min pour un gros monolithe


L'histoire Netflix : La transition vers les microservices
2008 : Netflix est un monolithe Java. Tout fonctionne bien.
2009 : Corruption de la base de données. 3 jours d'indisponibilité totale.
Netflix perd des millions. Leçon : Un seul point de défaillance peut tout détruire.
2010-2012 : Netflix migre vers les microservices.

De 1 monolithe à 700+ microservices (2000+ en 2025)
Chaque service : une seule responsabilité
Résilience : Si Recommendations tombe, Streaming continue

Résultat : Netflix déploie 4000 fois par jour sans interruption de service.

Qu'est-ce qu'un microservice ?
Définition : Un service petit, autonome, avec une seule responsabilité, déployable indépendamment.
Les 5 caractéristiques clés :

Une seule responsabilité : Gestion des événements, point final
Base de données propre : Pas de DB partagée avec d'autres services
API bien définie : REST, gRPC, ou messaging
Déployable indépendamment : Sans toucher aux autres services
Équipe autonome : 2-8 personnes (Two Pizza Team)

Architecture SmartCity Microservices

┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│   Events    │   │    Users    │   │  Payments   │   │Notification │
│   Service   │   │   Service   │   │   Service   │   │   Service   │
│   :3001     │   │   :3002     │   │   :3003     │   │   :3004     │
│             │   │             │   │             │   │             │
│ DB Events   │   │  DB Users   │   │ DB Payment  │   │    Redis    │
└──────┬──────┘   └──────┬──────┘   └──────┬──────┘   └──────┬──────┘
       │                 │                 │                 │
       └─────────────────┴─────────────────┴─────────────────┘
                                │
                        ┌───────▼────────┐
                        │  API Gateway   │
                        │     :8080      │
                        └───────┬────────┘
                                │
                        ┌───────▼────────┐
                        │    Clients     │
                        │ (Web, Mobile)  │
                        └────────────────┘

1.2 Comparaison : Monolithe vs Microservices
CritèreMonolitheMicroservicesComplexité initiale✅ Simple❌ ComplexeTemps de setup1 jour1-2 semainesDéploiementUn seul (npm start)Multiple (Docker, K8s)ScalabilitéVerticale (+ RAM/CPU)Horizontale par serviceRésilience❌ Point unique de défaillance✅ Isolation des pannesÉquipesUne seuleÉquipes autonomes par serviceTechnologieUne stackLiberté par serviceBase de donnéesUne seuleUne par servicePerformance✅ Appels locaux rapides⚠️ Latence réseauDebugging✅ Facile (un seul log)❌ Difficile (logs distribués)Taille équipe< 10 personnes10-100+ personnesCoût infrastructure💰 Faible💰💰 Élevé

1.3 Quand utiliser les microservices ?
✅ Utilisez les microservices si :

Équipe > 15 personnes : Besoin d'autonomie
Application complexe : Multiples domaines métier distincts
Scale différencié : Events a 1000 req/s, Users a 10 req/s
Déploiements fréquents : Besoin de CI/CD avancé
Innovation technologique : Tester Go ou Rust sur un service
Haute disponibilité : Isolation des pannes critique

❌ Restez en monolithe si :

Startup/MVP : Rapidité > Architecture
Petite équipe (< 10 personnes) : Overhead trop important
Budget limité : Infrastructure microservices = $$
Domaine simple : Pas besoin de découper
Pas d'expertise DevOps : Nécessite Kubernetes, CI/CD, monitoring

Citation de Martin Fowler :

"Don't start with microservices. Start with a monolith and split when the pain is real."

Conseil pratique : Commencez monolithe, migrez vers microservices quand :

Le déploiement prend > 30 min
Les équipes se marchent dessus
Impossible de scaler finement
La base de données est saturée


1.4 Les défis des microservices
Défi 1 : Complexité opérationnelle
Problème : 50 services = 50 repos, 50 déploiements, 50 bases de données, 50 logs.
Solution :

Kubernetes : Orchestration automatique
Docker : Conteneurisation
CI/CD : GitLab CI, GitHub Actions, Jenkins
Infrastructure as Code : Terraform, Ansible

Défi 2 : Communication inter-services
Problème : Latence réseau. Service A → Service B → Service C = 300ms.
Solution :

Caching : Redis pour données fréquentes
Async : Message queues (RabbitMQ, Kafka)
API Gateway : Point d'entrée unique

Défi 3 : Données distribuées
Problème : Pas de transactions ACID entre services. Comment garantir la cohérence ?
Solution :

Saga Pattern : Transactions distribuées avec compensation
Eventual Consistency : Accepter la cohérence à terme
Event Sourcing : Historique complet des événements

Défi 4 : Monitoring et debugging
Problème : Une requête passe par 5 services. Où est le bug ?
Solution :

Distributed Tracing : Jaeger, Zipkin
Centralized Logging : ELK Stack (Elasticsearch, Logstash, Kibana)
Correlation ID : Un ID unique par requête

Exemple de Correlation ID :
javascript// API Gateway génère un ID
const correlationId = uuidv4();
req.headers['x-correlation-id'] = correlationId;

// Chaque service log avec cet ID
logger.info(`[${req.headers['x-correlation-id']}] Processing event`);

// Dans les logs, on peut tracer toute la requête
// [abc-123] Gateway: Received request
// [abc-123] Events Service: Fetching event
// [abc-123] Payment Service: Processing payment
// [abc-123] Notification Service: Sending email
```

---

### 1.5 Architecture cible SmartCity
```
┌─────────────────────────────────────────────────────────┐
│                     CLIENTS                             │
│              (Web, Mobile, Partenaires)                 │
└────────────────────┬────────────────────────────────────┘
                     │ HTTPS
        ┌────────────▼────────────┐
        │     API GATEWAY         │  :8080
        │  - Authentification     │
        │  - Rate Limiting        │
        │  - Routage              │
        └────────────┬────────────┘
                     │
         ┌───────────┼───────────┬───────────┐
         │           │           │           │
    ┌────▼────┐ ┌───▼────┐ ┌───▼────┐ ┌────▼────┐
    │ Events  │ │ Users  │ │Payment │ │  Notif  │
    │Service  │ │Service │ │Service │ │ Service │
    │:3001    │ │:3002   │ │:3003   │ │ :3004   │
    └────┬────┘ └───┬────┘ └───┬────┘ └────┬────┘
         │          │          │           │
    ┌────▼────┐┌───▼────┐┌───▼────┐  ┌───▼────┐
    │PostgreSQL││Postgres││Postgres│  │ Redis  │
    │DB Events ││DB Users││DBPaymt │  │        │
    └──────────┘└────────┘└────────┘  └────────┘
         │          │          │           │
         └──────────┼──────────┴───────────┘
                    │
            ┌───────▼────────┐
            │  MESSAGE QUEUE │
            │   (RabbitMQ)   │
            └────────────────┘
```

**Flux typique** : Créer un événement
```
1. Client POST /api/events → API Gateway
2. Gateway vérifie JWT → OK
3. Gateway route → Events Service :3001
4. Events Service crée l'événement en DB
5. Events Service publie "EventCreated" dans RabbitMQ
6. Notification Service écoute → Envoie email
```

---

## 📖 PARTIE 2 : API GATEWAY (45 min)

### 2.1 Qu'est-ce qu'une API Gateway ?

**Définition** : Un **reverse proxy intelligent** qui sert de point d'entrée unique pour tous les microservices.

**Analogie** : La réception d'un hôtel. Au lieu d'aller directement dans les chambres, vous passez par la réception.
```
❌ Sans API Gateway (chaos)
┌──────┐
│Client├────► http://events-service:3001/events
│      ├────► http://users-service:3002/users
│      ├────► http://payments-service:3003/payments
└──────┘
Problèmes :
- Client connaît tous les services (couplage)
- CORS configuré 3 fois
- Authentification dupliquée 3 fois


✅ Avec API Gateway (propre)
┌──────┐     ┌───────────┐     ┌──────────┐
│Client├────►│  Gateway  ├────►│Service A │
└──────┘     │   :8080   ├────►│Service B │
             │           ├────►│Service C │
             └───────────┘     └──────────┘
Avantages :
- Client connaît 1 seule URL
- Authentification centralisée
- CORS géré une fois

2.2 Responsabilités de l'API Gateway
1. Routage
Rediriger les requêtes vers le bon service.
javascriptconst routes = {
  '/api/events': 'http://events-service:3001',
  '/api/users': 'http://users-service:3002',
  '/api/payments': 'http://payments-service:3003'
};

app.use('/api/*', (req, res) => {
  const service = findService(req.path);
  httpProxy.web(req, res, { target: service });
});
2. Authentification centralisée
Vérifier le JWT une seule fois au Gateway.
javascript// Gateway vérifie l'authentification
app.use('/api/*', async (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'No token' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    
    // Transmettre l'info au service backend
    req.headers['x-user-id'] = decoded.sub;
    req.headers['x-user-role'] = decoded.role;
    
    next();
  } catch {
    return res.status(401).json({ error: 'Invalid token' });
  }
});

// Les services backend n'ont plus besoin de vérifier JWT
// Ils lisent juste x-user-id et x-user-role
3. Rate Limiting
Limiter le nombre de requêtes par client.
javascriptconst rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Max 100 requêtes par IP
  message: 'Too many requests, please try again later'
});

app.use('/api/', limiter);
4. Load Balancing
Répartir la charge entre plusieurs instances.
javascriptconst instances = {
  events: [
    'http://events-1:3001',
    'http://events-2:3001',
    'http://events-3:3001'
  ]
};

let currentIndex = 0;

function getNextInstance(service) {
  const serviceInstances = instances[service];
  const instance = serviceInstances[currentIndex % serviceInstances.length];
  currentIndex++;
  return instance;
}

app.use('/api/events', (req, res) => {
  const target = getNextInstance('events');
  httpProxy.web(req, res, { target });
});
5. Agrégation de réponses
Combiner des données de plusieurs services.
javascript// GET /api/dashboard
app.get('/api/dashboard', authenticateJWT, async (req, res) => {
  try {
    // Appeler 3 services en parallèle
    const [events, user, stats] = await Promise.all([
      fetch('http://events-service:3001/events/upcoming'),
      fetch(`http://users-service:3002/users/${req.user.id}`),
      fetch('http://analytics-service:3005/stats')
    ]);
    
    // Agréger les réponses
    res.json({
      events: await events.json(),
      user: await user.json(),
      stats: await stats.json()
    });
  } catch (error) {
    res.status(500).json({ error: 'Dashboard unavailable' });
  }
});
6. Transformation et cache
javascriptconst redis = require('redis');
const client = redis.createClient();

app.get('/api/events', async (req, res) => {
  // Vérifier le cache
  const cached = await client.get('events:all');
  
  if (cached) {
    return res.json(JSON.parse(cached));
  }
  
  // Si pas en cache, appeler le service
  const response = await fetch('http://events-service:3001/events');
  const data = await response.json();
  
  // Mettre en cache 5 minutes
  await client.setex('events:all', 300, JSON.stringify(data));
  
  res.json(data);
});

2.3 Implémentation d'une API Gateway simple
javascriptconst express = require('express');
const httpProxy = require('http-proxy-middleware');
const rateLimit = require('express-rate-limit');
const jwt = require('jsonwebtoken');
const cors = require('cors');

const app = express();
const proxy = httpProxy.createProxyMiddleware;

// CORS
app.use(cors({
  origin: process.env.CLIENT_URL || 'http://localhost:3000',
  credentials: true
}));

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});
app.use(limiter);

// Services registry
const services = {
  events: process.env.EVENTS_SERVICE || 'http://localhost:3001',
  users: process.env.USERS_SERVICE || 'http://localhost:3002',
  payments: process.env.PAYMENTS_SERVICE || 'http://localhost:3003'
};

// Middleware d'authentification
function authenticateJWT(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Authentication required' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.headers['x-user-id'] = decoded.sub;
    req.headers['x-user-role'] = decoded.role;
    next();
  } catch {
    return res.status(401).json({ error: 'Invalid token' });
  }
}

// Routes
app.use('/api/events', authenticateJWT, proxy({
  target: services.events,
  changeOrigin: true,
  pathRewrite: { '^/api/events': '' }
}));

app.use('/api/users', authenticateJWT, proxy({
  target: services.users,
  changeOrigin: true,
  pathRewrite: { '^/api/users': '' }
}));

app.use('/api/payments', authenticateJWT, proxy({
  target: services.payments,
  changeOrigin: true,
  pathRewrite: { '^/api/payments': '' }
}));

// Health check
app.get('/health', (req, res) => {
  res.json({
    status: 'ok',
    services,
    timestamp: new Date().toISOString()
  });
});

const PORT = process.env.PORT || 8080;
app.listen(PORT, () => {
  console.log(`API Gateway running on port ${PORT}`);
});
```

---

### 2.4 Solutions du marché

| Solution | Type | Cas d'usage |
|----------|------|-------------|
| **Kong** | Open-source | Production, extensible via plugins |
| **Traefik** | Open-source | Kubernetes, auto-configuration |
| **AWS API Gateway** | Managed | Infrastructure AWS |
| **Azure API Management** | Managed | Infrastructure Azure |
| **Express Gateway** | Open-source | Node.js, simple |
| **Nginx** | Open-source | Léger, performant |

**Recommandation SmartCity** :
- **Dev/Test** : Express Gateway (simple)
- **Production** : Kong ou Traefik (mature)

---

## 📖 PARTIE 3 : SERVICE DISCOVERY (30 min)

### 3.1 Le problème : Où sont mes services ?

**Scénario** : Vous avez 20 services qui démarrent/s'arrêtent dynamiquement (auto-scaling).

**Problème** : Comment l'API Gateway sait où router ?
```
❌ Configuration statique (ne marche pas)
const services = {
  events: 'http://192.168.1.10:3001'
};
// Et si cette instance meurt ?
// Et si on scale à 3 instances ?


✅ Service Discovery (dynamique)
const eventsInstances = serviceRegistry.getInstances('events');
// Retourne : ['192.168.1.10:3001', '192.168.1.11:3001', '192.168.1.12:3001']
```

---

### 3.2 Comment fonctionne Service Discovery ?

**Principe** : Un **registre central** où les services s'enregistrent.
```
1. Service Events démarre
2. Service s'enregistre : "Je suis Events à 192.168.1.10:3001"
3. Gateway interroge le registre : "Où est Events ?"
4. Registre répond : "192.168.1.10:3001, 192.168.1.11:3001"
5. Gateway choisit une instance (load balancing)
6. Gateway route la requête

3.3 Solutions de Service Discovery
Consul (HashiCorp)
javascriptconst consul = require('consul')({ host: 'consul-server', port: 8500 });

// Service s'enregistre au démarrage
async function registerService() {
  await consul.agent.service.register({
    id: 'events-service-1',
    name: 'events',
    address: '192.168.1.10',
    port: 3001,
    check: {
      http: 'http://192.168.1.10:3001/health',
      interval: '10s',
      timeout: '5s'
    }
  });
  console.log('Service registered in Consul');
}

// Gateway découvre les instances
async function getEventsService() {
  const result = await consul.health.service({
    service: 'events',
    passing: true // Seulement les instances healthy
  });
  
  const instances = result.map(r => ({
    address: r.Service.Address,
    port: r.Service.Port
  }));
  
  return instances;
  // [{address: '192.168.1.10', port: 3001}, ...]
}
Kubernetes (Service Discovery natif)
yaml# events-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: events-service
spec:
  selector:
    app: events
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3001
javascript// Dans le Gateway, simplement :
fetch('http://events-service/events')
// Kubernetes résout automatiquement vers une instance disponible

3.4 Health Checks : Détecter les pannes
Principe : Le registre vérifie périodiquement si les services sont vivants.
javascript// Endpoint health dans chaque service
app.get('/health', async (req, res) => {
  try {
    // Vérifier la DB
    await db.query('SELECT 1');
    
    res.status(200).json({
      status: 'healthy',
      service: 'events-service',
      timestamp: new Date().toISOString()
    });
  } catch (error) {
    res.status(503).json({
      status: 'unhealthy',
      error: error.message
    });
  }
});

// Consul vérifie toutes les 10s
// Si 3 échecs consécutifs → Service marqué "unhealthy"
// Gateway ne route plus vers cette instance
```

---

## 📖 PARTIE 4 : PATTERNS ESSENTIELS (50 min)

### 4.1 Circuit Breaker : Éviter l'effet domino

#### Le problème
```
Service A appelle Service B (timeout 5s)
Service B est down
Service A attend 5s × 100 requêtes/s = 500 threads bloqués
Service A manque de mémoire
Service A plante aussi
❌ Effet domino catastrophique
```

#### La solution : Circuit Breaker

**Principe** : Comme un disjoncteur électrique. Si trop d'échecs, on coupe.

**Les 3 états** :
```
┌─────────────────────────────────────────────────┐
│  1. CLOSED (fermé)                              │
│     Tout fonctionne normalement                 │
│     On laisse passer toutes les requêtes        │
└──────────────┬──────────────────────────────────┘
               │
          Si 5 échecs consécutifs
               │
               ▼
┌─────────────────────────────────────────────────┐
│  2. OPEN (ouvert)                               │
│     Service détecté comme down                  │
│     Toutes les requêtes échouent immédiatement  │
│     (Fail fast, pas d'attente)                  │
└──────────────┬──────────────────────────────────┘
               │
          Après 60 secondes
               │
               ▼
┌─────────────────────────────────────────────────┐
│  3. HALF-OPEN (semi-ouvert)                     │
│     On teste avec 1 requête                     │
│     - Si succès → Retour à CLOSED               │
│     - Si échec → Retour à OPEN                  │
└─────────────────────────────────────────────────┘
Implémentation
javascriptclass CircuitBreaker {
  constructor(threshold = 5, timeout = 60000) {
    this.state = 'CLOSED';
    this.failureCount = 0;
    this.threshold = threshold;
    this.timeout = timeout;
    this.nextAttempt = Date.now();
  }
  
  async call(serviceFunction) {
    // Si circuit ouvert
    if (this.state === 'OPEN') {
      if (Date.now() < this.nextAttempt) {
        throw new Error('Circuit breaker is OPEN - Service unavailable');
      }
      // Temps écoulé, on teste
      this.state = 'HALF-OPEN';
    }
    
    try {
      const result = await serviceFunction();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }
  
  onSuccess() {
    this.failureCount = 0;
    this.state = 'CLOSED';
    console.log('Circuit breaker: CLOSED');
  }
  
  onFailure() {
    this.failureCount++;
    console.log(`Circuit breaker: Failure ${this.failureCount}/${this.threshold}`);
    
    if (this.failureCount >= this.threshold) {
      this.state = 'OPEN';
      this.nextAttempt = Date.now() + this.timeout;
      console.log(`Circuit breaker: OPEN (retry in ${this.timeout}ms)`);
    }
  }
}

// Utilisation
const paymentBreaker = new CircuitBreaker(5, 60000);

app.post('/api/orders', async (req, res) => {
  try {
    // Appel protégé par circuit breaker
    const payment = await paymentBreaker.call(async () => {
      const response = await fetch('http://payment-service:3003/process', {
        method: 'POST',
        body: JSON.stringify(req.body)
      });
      
      if (!response.ok) throw new Error('Payment failed');
      return response.json();
    });
    
    res.json({ success: true, payment });
    
  } catch (error) {
    if (error.message.includes('Circuit breaker')) {
      // Fallback : mode dégradé
      res.json({
        success: false,
        message: 'Payment service temporarily unavailable. Order saved, payment will be processed later.'
      });
    } else {
      res.status(500).json({ error: error.message });
    }
  }
});
```

**Bibliothèques** :
- **Node.js** : `opossum`
- **Java** : Resilience4j
- **Python** : `pybreaker`

---

### 4.2 Saga Pattern : Transactions distribuées

#### Le problème : Pas de ACID entre microservices

**Scénario** : Commander un billet d'événement
```
1. Créer la commande (Order Service)
2. Débiter 50€ (Payment Service)
3. Réserver la place (Event Service)
4. Envoyer confirmation (Notification Service)

❌ Si l'étape 3 échoue (plus de places), comment annuler 1 et 2 ?
Pas de ROLLBACK cross-services !
La solution : Saga Pattern
Principe : Une série de transactions locales avec des compensations en cas d'échec.

Approche 1 : Choreography (Chorégraphie)
Chaque service écoute des événements et réagit.
Order Service
  ├─ Crée commande (transaction locale)
  └─ Publie "OrderCreated" dans RabbitMQ
      ↓
Payment Service écoute "OrderCreated"
  ├─ Tente de débiter
  │   ├─ Succès → Publie "PaymentProcessed"
  │   └─ Échec → Publie "PaymentFailed"
      ↓
Event Service écoute "PaymentProcessed"
  ├─ Tente de réserver
  │   ├─ Succès → Publie "SeatReserved"
  │   └─ Échec → Publie "ReservationFailed"
      ↓
Si "ReservationFailed" → Compensation
  Payment Service écoute → Rembourse
  Order Service écoute → Annule commande
      ↓
Notification Service écoute "SeatReserved"
  └─ Envoie email de confirmation
Implémentation avec RabbitMQ :
javascript// Order Service
const amqp = require('amqplib');

async function createOrder(orderData) {
  // 1. Transaction locale : créer la commande
  const order = await db.orders.create({
    userId: orderData.userId,
    eventId: orderData.eventId,
    amount: orderData.amount,
    status: 'PENDING'
  });
  
  // 2. Publier l'événement
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();
  await channel.assertQueue('order-events');
  
  channel.sendToQueue('order-events', Buffer.from(JSON.stringify({
    type: 'OrderCreated',
    orderId: order.id,
    userId: order.userId,
    eventId: order.eventId,
    amount: order.amount
  })));
  
  return order;
}

// Payment Service - Écoute les événements
async function listenOrderEvents() {
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();
  await channel.assertQueue('order-events');
  
  channel.consume('order-events', async (msg) => {
    const event = JSON.parse(msg.content.toString());
    
    if (event.type === 'OrderCreated') {
      try {
        // Traiter le paiement
        const payment = await processPayment({
          userId: event.userId,
          amount: event.amount
        });
        
        // Publier succès
        channel.sendToQueue('payment-events', Buffer.from(JSON.stringify({
          type: 'PaymentProcessed',
          orderId: event.orderId,
          paymentId: payment.id
        })));
        
      } catch (error) {
        // Publier échec
        channel.sendToQueue('payment-events', Buffer.from(JSON.stringify({
          type: 'PaymentFailed',
          orderId: event.orderId,
          reason: error.message
        })));
      }
    }
    
    channel.ack(msg);
  });
}

// Event Service - Écoute les paiements
async function listenPaymentEvents() {
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();
  await channel.assertQueue('payment-events');
  
  channel.consume('payment-events', async (msg) => {
    const event = JSON.parse(msg.content.toString());
    
    if (event.type === 'PaymentProcessed') {
      try {
        // Réserver la place
        const reservation = await reserveSeat({
          orderId: event.orderId,
          eventId: event.eventId
        });
        
        // Publier succès
        channel.sendToQueue('reservation-events', Buffer.from(JSON.stringify({
          type: 'SeatReserved',
          orderId: event.orderId,
          reservationId: reservation.id
        })));
        
      } catch (error) {
        // Publier échec (ex: plus de places)
        channel.sendToQueue('reservation-events', Buffer.from(JSON.stringify({
          type: 'ReservationFailed',
          orderId: event.orderId,
          paymentId: event.paymentId,
          reason: error.message
        })));
      }
    }
    
    channel.ack(msg);
  });
}

// Payment Service - Compensation (écoute les échecs)
async function listenReservationFailures() {
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();
  await channel.assertQueue('reservation-events');
  
  channel.consume('reservation-events', async (msg) => {
    const event = JSON.parse(msg.content.toString());
    
    if (event.type === 'ReservationFailed') {
      console.log(`Compensating: Refunding payment ${event.paymentId}`);
      
      // Compensation : rembourser
      await refundPayment(event.paymentId);
      
      // Publier compensation effectuée
      channel.sendToQueue('payment-events', Buffer.from(JSON.stringify({
        type: 'PaymentRefunded',
        orderId: event.orderId,
        paymentId: event.paymentId
      })));
    }
    
    channel.ack(msg);
  });
}
```

**Avantages** :
- ✅ Décentralisé, pas de point unique de défaillance
- ✅ Services autonomes
- ✅ Résilient

**Inconvénients** :
- ❌ Difficile à suivre (pas de vue globale)
- ❌ Debugging complexe
- ❌ Risque de boucles infinies

---

#### Approche 2 : Orchestration

Un orchestrateur central coordonne toutes les étapes.
```
Saga Orchestrator
    │
    ├─ 1. Appelle Order Service → Créer commande
    │    ├─ Succès → Continue
    │    └─ Échec → Abort
    │
    ├─ 2. Appelle Payment Service → Débiter
    │    ├─ Succès → Continue
    │    └─ Échec → Compense : Annuler commande
    │
    ├─ 3. Appelle Event Service → Réserver
    │    ├─ Succès → Continue
    │    └─ Échec → Compense : Rembourser + Annuler commande
    │
    └─ 4. Appelle Notification Service → Envoyer email
Implémentation :
javascriptclass OrderSaga {
  async execute(orderData) {
    let orderId, paymentId, reservationId;
    
    try {
      // Étape 1 : Créer la commande
      console.log('Saga: Creating order...');
      const orderResponse = await fetch('http://order-service:3001/orders', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(orderData)
      });
      
      if (!orderResponse.ok) throw new Error('Order creation failed');
      const order = await orderResponse.json();
      orderId = order.id;
      console.log(`Saga: Order created ${orderId}`);
      
      // Étape 2 : Traiter le paiement
      console.log('Saga: Processing payment...');
      const paymentResponse = await fetch('http://payment-service:3003/payments', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          orderId,
          userId: orderData.userId,
          amount: orderData.amount
        })
      });
      
      if (!paymentResponse.ok) throw new Error('Payment failed');
      const payment = await paymentResponse.json();
      paymentId = payment.id;
      console.log(`Saga: Payment processed ${paymentId}`);
      
      // Étape 3 : Réserver la place
      console.log('Saga: Reserving seat...');
      const reservationResponse = await fetch('http://event-service:3002/reservations', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          orderId,
          eventId: orderData.eventId
        })
      });
      
      if (!reservationResponse.ok) throw new Error('Reservation failed');
      const reservation = await reservationResponse.json();
      reservationId = reservation.id;
      console.log(`Saga: Seat reserved ${reservationId}`);
      
      // Étape 4 : Envoyer notification
      console.log('Saga: Sending notification...');
      await fetch('http://notification-service:3004/send', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          userId: orderData.userId,
          type: 'order_confirmation',
          orderId
        })
      });
      
      console.log('Saga: SUCCESS - Order completed');
      return { success: true, orderId, paymentId, reservationId };
      
    } catch (error) {
      console.error(`Saga: FAILURE - ${error.message}`);
      console.log('Saga: Starting compensation...');
      
      // Compensation dans l'ordre inverse
      
      // Si la réservation a échoué mais paiement effectué
      if (paymentId && !reservationId) {
        console.log(`Saga: Refunding payment ${paymentId}`);
        await fetch(`http://payment-service:3003/payments/${paymentId}/refund`, {
          method: 'POST'
        });
      }
      
      // Si la commande a été créée
      if (orderId) {
        console.log(`Saga: Cancelling order ${orderId}`);
        await fetch(`http://order-service:3001/orders/${orderId}/cancel`, {
          method: 'POST'
        });
      }
      
      console.log('Saga: Compensation completed');
      return { success: false, error: error.message };
    }
  }
}

// Utilisation
app.post('/api/bookings', async (req, res) => {
  const saga = new OrderSaga();
  const result = await saga.execute(req.body);
  
  if (result.success) {
    res.status(201).json(result);
  } else {
    res.status(400).json(result);
  }
});
```

**Avantages** :
- ✅ Vue globale du processus
- ✅ Facile à debugger
- ✅ Contrôle centralisé

**Inconvénients** :
- ❌ Point central de défaillance (l'orchestrateur)
- ❌ Couplage (orchestrateur connaît tous les services)

---

#### Comparaison

| Aspect | Choreography | Orchestration |
|--------|--------------|---------------|
| **Complexité** | ⚠️ Élevée | ✅ Plus simple |
| **Couplage** | ✅ Faible | ⚠️ Moyen |
| **Point de défaillance** | ✅ Aucun | ❌ Orchestrateur |
| **Debugging** | ❌ Difficile | ✅ Facile |
| **Vue globale** | ❌ Non | ✅ Oui |
| **Scalabilité** | ✅ Excellente | ⚠️ Limitée par orchestrateur |

**Recommandation** :
- **Orchestration** pour SmartCity (plus simple, équipe de taille moyenne)
- **Choreography** pour grandes architectures (Netflix, Uber)

---

### 4.3 Event-Driven Architecture : Communication asynchrone

#### Pourquoi l'asynchrone ?

**Problème synchrone** :
```
Client → Gateway → Order Service (attend 2s)
                       ↓
                   Payment Service (attend 1s)
                       ↓
                   Notification Service (attend 3s)
                       ↓
         Latence totale : 6 secondes ❌
         Si Notification est down → tout échoue
```

**Solution asynchrone** :
```
Client → Gateway → Order Service
                       ↓
                   Répond immédiatement (200ms) ✅
                       ↓
                   Publie "OrderCreated" dans queue
                       ↓
En arrière-plan (parallèle) :
    Payment Service écoute → Traite (1s)
    Notification Service écoute → Envoie email (3s)
    Analytics Service écoute → Enregistre stats (500ms)

Message Queue vs Event Stream
Message Queue (RabbitMQ, AWS SQS) :

Un message → un seul consommateur
Le message est supprimé après consommation
Garantie de livraison
FIFO (First In First Out)

Event Stream (Kafka, AWS Kinesis) :

Un événement → plusieurs consommateurs
Les événements sont conservés (log distribué)
Rejouable (event sourcing)
Permet le replay


Implémentation avec RabbitMQ
javascriptconst amqp = require('amqplib');

// Publisher : Order Service
async function publishOrderCreated(order) {
  const connection = await amqp.connect(process.env.RABBITMQ_URL);
  const channel = await connection.createChannel();
  
  const exchange = 'events';
  await channel.assertExchange(exchange, 'fanout', { durable: true });
  
  const message = {
    type: 'OrderCreated',
    data: {
      orderId: order.id,
      userId: order.userId,
      eventId: order.eventId,
      amount: order.amount,
      timestamp: new Date().toISOString()
    }
  };
  
  channel.publish(exchange, '', Buffer.from(JSON.stringify(message)));
  console.log('Published: OrderCreated');
  
  await channel.close();
  await connection.close();
}

// Consumer 1 : Payment Service
async function listenForOrders() {
  const connection = await amqp.connect(process.env.RABBITMQ_URL);
  const channel = await connection.createChannel();
  
  const exchange = 'events';
  await channel.assertExchange(exchange, 'fanout', { durable: true });
  
  const queue = await channel.assertQueue('payment-queue', { durable: true });
  await channel.bindQueue(queue.queue, exchange, '');
  
  console.log('Payment Service: Waiting for orders...');
  
  channel.consume(queue.queue, async (msg) => {
    const event = JSON.parse(msg.content.toString());
    
    if (event.type === 'OrderCreated') {
      console.log(`Payment Service: Processing order ${event.data.orderId}`);
      
      try {
        await processPayment(event.data);
        channel.ack(msg);
      } catch (error) {
        console.error('Payment failed:', error);
        // Rejeter le message (sera retenté)
        channel.nack(msg, false, true);
      }
    }
  });
}

// Consumer 2 : Notification Service
async function listenForOrders() {
  const connection = await amqp.connect(process.env.RABBITMQ_URL);
  const channel = await connection.createChannel();
  
  const exchange = 'events';
  await channel.assertExchange(exchange, 'fanout', { durable: true });
  
  const queue = await channel.assertQueue('notification-queue', { durable: true });
  await channel.bindQueue(queue.queue, exchange, '');
  
  console.log('Notification Service: Waiting for orders...');
  
  channel.consume(queue.queue, async (msg) => {
    const event = JSON.parse(msg.content.toString());
    
    if (event.type === 'OrderCreated') {
      console.log(`Notification Service: Sending email for order ${event.data.orderId}`);
      
      await sendEmail({
        to: event.data.userId,
        subject: 'Commande confirmée',
        body: `Votre commande ${event.data.orderId} a été créée.`
      });
      
      channel.ack(msg);
    }
  });
}
Avantages :

✅ Découplage total
✅ Résilience (si un consumer est down, les autres continuent)
✅ Scalabilité (ajouter des consumers facilement)
✅ Performance (réponse immédiate au client)


4.4 Patterns de résilience supplémentaires
Timeout
Ne jamais attendre indéfiniment.
javascriptasync function callServiceWithTimeout(url, timeout = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, {
      signal: controller.signal
    });
    clearTimeout(timeoutId);
    return response;
  } catch (error) {
    if (error.name === 'AbortError') {
      throw new Error('Request timeout');
    }
    throw error;
  }
}
Retry avec Exponential Backoff
javascriptasync function retryWithBackoff(fn, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt === maxRetries) throw error;
      
      const delay = Math.pow(2, attempt) * 1000; // 2s, 4s, 8s
      console.log(`Attempt ${attempt} failed. Retrying in ${delay}ms...`);
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}

// Utilisation
const data = await retryWithBackoff(() => 
  fetch('http://payment-service:3003/process')
);
Bulkhead Pattern
Isoler les ressources pour éviter qu'une défaillance consomme tout.
javascriptconst pLimit = require('p-limit');

// Limiter à 10 requêtes simultanées vers Payment Service
const paymentLimit = pLimit(10);

app.post('/api/orders', async (req, res) => {
  try {
    // Cette requête attendra si 10 autres sont déjà en cours
    const payment = await paymentLimit(() => 
      fetch('http://payment-service:3003/process')
    );
    
    res.json({ success: true });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

---

## 🎓 CONCLUSION

### Points clés à retenir

**1. Microservices ≠ Solution magique**  
Complexité opérationnelle élevée. Commencez monolithe, migrez si nécessaire.

**2. API Gateway = Point d'entrée essentiel**  
Authentification, rate limiting, routage centralisés.

**3. Service Discovery = Dynamisme**  
Les services s'enregistrent automatiquement. Consul, Eureka, Kubernetes.

**4. Circuit Breaker = Résilience**  
Éviter l'effet domino quand un service tombe.

**5. Saga Pattern = Transactions distribuées**  
Orchestration (simple) ou Choreography (scalable).

**6. Event-Driven = Découplage**  
Communication asynchrone via message queues pour performance et résilience.

---

### Architecture SmartCity finale
```
                    ┌─────────────┐
                    │   Clients   │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │ API Gateway │
                    │  + Consul   │
                    └──────┬──────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
    ┌────▼────┐      ┌────▼────┐      ┌────▼────┐
    │ Events  │      │  Users  │      │ Payment │
    │ Service │      │ Service │      │ Service │
    │ +Circuit│      │         │      │ +Circuit│
    │ Breaker │      │         │      │ Breaker │
    └────┬────┘      └────┬────┘      └────┬────┘
         │                │                │
         └────────────────┼────────────────┘
                          │
                   ┌──────▼──────┐
                   │  RabbitMQ   │
                   │  (Events)   │
                   └─────────────┘

Quand utiliser quoi ?
BesoinSolutionPetit projetMonolitheÉquipe > 15MicroservicesPoint d'entrée uniqueAPI GatewayServices dynamiquesService DiscoveryService instableCircuit BreakerTransaction multi-servicesSaga PatternDécouplageEvent-Driven

Citation finale

"The best way to learn microservices is to feel the pain of a monolith first."
— Sam Newman, Building Microservices

En 2025 : Les microservices sont le standard pour les grandes applications. Mais ne sous-estimez jamais la valeur d'un bon monolithe bien construit.

Ressources pour aller plus loin
Livres :

"Building Microservices" - Sam Newman (2021)
"Microservices Patterns" - Chris Richardson (2018)
"Release It!" - Michael Nygard (2018)

Outils :

Kong : API Gateway
Consul : Service Discovery
RabbitMQ : Message Queue
Kubernetes : Orchestration

Patterns :

microservices.io - Chris Richardson
martinfowler.com/microservices


[FIN DU JOUR 7 - MICROSERVICES ET ARCHITECTURE DISTRIBUÉE]
Prochaine étape : Jour 8 - Performance, Tests et DevOps 🚀