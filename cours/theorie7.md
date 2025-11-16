## 📚 Jour 7 — Microservices et architecture distribuée

**Cours théorique — Mastère 2 (Durée : 3h30)**

## 🎯 Objectifs pédagogiques

À la fin de cette journée, vous serez capables de :

- Comprendre l'architecture microservices et ses différences avec le monolithe
- Maîtriser le rôle et l'implémentation d'une API Gateway
- Mettre en œuvre la découverte de services (Service Discovery)
- Appliquer les patterns essentiels : Circuit Breaker et Saga

---

## 📖 Partie 1 — Architecture microservices (50 min)

### 1.1 Du monolithe aux microservices

#### Le monolithe : simple mais limité

- **Définition** : une application où tout le code est regroupé dans un seul projet, déployé comme une seule unité.
- Exemple SmartCity :

```text
┌─────────────────────────────────┐
│  Projet Node.js unique          │
│  ├─ routes/events.js            │
│  ├─ routes/users.js             │
│  ├─ routes/payments.js          │
│  ├─ routes/notifications.js     │
│  └─ routes/auth.js              │
│  Une seule base de données      │
│  Un seul déploiement / serveur  │
└─────────────────────────────────┘
```

**Avantages**

- ✅ Simple à développer : un repo, une stack
- ✅ Déploiement rapide : `npm start`
- ✅ Tests d'intégration faciles
- ✅ Performant (pas d'appels réseau)
- ✅ Idéal pour MVP / petites équipes (&lt;10)

**Limites**

- ❌ Scalabilité « tout ou rien »
- ❌ Déploiements risqués : un bug = tout plante
- ❌ Équipes bloquées sur le même code
- ❌ Stack technologique figée, builds lents

#### L'exemple Netflix

- 2008 : monolithe Java stable
- 2009 : corruption de la base → 3 jours d'indisponibilité (pannes critiques)
- 2010-2012 : migration vers les microservices
- 2025 : 2000+ microservices, 4000 déploiements/jour
- Résilience : si `Recommendations` tombe, `Streaming` continue

#### Qu'est-ce qu'un microservice ?

- Service **petit, autonome** avec une seule responsabilité, déployable indépendamment.
- Caractéristiques :
  - Une seule responsabilité (Events, Users, Payments…)
  - Base de données propre
  - API explicite (REST, gRPC, events)
  - Déploiement indépendant
  - Équipe autonome (Two Pizza Team)

```text
┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│   Events    │ │    Users    │ │  Payments   │ │ Notification│
│  Service    │ │  Service    │ │  Service    │ │   Service   │
│   :3001     │ │   :3002     │ │   :3003     │ │   :3004     │
└─────┬───────┘ └────┬────────┘ └────┬────────┘ └────┬────────┘
      │              │               │               │
      └──────────────┴───────────────┴───────────────┘
                     │
             ┌───────▼────────┐
             │   API Gateway  │
             └───────┬────────┘
                     │
             ┌───────▼────────┐
             │    Clients     │
             └────────────────┘
```

---

### 1.2 Comparaison : monolithe vs microservices

| Critère                | Monolithe                       | Microservices                          |
|------------------------|---------------------------------|----------------------------------------|
| Complexité initiale    | ✅ Simple                        | ❌ Complexe (infra, CI/CD)             |
| Temps de setup         | 1 jour                          | 1-2 semaines                           |
| Déploiement            | Un artefact                     | Multiples (Docker, K8s)                |
| Scalabilité            | Verticale (+CPU/RAM)            | Horizontale par service                |
| Résilience             | ❌ Point unique de défaillance  | ✅ Isolation des pannes                |
| Organisation           | Une équipe                      | Équipes autonomes                      |
| Technologies           | Stack unique                    | Liberté par service                    |
| Base de données        | Unique                          | Une par service                        |
| Performance            | ✅ Appels locaux rapides        | ⚠️ Latence réseau                      |
| Debugging              | ✅ Un seul log                  | ❌ Logs distribués                     |
| Taille d'équipe        | &lt; 10 personnes                | 10-100+                                |
| Coût infrastructure    | 💰 Faible                       | 💰💰 Plus élevé                        |

---

### 1.3 Quand (ne pas) utiliser les microservices ?

**Adoptez les microservices si :**

- Équipe &gt; 15 personnes, besoin d'autonomie
- Domaines métier multiples
- Charges très différentes (Events 1000 req/s vs Users 10 req/s)
- Déploiements fréquents (CI/CD avancé)
- Besoin d'expérimenter de nouvelles technologies
- Haute disponibilité critique

**Restez monolithe si :**

- Startup / MVP (rapidité avant tout)
- Petite équipe (&lt;10) ou budget limité
- Domaine simple
- Pas (encore) d'expertise DevOps

> Martin Fowler : « Don’t start with microservices. Start with a monolith and split when the pain is real. »

**Signaux de migration :**

- Déploiement &gt; 30 min
- Conflits inter-équipes sur le même code
- Scalabilité fine impossible
- Base de données saturée

---

### 1.4 Les défis des microservices

1. **Complexité opérationnelle**  
   50 services = 50 repos, déploiements, bases, logs.  
   _Solutions_ : Kubernetes, Docker, CI/CD (GitHub Actions, GitLab CI, Jenkins), IaC (Terraform, Ansible).

2. **Communication inter-services**  
   Latence cumulée A → B → C = 300 ms.  
   _Solutions_ : Caching (Redis), messaging asynchrone (RabbitMQ, Kafka), API Gateway.

3. **Données distribuées**  
   Pas de transactions ACID multi-services.  
   _Solutions_ : Saga Pattern (compensation), eventual consistency, event sourcing.

4. **Monitoring & debugging**  
   Une requête traverse 5 services : où est l’erreur ?  
   _Solutions_ : Distributed tracing (Jaeger, Zipkin), centralized logging (ELK), correlation ID.

```javascript
// API Gateway génère un ID
const correlationId = uuidv4();
req.headers['x-correlation-id'] = correlationId;

// Chaque service log avec cet ID
logger.info(`[${req.headers['x-correlation-id']}] Processing event`);

// Dans les logs :
// [abc-123] Gateway: Received request
// [abc-123] Events Service: Fetching event
// [abc-123] Payment Service: Processing payment
// [abc-123] Notification Service: Sending email
```

---

### 1.5 Architecture cible SmartCity

```text
┌─────────────────────────────────────────────────────────┐
│                     CLIENTS (Web, Mobile, Partenaires)  │
└────────────────────┬────────────────────────────────────┘
                     │ HTTPS
        ┌────────────▼────────────┐
        │        API GATEWAY      │ :8080
        │  - Auth, Rate limiting  │
        │  - Routage, agrégation  │
        └────────────┬────────────┘
                     │
         ┌───────────┼───────────┬───────────┐
         │           │           │           │
    ┌────▼────┐ ┌───▼────┐ ┌───▼────┐ ┌────▼────┐
    │ Events  │ │ Users  │ │Payment │ │  Notif  │
    │ Service │ │Service │ │Service │ │ Service │
    │ :3001   │ │ :3002  │ │ :3003  │ │ :3004   │
    └────┬────┘ └───┬────┘ └───┬────┘ └────┬────┘
         │          │          │           │
    ┌────▼────┐┌───▼────┐┌───▼────┐  ┌───▼────┐
    │Postgres ││Postgres││Postgres│  │ Redis  │
    │Events   ││Users   ││Payments│  │ Cache  │
    └─────────┘└────────┘└────────┘  └────────┘
                    │
            ┌───────▼────────┐
            │  MESSAGE QUEUE │ (RabbitMQ)
            └────────────────┘
```

**Flux typique — Création d’un événement**

```text
1. Client POST /api/events → API Gateway
2. Gateway vérifie le JWT → OK
3. Gateway route vers Events Service (:3001)
4. Events Service écrit l’événement en DB
5. Events Service publie `EventCreated` dans RabbitMQ
6. Notification Service consomme l’événement et envoie un email
```

---

## 📖 PARTIE 2 : API GATEWAY (45 min)

### 2.1 Qu'est-ce qu'une API Gateway ?

- **Définition** : reverse proxy intelligent qui centralise l'accès aux microservices (sécurité, routage, observabilité).
- **Analogie** : la réception d’un hôtel – on passe toujours par elle avant toute interaction.

```text
❌ Sans Gateway (couplage fort)
Client ──► http://events-service:3001/events
      └──► http://users-service:3002/users
      └──► http://payments-service:3003/payments
• Le client connaît tous les services
• Authentification/CORS à dupliquer
• URLs internes exposées

✅ Avec Gateway (propre)
Client ──► https://api.smartcity.fr (Gateway)
                 └──► Services internes (Events, Users, Payments…)
• Une seule URL publique
• Authentification centralisée
• Observabilité, rate limiting, caching
```

---

### 2.2 Responsabilités clés

1. **Routage**

   ```javascript
   const routes = {
     '/api/events': 'http://events-service:3001',
     '/api/users' : 'http://users-service:3002',
     '/api/payments': 'http://payments-service:3003'
   };

   app.use('/api/*', (req, res) => {
     const target = routes[match(req.path)];
     proxy.web(req, res, { target });
   });
   ```

2. **Authentification centralisée**

   Vérifier le JWT une seule fois, propager l’identité.

   ```javascript
   app.use('/api/*', async (req, res, next) => {
     const token = req.headers.authorization?.split(' ')[1];
     if (!token) return res.status(401).json({ error: 'No token' });
     try {
       const decoded = jwt.verify(token, process.env.JWT_SECRET);
       req.headers['x-user-id'] = decoded.sub;
       req.headers['x-user-role'] = decoded.role;
       next();
     } catch {
       return res.status(401).json({ error: 'Invalid token' });
     }
   });
   ```

3. **Rate limiting**

   ```javascript
   const limiter = rateLimit({
     windowMs: 15 * 60 * 1000,
     max: 100,
     message: 'Too many requests, please try again later'
   });
   app.use('/api/', limiter);
   ```

4. **Load balancing**

   ```javascript
   const instances = {
     events: [
       'http://events-1:3001',
       'http://events-2:3001',
       'http://events-3:3001'
     ]
   };

   let currentIndex = 0;
   function getNextInstance(service) {
     const list = instances[service];
     return list[(currentIndex++) % list.length];
   }

   app.use('/api/events', (req, res) => {
     proxy.web(req, res, { target: getNextInstance('events') });
   });
   ```

5. **Agrégation**

   ```javascript
   app.get('/api/dashboard', authenticateJWT, async (req, res) => {
     try {
       const [events, user, stats] = await Promise.all([
         fetch('http://events-service:3001/events/upcoming'),
         fetch(`http://users-service:3002/users/${req.user.id}`),
         fetch('http://analytics-service:3005/stats')
       ]);
       res.json({
         events: await events.json(),
         user: await user.json(),
         stats: await stats.json()
       });
     } catch {
       res.status(500).json({ error: 'Dashboard unavailable' });
     }
   });
   ```

6. **Transformation & cache**

   ```javascript
   const redis = require('redis');
   const client = redis.createClient();

   app.get('/api/events', async (req, res) => {
     const cached = await client.get('events:all');
     if (cached) return res.json(JSON.parse(cached));

     const response = await fetch('http://events-service:3001/events');
     const data = await response.json();
     await client.setEx('events:all', 300, JSON.stringify(data));
     res.json(data);
   });
   ```

---

### 2.3 Implémentation Express (extrait)

```javascript
const express = require('express');
const { createProxyMiddleware } = require('http-proxy-middleware');
const rateLimit = require('express-rate-limit');
const jwt = require('jsonwebtoken');
const cors = require('cors');

