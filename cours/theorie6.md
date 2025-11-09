📚 JOUR 6 - ARCHITECTURE SOA ET SERVICES SOAP
Cours Théorique - Mastère 2 (Durée : 3h30)
🎯 OBJECTIFS PÉDAGOGIQUES
À la fin de cette journée, vous serez capables de :

Comprendre les principes fondamentaux de l'architecture SOA
Distinguer SOA, SOAP et REST
Identifier les concepts WSDL et UDDI
Reconnaître les patterns SOA classiques
Évaluer la pertinence de SOA en 2025


📖 PARTIE 1 : LE PARADIGME SOA (50 min)
1.1 L'histoire de SOA : D'où vient ce besoin ?
Le cauchemar de l'intégration (années 1990-2000)
Imaginez une grande banque en 2003. Elle possède :

Un système de gestion des comptes en COBOL sur mainframe IBM (années 1970)
Une application de prêts en Java sur serveur Unix (années 1990)
Un CRM en .NET sur Windows (années 2000)
Des agences qui veulent tout centraliser dans une application web

Le problème : Ces systèmes ne parlent pas la même langue. Chacun a son protocole, son format de données, son architecture. Les faire communiquer ? Mission quasi impossible.
Les tentatives ratées :

CORBA (1991) : Trop complexe, jamais vraiment adopté
DCOM (1996) : Microsoft uniquement, pas cross-platform
RMI (1997) : Java uniquement
EDI : Coûteux, réservé aux grands groupes B2B

L'émergence de SOA (2000-2005)
Le W3C et OASIS (organismes de standardisation) proposent une nouvelle vision : SOA (Service-Oriented Architecture).
La promesse :

"Créez des services réutilisables qui communiquent via des standards ouverts (XML, HTTP), peu importe la technologie sous-jacente."

Les géants technologiques s'alignent :

IBM : Lance WebSphere et investit des milliards
Microsoft : Crée Windows Communication Foundation (WCF)
Oracle : Développe Oracle SOA Suite
SAP : Intègre SOA dans NetWeaver

Entre 2005 et 2010, SOA devient LE paradigme architectural des grandes entreprises. Les budgets se chiffrent en millions, les projets deviennent stratégiques.
La désillusion (2010-2015)
Puis vient la désillusion. Les projets SOA :

Dépassent systématiquement les budgets de 200-300%
Prennent 2-3 ans au lieu de 6 mois promis
Deviennent tellement complexes que personne ne maîtrise l'architecture complète
Nécessitent des armées de consultants très coûteux

En parallèle :

2006 : AWS lance EC2, début du cloud computing
2010 : REST se popularise (défini en 2000 par Roy Fielding)
2012 : Les startups de la Silicon Valley ignorent SOA et construisent des APIs REST simples
2014 : Les microservices émergent comme alternative moderne et agile

Le verdict : SOA n'a pas échoué techniquement, mais a été sur-vendu et mal implémenté. Les principes étaient bons, l'exécution souvent catastrophique.

1.2 Les 8 principes fondamentaux de SOA
SOA n'est pas une technologie, c'est une philosophie architecturale basée sur ces principes :
1. Contrat de service standardisé
Chaque service expose un contrat formel (comme un contrat légal) décrivant exactement ce qu'il fait, sans révéler comment.
Service de Paiement
    ↓
Contrat (WSDL)
    ├─ ProcessPayment(amount, account)
    ├─ RefundPayment(transactionId)
    └─ GetPaymentStatus(transactionId)
Analogie : Vous utilisez une carte bancaire sans connaître les algorithmes de chiffrement. Le contrat est : "je paie, ça fonctionne".
2. Couplage faible (Loose Coupling)
Les services sont indépendants. Modifier l'implémentation d'un service n'impacte pas les autres, tant que le contrat reste identique.
Contre-exemple : Une base de données partagée = couplage fort. Si Service A change un champ, Service B casse.
3. Abstraction
Le service cache sa complexité interne. Les clients voient uniquement le contrat, pas l'implémentation.
Analogie : Quand vous utilisez un GPS, vous ne connaissez pas les algorithmes de calcul d'itinéraire. Vous dites "emmène-moi à Paris" et il le fait.
4. Réutilisabilité
Un service bien conçu peut être utilisé dans différents contextes.
Exemple : Un service "Authentification" utilisé par :

L'application web
L'application mobile
Le portail partenaire
Les APIs publiques

Bénéfice : Développer une fois, utiliser partout.
5. Autonomie
Chaque service contrôle sa propre logique et ses propres données.
✅ Service Commande
    ↓
Base de données Commandes (autonome)

❌ NE doit PAS accéder directement à la base Utilisateurs
✅ DOIT appeler le Service Utilisateur
Principe : Chaque service est un domaine métier autonome.
6. Sans état (Stateless)
Les services ne conservent pas d'état entre les requêtes. Chaque requête contient toute l'information nécessaire.
Comme HTTP : Chaque requête est indépendante et auto-suffisante.
Avantage : Scalabilité horizontale facile.
7. Découvrabilité
Les services sont documentés et référencés dans un annuaire où les développeurs peuvent les trouver.
Concept UDDI : "Pages jaunes" des services web (nous y reviendrons).
8. Composabilité
Les services simples peuvent être combinés pour créer des processus métier complexes.
Processus : Commander un produit
    ↓
Service 1: Vérifier stock
    ↓
Service 2: Calculer prix (promos + taxes)
    ↓
Service 3: Traiter paiement
    ↓
Service 4: Créer commande
    ↓
Service 5: Envoyer confirmation

1.3 Architecture en couches SOA
Une architecture SOA typique s'organise en couches :
┌─────────────────────────────────────────┐
│     PRÉSENTATION                        │  Applications (Web, Mobile)
└────────────┬────────────────────────────┘
             │
┌────────────▼────────────────────────────┐
│     PROCESSUS MÉTIER                    │  Orchestration (BPEL, Workflow)
└────────────┬────────────────────────────┘
             │
┌────────────▼────────────────────────────┐
│     SERVICES MÉTIER                     │  Services exposés (Commande, Paiement)
└────────────┬────────────────────────────┘
             │
┌────────────▼────────────────────────────┐
│     SERVICES INFRASTRUCTURE             │  Sécurité, Logs, Cache
└────────────┬────────────────────────────┘
             │
┌────────────▼────────────────────────────┐
│     DONNÉES                             │  Bases de données, Legacy, ERP
└─────────────────────────────────────────┘
L'ESB (Enterprise Service Bus) : Le cœur d'une architecture SOA. C'est un middleware intelligent qui :

Route les messages entre services
Transforme les formats de données (XML ↔ JSON ↔ CSV)
Gère la sécurité et les transactions
Assure la disponibilité et le load balancing
Implémente les patterns d'intégration

Exemples d'ESB : IBM WebSphere, Oracle Service Bus, MuleSoft, Apache Camel.
Le problème de l'ESB : Il devient souvent un point unique de défaillance et un goulot d'étranglement lorsqu'il contient trop de logique.

### 1.4 SOA vs Microservices vs REST

| **Aspect** | **SOA** | **Microservices** | **REST** |
|------------|---------|-------------------|----------|
| Naissance | 2000-2005 | 2014+ | 2010+ |
| Philosophie | Réutilisation maximale | Agilité et autonomie | Simplicité |
| Protocole dominant | SOAP (XML/HTTP) | HTTP/JSON, gRPC | HTTP/JSON |
| Taille des services | Large (10-100 fonctions) | Petit (1-10 fonctions) | Variable |
| Communication | Via ESB central | Point-à-point | Point-à-point |
| Base de données | Souvent partagée | Une par service | Variable |
| Gouvernance | Centralisée (forte) | Décentralisée | Aucune |
| Contrat | WSDL strict et formel | OpenAPI (optionnel) | Informel |
| Découverte | UDDI | Service Registry | Documentation |
| Complexité | ⚠️⚠️⚠️ Très élevée | ⚠️⚠️ Moyenne | ✅ Simple |
| Coût | 💰💰💰 Très élevé | 💰💰 Moyen | 💰 Abordable |

**En résumé :**