const app = express();
app.use(cors({ origin: process.env.CLIENT_URL || 'http://localhost:3000', credentials: true }));
app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));

const services = {
  events: process.env.EVENTS_SERVICE || 'http://localhost:3001',
  users: process.env.USERS_SERVICE || 'http://localhost:3002',
  payments: process.env.PAYMENTS_SERVICE || 'http://localhost:3003'
};

function authenticateJWT(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'Authentication required' });
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.headers['x-user-id'] = decoded.sub;
    req.headers['x-user-role'] = decoded.role;
    next();
  } catch {
    return res.status(401).json({ error: 'Invalid token' });
  }
}

app.use('/api/events', authenticateJWT, createProxyMiddleware({
  target: services.events,
  changeOrigin: true,
  pathRewrite: { '^/api/events': '' }
}));

// ... idem pour /api/users et /api/payments

app.get('/health', (req, res) => {
  res.json({ status: 'ok', services, timestamp: new Date().toISOString() });
});

const PORT = process.env.PORT || 8080;
app.listen(PORT, () => console.log(`API Gateway running on port ${PORT}`));
```

> **Astuce** : exposez toujours un `/health` détaillé (statut, dépendances) pour l’intégration avec Service Discovery et la supervision.

---

### 2.4 Solutions du marché

| Solution | Type | Cas d’usage |
|----------|------|-------------|
| **Kong** | Open-source / Enterprise | Production, plugins (auth, rate-limit, observabilité) |
| **Traefik** | Open-source | Kubernetes natif, auto-discovery |
| **AWS API Gateway** | Managed | Intégration serverless, écosystème AWS |
| **Azure API Management** | Managed | Écosystème Azure, monitoring intégré |
| **Express Gateway / Nginx** | Open-source léger | POC, environnements Node.js simples |

**Recommandation SmartCity**

- Dev / Test : Express Gateway (mise en route rapide)
- Production : Kong ou Traefik (observabilité, plugins, support Kubernetes)

---

## 📖 PARTIE 3 : SERVICE DISCOVERY (30 min)

### 3.1 Le problème : Où sont mes services ?

**Scénario** : 20 microservices démarrent/s’arrêtent en fonction de l’auto-scaling. Les IP changent à chaque déploiement.

**Problème** : comment l’API Gateway (ou un autre service) sait vers quelle instance router ?

```text
❌ Configuration statique
const services = {
  events: 'http://192.168.1.10:3001'
};
// L’instance change d’IP ou tombe → indisponibilité