- SOA = Architecture d'entreprise lourde, forte gouvernance, interopérabilité maximale.
- Microservices = SOA moderne, agile, décentralisé, orienté DevOps.
- REST = Style architectural simple pour APIs web.

> *“Microservices are SOA done right.”* — Martin Fowler

Les microservices héritent des bons principes de SOA (couplage faible, autonomie) tout en évitant la complexité d’un ESB centralisé et des standards XML.

### 1.5 Quand utiliser SOA en 2025 ?

**SOA reste pertinent si :**

- ✅ Vous gérez des systèmes legacy très hétérogènes.
- ✅ Vous évoluez dans un secteur régulé (banque, assurance, santé, administration).
- ✅ Vous avez besoin de transactions distribuées ACID.
- ✅ Vos partenaires exigent encore SOAP ou WS-*.
- ✅ Vous devez appliquer une gouvernance et un audit stricts.
- ✅ Vous intégrez des solutions métiers existantes (ERP, mainframe).

**Préférez REST ou les microservices si :**

- ✅ Vous êtes une startup ou une PME.
- ✅ Vous devez livrer rapidement (agilité, déploiements fréquents).
- ✅ Vous exposez des APIs web/mobile publiques.
- ✅ Votre équipe est orientée DevOps / Cloud natif.
- ✅ Votre budget est limité.

**La réalité en 2025 :**

- Les nouvelles architectures naissent rarement en SOA “pur”.
- Les grands groupes migrent progressivement vers des microservices.
- SOAP reste omniprésent dans les systèmes legacy… et le restera.
- Comprendre SOA demeure indispensable pour maintenir ces environnements et concevoir des migrations maîtrisées.

---

## 📖 PARTIE 2 : SOAP ET WSDL (45 min)

### 2.1 SOAP : Le protocole de communication

**SOAP** (*Simple Object Access Protocol*) est un protocole basé sur XML standardisé par le W3C en 2003. Ironie du sort : malgré son nom, il est loin d'être « simple ». L'acronyme a d'ailleurs été abandonné officiellement.

#### Philosophie générale

SOAP considère toute interaction comme un échange de messages XML structurés, véhiculés sur HTTP (mais aussi SMTP, JMS, etc.). Un message SOAP se compose :

```
Message SOAP = Enveloppe
    ├─ Header (optionnel) : métadonnées, sécurité, transactions
    └─ Body : contenu de la requête ou de la réponse
```

#### Exemple : récupérer les détails d’un événement

```xml
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope">
    <soap:Header>
        <Authentication>
            <Token>abc123xyz</Token>
        </Authentication>
    </soap:Header>
    <soap:Body>
        <GetEventDetails>
            <EventID>550e8400</EventID>
        </GetEventDetails>
    </soap:Body>
</soap:Envelope>
```

Réponse possible :

```xml
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope">
    <soap:Body>
        <GetEventDetailsResponse>
            <Event>
                <ID>550e8400</ID>
                <Title>Jazz sous les étoiles</Title>
                <Date>2025-07-15T20:00:00Z</Date>
                <Price>15.00</Price>
            </Event>
        </GetEventDetailsResponse>
    </soap:Body>
</soap:Envelope>
```

**Comparaison REST** :

```http
GET /api/events/550e8400
Authorization: Bearer abc123xyz

{
  "id": "550e8400",
  "title": "Jazz sous les étoiles",
  "date": "2025-07-15T20:00:00Z",
  "price": 15.00
}
```

> Verdict : REST est environ cinq fois plus concis pour le même résultat.

### 2.2 Les spécifications WS-* : la galaxie SOAP

SOAP s'accompagne d'un vaste ensemble de spécifications nommées **WS-*** (*Web Services*). Elles adressent la sécurité, les transactions ou encore la fiabilité :

| Spécification | Rôle principal |
|---------------|----------------|
| WS-Security | Chiffrement et signature des messages |
| WS-Transaction | Transactions distribuées ACID |
| WS-ReliableMessaging | Garantie de livraison des messages |
| WS-Addressing | Routage avancé via intermédiaires |
| WS-Policy | Déclaration de politiques de sécurité / QoS |
| WS-Coordination | Coordination de plusieurs services |

**Problème majeur** : ces spécifications sont inter-dépendantes. Implémenter WS-Security impose WS-Policy ; WS-Transaction requiert WS-Coordination… on construit un véritable château de cartes.

**Réalisme 2025** : très peu d'entreprises vont jusqu'au bout. La plupart se contentent d’un SOAP + HTTPS classique.

### 2.3 WSDL : le contrat de service

**WSDL** (*Web Services Description Language*) décrit intégralement un service SOAP. C’est l’équivalent d’OpenAPI/Swagger pour le monde SOAP.

#### Les cinq blocs d’un WSDL

1. **Types** : schémas de données (XSD).
2. **Messages** : définition des messages de requête et réponse.
3. **PortType** : catalogue des opérations disponibles.
4. **Binding** : protocole utilisé (SOAP document/literal, RPC...).
5. **Service** : endpoints réels (URL, ports).

#### Extrait commenté

```xml
<definitions name="EventService">
  <!-- 1. Types -->
    <types>
        <Event>
            <ID>string</ID>
            <Title>string</Title>
            <Price>decimal</Price>
        </Event>
    </types>
    
  <!-- 2. Messages -->
    <message name="GetEventRequest">
    <part name="eventID" type="string" />
    </message>
    <message name="GetEventResponse">
    <part name="event" type="Event" />
    </message>
    
  <!-- 3. PortType -->
    <portType name="EventServicePort">
        <operation name="GetEvent">
      <input message="GetEventRequest" />
      <output message="GetEventResponse" />
        </operation>
    </portType>
    
  <!-- 4. Binding -->
    <binding name="EventServiceBinding" type="EventServicePort">
    <soap:binding style="document" transport="http" />
    </binding>
    
  <!-- 5. Service -->
    <service name="EventService">
        <port name="EventPort" binding="EventServiceBinding">
      <soap:address location="https://api.smartcity.local/soap/events" />
        </port>
    </service>
</definitions>
```

#### Génération automatique : le grand atout

```bash
# Java
wsimport http://api.smartcity.local/soap/events?wsdl

# .NET
wsdl http://api.smartcity.local/soap/events?wsdl

# Python
python -m zeep http://api.smartcity.local/soap/events?wsdl
```

```java
EventService service = new EventService();
Event event = service.getEvent("550e8400");
System.out.println(event.getTitle()); // "Jazz sous les étoiles"
```

> Côté REST, pas de génération standard (même si OpenAPI comble désormais ce manque).

---

### 2.4 SOAP vs REST : Le verdict pragmatique

#### Tableau comparatif

| Critère | SOAP | REST |
|---------|------|------|
| **Simplicité** | ❌ XML verbeux | ✅ JSON léger |
| **Performance** | ❌ Lourd (~1200 bytes) | ✅ Léger (~250 bytes) |
| **Typage strict** | ✅ WSDL formel | ❌ Informel |
| **Génération code** | ✅ Automatique | ⚠️ Possible (OpenAPI) |
| **Sécurité avancée** | ✅ WS-Security | ⚠️ HTTPS + JWT |
| **Transactions** | ✅ WS-Transaction | ❌ Pas de standard |
| **Support navigateur** | ❌ Non natif | ✅ Natif (fetch/axios) |
| **Adoption 2025** | ❌ Déclin | ✅ Dominant |

#### Ce qu’il faut retenir

- **REST** domine les nouvelles applications : léger, rapide, compatible navigateur, facile à documenter.
- **SOAP** conserve un avantage sur :
  - Les transactions distribuées ACID.
  - La génération de clients strongly-typed sans effort.
  - Les exigences de sécurité message-level (WS-Security).
- En 2025, SOAP reste très présent dans les systèmes legacy et secteurs régulés (banque, santé, gouvernement).

#### Quand choisir quoi ?

- Privilégiez **SOAP** si :
  - Vos partenaires imposent déjà des services SOAP/WSDL.
  - Vous gérez des transactions critiques multi-systèmes.
  - Vous avez besoin de sécurité message-level et d’un cadre strict.