✅ Service Discovery
const instances = registry.getInstances('events');
// → ['192.168.1.10:3001','192.168.1.11:3001']
```

---

### 3.2 Comment fonctionne Service Discovery ?

1. Le service démarre → s’enregistre dans un registre (nom, IP, port, métadonnées)
2. Le registre exécute des health-checks réguliers
3. Les clients interrogent le registre pour connaître les instances disponibles
4. Si une instance tombe, le registre la marque <em>unhealthy</em> et ne la renvoie plus

---

### 3.3 Solutions (Consul, Kubernetes)

#### Consul (HashiCorp)

```javascript
const consul = require('consul')({ host: 'consul-server', port: 8500 });

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
}

async function getEventsInstances() {
  const result = await consul.health.service({ service: 'events', passing: true });
  return result.map(r => ({ address: r.Service.Address, port: r.Service.Port }));
}
```

#### Kubernetes (Service Discovery natif)

```yaml
# events-service.yaml
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
```

```javascript
// Dans le code
fetch('http://events-service/events'); // DNS interne K8s → load balancing auto
```

---

### 3.4 Health checks

```javascript
app.get('/health', async (req, res) => {
  try {
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
```

> Consul / Eureka : après 3 checks échoués → instance marquée <em>unhealthy</em>. Les clients reçoivent uniquement des instances saines.

---

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

### 4.2 Saga Pattern : transactions distribuées

#### Le problème

Commande SmartCity :

1. Créer la commande (Order Service)
2. Débiter 50€ (Payment Service)
3. Réserver la place (Event Service)
4. Envoyer confirmation (Notification Service)

❌ Si l’étape 3 échoue, comment annuler les étapes 1 et 2 ? Il n’existe pas de ROLLBACK global.

> **Rappel ACID** — Atomicité, Cohérence, Isolation, Durabilité. Ces propriétés sont garanties par un seul moteur de base de données. Dans une architecture microservices où chaque service possède sa propre DB, on ne peut pas faire un commit global atomique : on orchestre des transactions locales + des actions de compensation.

#### Principe

Saga = enchaînement de transactions locales + opérations de compensation si une étape échoue.

##### Approche 1 : Chorégraphie (events)

Chaque service écoute des événements et réagit.

```text
Order Service  → publie "OrderCreated"
Payment Service → débite, publie "PaymentProcessed"/"PaymentFailed"
Event Service → réserve, publie "SeatReserved"/"ReservationFailed"
Notification Service → écoute "SeatReserved"

En cas de "ReservationFailed" → Payment rembourse, Order annule.
```

```javascript
const amqp = require('amqplib');

async function createOrder(data) {
  const order = await db.orders.create({ ...data, status: 'PENDING' });
  channel.sendToQueue('order-events', Buffer.from(JSON.stringify({
    type: 'OrderCreated',
    orderId: order.id,
    userId: order.userId,
    eventId: order.eventId,
    amount: order.amount
  })));
}
```

✅ Décentralisé, scalable — ❌ tracing/debugging difficiles.

##### Approche 2 : Orchestration

Un orchestrateur central pilote chaque étape et déclenche les compensations.

```javascript
class OrderSaga {
  async execute(payload) {
    let orderId, paymentId;
    try {
      const order = await http.post('http://order-service/orders', payload);
      orderId = order.id;

      const payment = await http.post('http://payment-service/payments', {
        orderId,
        userId: payload.userId,
        amount: payload.amount
      });
      paymentId = payment.id;

      await http.post('http://event-service/reservations', { orderId, eventId: payload.eventId });
      await http.post('http://notification-service/send', { userId: payload.userId, type: 'order_confirmation', orderId });

      return { success: true, orderId, paymentId };
    } catch (error) {
      await compensate({ orderId, paymentId });
      return { success: false, error: error.message };
    }
  }
}
```

✅ Vue globale, debugging simple — ❌ orchestrateur = point central de défaillance.

##### Comparaison

| Aspect            | Chorégraphie         | Orchestration            |
|-------------------|----------------------|--------------------------|
| Complexité        | ⚠️ Élevée             | ✅ Plus simple            |
| Couplage          | ✅ Faible             | ⚠️ Moyen (orchestrateur) |
| Point de panne    | ✅ Aucun              | ❌ Orchestrateur         |
| Debugging         | ❌ Difficile          | ✅ Facile                |
| Vue globale       | ❌ Non                | ✅ Oui                   |
| Scalabilité       | ✅ Excellente         | ⚠️ Limitée par orchestrateur |

**SmartCity** : Orchestration (taille d’équipe moyenne, besoin de visibilité). Chorégraphie réservée aux très grandes architectures (Netflix, Uber).

---

### 4.3 Event-Driven Architecture : communication asynchrone

#### Pourquoi l’asynchrone ?

```text
❌ Synchrone
Client → Gateway → Order Service (2s)
                     ↳ Payment Service (1s)
                     ↳ Notification Service (3s)
= 6s de latence et tout échoue si Notification est down.

✅ Asynchrone
Client → Gateway → Order Service (réponse 200ms)
Order Service publie "OrderCreated" dans RabbitMQ/Kafka
Payment / Notification / Analytics consomment en parallèle
```

#### Message queue vs Event stream

- **RabbitMQ / AWS SQS** : un message → un seul consommateur, message supprimé après consommation, garantie de livraison, FIFO.
- **Kafka / AWS Kinesis** : un événement → plusieurs consommateurs, log persistant, rejouable (event sourcing).

#### Exemple RabbitMQ

```javascript
const amqp = require('amqplib');

async function publishOrderCreated(order) {
  const connection = await amqp.connect(process.env.RABBITMQ_URL);
  const channel = await connection.createChannel();
  const exchange = 'events';
  await channel.assertExchange(exchange, 'fanout', { durable: true });

  channel.publish(exchange, '', Buffer.from(JSON.stringify({
    type: 'OrderCreated',
    data: {
      orderId: order.id,
      userId: order.userId,
      eventId: order.eventId,
      amount: order.amount,
      timestamp: new Date().toISOString()
    }
  })));

  await channel.close();
  await connection.close();
}

async function listenForOrders() {
  const connection = await amqp.connect(process.env.RABBITMQ_URL);
  const channel = await connection.createChannel();
  const exchange = 'events';
  await channel.assertExchange(exchange, 'fanout', { durable: true });
  const queue = await channel.assertQueue('payment-queue', { durable: true });
  await channel.bindQueue(queue.queue, exchange, '');

  channel.consume(queue.queue, async (msg) => {
    const event = JSON.parse(msg.content.toString());
    if (event.type === 'OrderCreated') {
      try {
        await processPayment(event.data);
        channel.ack(msg);
      } catch (error) {
        channel.nack(msg, false, true); // retry
      }
    }
  });
}
```

✅ Découplage total — ✅ Résilience (si un consumer est down, les autres continuent) — ✅ Scalabilité (ajout de consumers) — ✅ Performance (réponse immédiate).


### 4.4 Patterns de résilience supplémentaires

#### Timeout

Ne jamais attendre indéfiniment.

```javascript
async function callServiceWithTimeout(url, timeout = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  try {
    const response = await fetch(url, { signal: controller.signal });
    clearTimeout(timeoutId);
    return response;
  } catch (error) {
    if (error.name === 'AbortError') throw new Error('Request timeout');
    throw error;
  }
}
```

#### Retry avec exponential backoff

```javascript
async function retryWithBackoff(fn, maxRetries = 3) {
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

const data = await retryWithBackoff(() =>
  fetch('http://payment-service:3003/process')
);
```

#### Bulkhead Pattern

Isoler les ressources pour éviter qu'une défaillance consomme toutes les connexions.

```javascript
const pLimit = require('p-limit');
const paymentLimit = pLimit(10); // max 10 requêtes simultanées

app.post('/api/orders', async (req, res) => {
  try {
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