- Choisissez **REST** si :
  - Vous exposez des APIs web/mobile publiques.
  - Vous cherchez la simplicité, la rapidité de développement et l’adoption large.
  - Vous voulez profiter pleinement de l’écosystème moderne (API Gateway, SPA, mobile, IoT).

> **Synthèse 2025** : REST a gagné la bataille du web grand public. SOAP survit là où la gouvernance, les contrats stricts et la compatibilité legacy priment sur la simplicité.

---

## 📖 PARTIE 3 : UDDI ET DÉCOUVERTE (25 min)

### 3.1 UDDI : le rêve d'un annuaire universel

**UDDI** (*Universal Description, Discovery and Integration*) se voulait le *Pages Jaunes mondial* des services SOAP.

> "Publions tous nos services dans un registre unique et trouvons automatiquement nos partenaires B2B." — Vision 2001

**Les promoteurs** : IBM, Microsoft, SAP réunis derrière l'UBR (*UDDI Business Registry*).

**Promesse initiale**

- Publication d’un service (ex. « Calcul TVA UE »).
- Découverte automatique par tous les partenaires.
- Intégration B2B sans friction.

**La réalité (2006)** : fermeture de l’UBR public après un échec cuisant.

| Raisons de l’échec | Détails |
|--------------------|---------|
| Complexité excessive | API UDDI en SOAP, implémentations lourdes |
| Sécurité floue | Qui publie ? Qui valide ? |
| Manque de confiance | Les entreprises hésitent à exposer leurs services |
| Effet boule de neige | Peu de services → Peu d’adoption → Encore moins de services |
| Concurrence de la simplicité | Google devient l’annuaire réel des APIs |

> Aujourd’hui : UDDI survit uniquement dans quelques registres privés internes.

---

### 3.2 Structure conceptuelle d’UDDI

Un registre UDDI se découpe en trois « pages » :

- **White Pages** : identité de l’entreprise (nom, contact, adresse).
- **Yellow Pages** : classification par catégories métier.
- **Green Pages** : détails techniques (URL, WSDL, protocoles).

**Métaphore** : un annuaire téléphonique complet (Qui ? Dans quel domaine ? Comment le joindre techniquement ?).

---

### 3.3 Alternatives modernes à UDDI

Le besoin de découverte n’a pas disparu, mais les solutions ont évolué :

#### Service Discovery interne (microservices)

- **Consul (HashiCorp)** : registre distribué, health checks, DNS intégré.
- **Eureka (Netflix OSS)** : registry + load balancing côté client, résilience.
- **Kubernetes** : découverte native via DNS, labels et sélecteurs.

#### Marketplaces d’APIs

- **RapidAPI, Postman Public API Network** : milliers d’APIs publiques, doc interactive, onboarding rapide.
- Pas de découverte automatique, mais une **expérience développeur** exemplaire.

> **Leçon clé** : UDDI a échoué parce que trop complexe. Les solutions modernes misent sur la simplicité, l’automatisation et une UX pragmatique.

---

## 📖 PARTIE 4 : PATTERNS SOA (40 min)

### 4.1 Patterns de conception de services

| Pattern | Problème | Solution | Bénéfice |
|---------|----------|----------|----------|
| **Service Façade** | Système legacy avec dizaines de méthodes exposées, incompréhensible pour les clients | Créer une façade simplifiée qui expose uniquement les opérations nécessaires | Interface claire, complexité encapsulée |
| **Service Adapter** | Source de données qui ne parle pas SOAP (BDD, fichier, API REST) | Adapter qui transforme SOAP ↔ protocole natif | Uniformisation des interfaces |
| **Canonical Data Model** | Chaque service possède son propre format | Définir un modèle canonique et laisser l’ESB transformer | N transformations seulement (vs N×(N-1)) |

```text
Client → Service Façade → Legacy (50 méthodes)
             ↓
        expose 5 méthodes clés
```

```text
SOAP Request → Adapter → SQL Query → Base
```

```text
Service A → ESB → Format canonique → ESB → Service B
```

### 4.2 Patterns d’orchestration et chorégraphie

#### Pattern 4 : Orchestration (chef d’orchestre)

```text
Orchestrateur
  ├─ Vérifier stock
  ├─ Calculer prix
  ├─ Traiter paiement
  ├─ Créer commande
  └─ Envoyer confirmation
```

- **Outil historique** : BPEL.
- **Avantage** : supervision centralisée, traçabilité.
- **Limite** : point unique de défaillance.

#### Pattern 5 : Chorégraphie (partition partagée)

```text
Service A → publie “OrderCreated”
     ↓
Service B → traite → publie “PaymentProcessed”
     ↓
Service C → traite → publie “OrderShipped”
```

- **Avantage** : résilience, découplage.
- **Limite** : supervision plus complexe.
- **Modernité** : architectures event-driven (Kafka, RabbitMQ, SNS/SQS).

### 4.3 Patterns de fiabilité

| Pattern | Pourquoi | Principe clé |
|---------|---------|--------------|
| **Idempotence** | Réseau instable → doublons possibles | Rejouer un message ne change pas l’état. Exemple : `createOrder(123)` ne crée qu’une seule commande. |
| **Circuit Breaker** | Service aval en panne → cascade de timeouts | Disjoncter après X échecs, passer en half-open pour tester la reprise. |
| **Retry + Exponential Backoff** | Service temporairement indisponible | Réessayer avec délais croissants (1s, 2s, 4s, 8s…) pour éviter d’aggraver la situation. |

```text
Tentative 1 → échec → attendre 1s
Tentative 2 → échec → attendre 2s
...
Tentative 5 → abandon
```

> Ces patterns sont aujourd’hui intégrés nativement dans de nombreux frameworks (Hystrix/Resilience4j, AWS SDK, gRPC, etc.).

---

### 4.4 Anti-patterns SOA (à éviter absolument)

| Anti-pattern | Symptôme | Conséquence | Solution |
|--------------|----------|-------------|----------|
| **God Service** | Service unique avec 150 opérations (`UserManagementService`) | Intrinsèquement monolithique, évolutivité impossible | Découper par domaines cohésifs (User, Order, Payment…) |
| **Chatty Services** | 5 appels réseau pour afficher un profil (`GetUser` + `GetAddress`…) | Latence élevée, fragilité réseau | Introduire une façade (`GetCompleteUserProfile`) |
| **ESB Spaghetti** | ESB bourré de règles métier | Goulot d’étranglement incompréhensible | Garder l’ESB sur les préoccupations transverses, laisser la logique aux services |

### 🎓 Conclusion : SOA en perspective

#### L’héritage de SOA

- ✅ Couplage faible indispensable à l’évolutivité.
- ✅ Contrats clairs pour collaborer entre équipes.
- ✅ Abstraction pour faire évoluer l’implémentation sans casser les clients.
- ✅ Réutilisabilité = économies sur le long terme.
- ✅ Patterns d’intégration (façade, adapter, orchestration) toujours pertinents.

#### Les erreurs à éviter

- ❌ Sur-ingénierie (WS-*, UDDI).
- ❌ Gouvernance bloquante.
- ❌ ESB transformé en monstre central.
- ❌ Complexité qui étouffe la valeur métier.
- ❌ Registres publics sans confiance.

#### SOA vs Microservices : l’évolution naturelle

| Aspect | SOA | Microservices |
|--------|-----|---------------|
| Philosophie | Réutilisation maximale | Agilité & autonomie |
| Communication | ESB central | Point-à-point |
| Gouvernance | Centralisée | Décentralisée |
| Technologies | SOAP / XML | HTTP / JSON, gRPC |
| Déploiement | Monolithique ou par service | Conteneurs indépendants |
| Organisation | Équipes par couche | Équipes par domaine |

> “Microservices are SOA done right. We kept the good principles and eliminated the complexity.” — Martin Fowler

#### Quand choisir quoi ?

**SOA / SOAP pertinent si :**

- ✅ Grand compte avec lourd héritage SOAP.
- ✅ Secteur régulé (banque, santé, administration).
- ✅ Partenaires B2B imposant SOAP.
- ✅ Transactions distribuées ACID.
- ✅ Besoin de sécurité message-level (WS-Security).

**Microservices / REST si :**

- ✅ Nouvelle application ou modernisation.
- ✅ Besoin d’agilité & CI/CD.
- ✅ Équipes DevOps autonomes.
- ✅ APIs web/mobile publiques.
- ✅ Scalabilité indépendante par service.

---

#### Les chiffres qui parlent

- 65 % des nouvelles architectures = Microservices/REST.
- 25 % = Monolithes (PME, startups).
- 10 % = SOA/SOAP (maintenance, secteurs régulés).
- 40 % des systèmes d’entreprise existants restent en SOA/SOAP → migration sur 10-20 ans.

> SOA est un héritage à comprendre, pas à ignorer.

#### Schéma d’évolution

```text
1990s       2000s         2010s           2020s
Monolithe → SOA/SOAP → REST APIs → Microservices
```

#### Patterns SOA toujours utiles

Service Façade, Service Adapter, Canonical Data Model, Orchestration → Saga, Chorégraphie → Event-driven, Circuit Breaker, Idempotence, Retry/Backoff.

> Les patterns restent, les implémentations changent.

#### Stratégie de migration SOA → Microservices

1. **Strangler Fig** : envelopper l’ancien système et migrer service par service.
2. **API Gateway** : proxy unique vers SOAP ou REST.
3. **Itération** : migrer un service à la fois (2-5 ans pour un grand groupe).

#### Ressources pour aller plus loin

- 📘 *Enterprise Integration Patterns* — Gregor Hohpe.
- 📗 *SOA Patterns* — Arnon Rotem-Gal-Oz.
- 📕 *Building Microservices* — Sam Newman.
- W3C SOAP, WSDL 2.0, OASIS Web Services.
- Outils : SoapUI, Postman, Apache Camel.
- Pour la transition : article “Microservices” de Martin Fowler, *Monolith to Microservices*.

#### Exercices pratiques

1. Appeler un service SOAP public avec SoapUI et analyser le WSDL.
2. Implémenter une même feature en SOAP et REST, comparer.
3. Tester un ESB (Apache Camel) : SOAP → transformation → REST.
4. Auditer une architecture SOA existante et proposer un plan de modernisation.

#### Citations

> “SOA promised heaven but delivered hell. Microservices learned from those mistakes.” — Anonymous DevOps Engineer

> “Understanding SOA is like understanding Latin...” — Software Architecture Wisdom

#### Pourquoi étudier SOA en 2025 ?

1. **Héritage technologique** massif à maintenir/migrer.
2. **Compréhension historique** des microservices.
3. **Patterns intemporels** réutilisés partout.

### 📊 SOA en un coup d’œil

| Aspect | Description | Pertinence 2025 |
|--------|-------------|-----------------|
| Principes | Couplage faible, réutilisabilité, abstraction | ✅ Toujours valides |
| SOAP | Protocole XML verbeux | ⚠️ Legacy uniquement |
| WSDL | Contrat formel | ⚠️ Remplacé par OpenAPI |
| UDDI | Annuaire de services | ❌ Remplacé par registres simples |
| ESB | Bus central | ⚠️ API Gateway / Service Mesh |
| Patterns | Façade, Adapter, Circuit Breaker | ✅ Intemporels |
| WS-* | Specs sécurité / transaction | ⚠️ Complexité élevée |

### 🎯 Mémo rapide (10 points clés)

1. SOA = philosophie.
2. SOAP = verbeux ; REST domine.
3. WSDL = génération automatique.
4. UDDI = échec, simplicité gagne.
5. ESB = attention au silo central.
6. Microservices = SOA modernisé.
7. Patterns SOA = toujours utiles.
8. WS-* = sur-ingénierie.
9. Legacy SOA = compétence recherchée.
10. Comprendre le passé pour mieux bâtir l’avenir.

💡 **Mot de la fin** : SOA n’est pas une relique à enterrer, mais un socle à connaître pour mieux construire vos architectures modernes.

**Prochaines étapes :**

- Appliquer ces notions sur SmartCity.
- Explorer un service SOAP réel (SoapUI).
- Planifier la modernisation d’un système legacy.
- Cartographier les emprunts des microservices à SOA.

[FIN DU JOUR 6 - ARCHITECTURE SOA ET SERVICES SOAP]