📚 JOUR 3 - DÉVELOPPEMENT D'API REST AVANCÉ
Cours Théorique - Mastère 2 (Durée : 3h30)

🎯 OBJECTIFS PÉDAGOGIQUES
À la fin de cette matinée, vous serez capables de :

Choisir et implémenter une stratégie de versioning d'API appropriée
Gérer la rétrocompatibilité et la dépréciation d'endpoints
Maîtriser OpenAPI/Swagger pour documenter vos APIs de manière professionnelle
Comprendre HATEOAS et son application pratique
Générer une documentation interactive automatique
Appliquer les bonnes pratiques de documentation d'API


📖 PARTIE 1 : VERSIONING D'API (55 min)
1.1 Pourquoi versionner une API ?
Imaginons une startup de livraison de repas qui lance son API en 2020. Les restaurants peuvent lister leurs plats, les clients peuvent commander. L'endpoint principal est GET /orders qui renvoie :
json{
  "id": 123,
  "customer_name": "Marie Dupont",
  "total": 45.50,
  "items": ["Pizza Margherita", "Salade César"]
}
Deux ans plus tard, en 2022, l'entreprise veut améliorer son API. Ils réalisent que renvoyer juste les noms des items est insuffisant. Ils veulent renvoyer des objets complets avec prix, quantité, options. La nouvelle structure serait :
json{
  "id": 123,
  "customer": {
    "id": 456,
    "name": "Marie Dupont",
    "email": "marie@example.com"
  },
  "total": 45.50,
  "items": [
    {
      "id": 789,
      "name": "Pizza Margherita",
      "price": 12.50,
      "quantity": 2
    }
  ]
}
Problème : si vous changez directement l'API existante, vous cassez toutes les applications clientes qui s'attendent à l'ancien format. Des centaines de restaurants utilisent leur application mobile qui parse customer_name comme une string. Avec le nouveau format, elle crashera en essayant de lire customer.name.
C'est exactement pour ça qu'on versionne les APIs. Le versioning permet de faire évoluer votre API sans casser les clients existants. Les anciens clients continuent d'utiliser v1, les nouveaux clients utilisent v2.
1.2 Quand créer une nouvelle version ?
Tous les changements ne nécessitent pas une nouvelle version. Il faut distinguer les changements compatibles (non-breaking) des changements incompatibles (breaking).
Changements compatibles (pas besoin de nouvelle version)
Ajouter un nouveau champ optionnel : Si vous ajoutez phone_number à la réponse, les clients existants l'ignoreront simplement. Pas de casse.
Ajouter un nouvel endpoint : POST /orders/bulk pour créer plusieurs commandes en une fois. Les clients qui ne l'utilisent pas ne sont pas impactés.
Ajouter une nouvelle valeur dans un enum : Si vous aviez status: ["pending", "delivered"] et que vous ajoutez "cancelled", les clients existants continueront de gérer pending et delivered. Ils devront être mis à jour pour gérer cancelled, mais ça ne cassera pas leur code existant.
Rendre un champ obligatoire optionnel : Si customer_name était obligatoire et devient optionnel, les clients qui l'envoient toujours continueront de fonctionner.
Changements incompatibles (nouvelle version nécessaire)
Renommer un champ : customer_name → customer.name. Les clients qui lisent customer_name ne trouveront plus ce champ.
Changer le type d'un champ : total était un number, devient un string "45.50 EUR". Les clients qui parsent un number crasheront.
Supprimer un champ : Si vous enlevez delivery_address, les clients qui en ont besoin ne pourront plus fonctionner.
Changer la structure d'un champ : items était un array de strings, devient un array d'objets. Breaking change massif.
Rendre un champ optionnel obligatoire : Si email était optionnel et devient obligatoire, les clients qui ne l'envoient pas auront des erreurs 422.
Changer le comportement d'un endpoint : DELETE /users/123 supprimait définitivement, maintenant il fait un soft delete. Même si la structure de réponse ne change pas, le comportement change, c'est potentiellement breaking.
Règle d'or : En cas de doute, considérez que c'est un breaking change et créez une nouvelle version.
1.3 Les trois stratégies de versioning
Il existe trois approches principales pour versionner une API. Chacune a ses avantages et inconvénients.
Stratégie 1 : Versioning dans l'URI (la plus courante)
https://api.example.com/v1/users
https://api.example.com/v2/users
https://api.example.com/v3/users
C'est l'approche la plus répandue et la plus intuitive. La version fait partie de l'URL elle-même.
Avantages :

Visible et explicite : Rien qu'en regardant l'URL, vous savez quelle version vous utilisez
Cache-friendly : Les proxies et CDNs peuvent cacher v1 et v2 séparément sans confusion
Simple à tester : Dans Postman, vous changez juste l'URL
Compatible navigateur : Vous pouvez tester dans un navigateur directement

Inconvénients :

Pas très "RESTful pur" : Les puristes arguent que /users/123 devrait pointer vers LA ressource utilisateur, pas vers "la version 1 de la ressource utilisateur"
URLs multiples pour la même ressource : /v1/users/123 et /v2/users/123 sont deux URLs différentes pour le même utilisateur conceptuel

Implémentation :
javascript// Node.js/Express
const v1Router = express.Router();
const v2Router = express.Router();

v1Router.get('/users/:id', (req, res) => {
  // Ancienne logique
  res.json({ id: req.params.id, name: 'Marie' });
});

v2Router.get('/users/:id', (req, res) => {
  // Nouvelle logique
  res.json({ 
    id: req.params.id, 
    profile: { name: 'Marie', email: 'marie@example.com' }
  });
});

app.use('/api/v1', v1Router);
app.use('/api/v2', v2Router);
Variante : Sous-domaine
https://v1.api.example.com/users
https://v2.api.example.com/users
Même principe, mais avec un sous-domaine. Utilisé par certaines grandes entreprises (AWS par exemple utilise des sous-domaines pour certains services).
Stratégie 2 : Versioning par Header
GET /users/123
Accept: application/vnd.example.v1+json

GET /users/123
Accept: application/vnd.example.v2+json
La version est spécifiée dans un header HTTP, typiquement Accept ou un header custom comme API-Version.
Avantages :

RESTful pur : L'URL reste la même, seule la représentation change
URL unique : /users/123 pointe toujours vers l'utilisateur 123, quelle que soit la version
Flexible : Vous pouvez avoir différentes versions pour différents formats (v1 en JSON, v1 en XML...)

Inconvénients :

Moins visible : Vous ne voyez pas la version dans l'URL, il faut regarder les headers
Difficile à tester dans un navigateur : Vous ne pouvez pas simplement coller l'URL
Complexité du caching : Les CDNs doivent être configurés pour cacher selon les headers, plus complexe
Erreurs plus fréquentes : Les développeurs oublient souvent de spécifier le header

Implémentation :
javascriptapp.get('/api/users/:id', (req, res) => {
  const version = req.get('API-Version') || 'v1';
  
  if (version === 'v1') {
    res.json({ id: req.params.id, name: 'Marie' });
  } else if (version === 'v2') {
    res.json({ 
      id: req.params.id, 
      profile: { name: 'Marie', email: 'marie@example.com' }
    });
  } else {
    res.status(400).json({ error: 'Unsupported API version' });
  }
});
Utilisé par : GitHub API v3, Stripe (avec Stripe-Version), Twilio.
Stratégie 3 : Versioning par Query Parameter
GET /users/123?version=1
GET /users/123?version=2
La version est passée comme un paramètre de requête.
Avantages :

Visible dans l'URL : Comme l'approche URI, mais sans changer le chemin de base
Facile à tester : Dans un navigateur ou Postman
Optional avec default : Si pas de paramètre, utiliser une version par défaut

Inconvénients :

Mélange version et filtres : /users?version=2&status=active&page=1 peut devenir confus
Moins propre : Le query parameter est généralement réservé pour les filtres et options
Rarement utilisé en pratique : Peu d'APIs majeures utilisent cette approche

Implémentation :
javascriptapp.get('/api/users/:id', (req, res) => {
  const version = req.query.version || '1';
  
  if (version === '1') {
    res.json({ id: req.params.id, name: 'Marie' });
  } else if (version === '2') {
    res.json({ 
      id: req.params.id, 
      profile: { name: 'Marie', email: 'marie@example.com' }
    });
  }
});
Quelle stratégie choisir ?
Pour 90% des cas, versioning dans l'URI (/v1/, /v2/) est le meilleur choix. C'est ce que font Google, Twitter, Facebook, Amazon, Netflix. C'est simple, explicite, et tout le monde comprend.
Utilisez le versioning par header si vous avez des contraintes spécifiques (compatibilité avec des standards existants, architecture complexe avec négociation de contenu avancée).
Évitez le versioning par query parameter sauf si vous avez une raison très spécifique.
1.4 Schéma de versioning sémantique
Deux approches principales pour numéroter vos versions :
Version majeure uniquement : v1, v2, v3...

Simple, clair
Incrémentez seulement pour les breaking changes
Utilisé par la plupart des APIs publiques

Versioning sémantique complet : v1.2.3

Major.Minor.Patch (Semver)
Incrémentez Major pour breaking changes, Minor pour nouvelles features compatibles, Patch pour bug fixes
Plus d'information, mais plus complexe pour une API publique

Pour les APIs REST publiques, restez sur les versions majeures simples : v1, v2, v3. Gardez le semver complet pour vos bibliothèques et SDKs.
1.5 Gestion de la rétrocompatibilité
Une fois que vous avez plusieurs versions, vous devez les maintenir. Mais combien de temps ?
Politique de support
Définissez et communiquez clairement votre politique. Exemple :
v1 : Stable, supportée jusqu'au 31/12/2025
v2 : Stable, supportée jusqu'au 31/12/2027
v3 : Beta, production ready Q2 2026
Règle classique : Supportez au minimum les 2 dernières versions majeures. Quand v4 sort, v2 reste supportée, mais v1 entre en dépréciation.
Stratégie de sunset (coucher de soleil)
Quand vous voulez retirer une ancienne version :
6-12 mois avant : Annoncez la date de sunset dans votre blog, newsletter, documentation.
3-6 mois avant : Ajoutez un header de dépréciation dans les réponses :
httpSunset: Sat, 31 Dec 2025 23:59:59 GMT
Deprecation: Sat, 01 Jan 2025 00:00:00 GMT
Link: <https://api.example.com/docs/migration-v2-to-v3>; rel="successor-version"
1 mois avant : Contactez directement les gros clients qui utilisent encore l'ancienne version. Offrez du support pour migrer.
Jour J : La version ancienne commence à renvoyer 410 Gone au lieu de fonctionner normalement.
json{
  "error": "API v1 has been sunset on December 31, 2025",
  "migration_guide": "https://api.example.com/docs/migration-v1-to-v2"
}
Maintien de deux versions en parallèle
Comment maintenir v1 et v2 sans dupliquer tout le code ?
Approche 1 : Adapters/Transformers
Vous avez une logique métier unique, et des adapters qui transforment les données pour chaque version.
javascript// Logique métier (version agnostique)
function getUserData(id) {
  return database.query('SELECT * FROM users WHERE id = ?', [id]);
}

// Adapter v1
function transformToV1(userData) {
  return {
    id: userData.id,
    name: userData.full_name,
    email: userData.email_address
  };
}

// Adapter v2
function transformToV2(userData) {
  return {
    id: userData.id,
    profile: {
      name: userData.full_name,
      email: userData.email_address,
      phone: userData.phone_number
    },
    metadata: {
      created_at: userData.created_at,
      updated_at: userData.updated_at
    }
  };
}
Approche 2 : Feature flags
Pour des changements mineurs, utilisez des feature flags dans le code.
javascriptfunction getUser(id, apiVersion) {
  const user = database.query('SELECT * FROM users WHERE id = ?', [id]);
  
  if (apiVersion === 'v1') {
    return { id: user.id, name: user.full_name };
  } else {
    return { id: user.id, profile: { name: user.full_name, email: user.email }};
  }
}
Attention : cette approche devient vite ingérable si vous avez trop de versions. Préférez les adapters.
1.6 Dépréciation d'endpoints individuels
Parfois, vous ne voulez pas déprécier toute une version, juste un endpoint spécifique.
Utilisez le header Deprecation (RFC 8594) :
httpGET /users/123/legacy-stats

HTTP/1.1 200 OK
Deprecation: Sat, 01 Jan 2026 00:00:00 GMT
Link: <https://api.example.com/users/123/analytics>; rel="alternate"
Sunset: Sat, 01 Jul 2026 23:59:59 GMT

{
  "data": "...",
  "deprecation_notice": "This endpoint is deprecated. Please migrate to /users/{id}/analytics"
}
Documentez clairement l'alternative et la timeline.

# 📝 PARTIE 2 : DOCUMENTATION D'API (70 min)

## 2.1 Pourquoi documenter est crucial

Une API sans documentation est comme un restaurant sans menu. Techniquement, vous pouvez demander au serveur "qu'est-ce que vous avez ?", mais c'est laborieux et frustrant.

Imaginons un développeur externe qui découvre votre API SmartCity. Il veut intégrer les événements culturels dans son application. Sans documentation :

- Il ne sait pas quels endpoints existent
- Il ne sait pas quel format envoyer pour créer un événement
- Il ne sait pas quelles erreurs peuvent survenir
- Il ne sait pas s'il doit s'authentifier
- Il ne sait pas quelles limites de rate limiting existent

Résultat : il abandonne et va voir une API concurrente mieux documentée. Une bonne documentation n'est pas un luxe, c'est une nécessité absolue.

## 2.2 OpenAPI Specification : le standard de l'industrie

OpenAPI (anciennement Swagger) est devenu LE standard pour documenter les APIs REST. C'est un format YAML (ou JSON) qui décrit complètement votre API de manière machine-readable.

### Histoire rapide

En 2011, une entreprise appelée Wordnik crée Swagger pour documenter leur propre API. En 2015, Swagger est racheté par SmartBear et devient open-source. En 2016, la spécification est donnée à la Linux Foundation et renommée OpenAPI Specification (OAS). Aujourd'hui, c'est le standard adopté par Google, Microsoft, IBM, etc.

### Structure de base d'un fichier OpenAPI

Voici un exemple complet et détaillé pour le service Events de SmartCity :
```yaml
openapi: 3.0.3
info:
  title: SmartCity Events API
  version: 1.0.0
  description: |
    API pour gérer les événements culturels de la ville.
    
    ## Authentification
    La plupart des endpoints nécessitent une authentification JWT.
    Obtenez un token via /auth/login et incluez-le dans le header Authorization.
    
    ## Rate Limiting
    100 requêtes par minute par IP pour les endpoints publics.
    1000 requêtes par minute pour les utilisateurs authentifiés.
    
  contact:
    name: Équipe Events
    email: events@smartcity.local
    url: https://smartcity.local/support
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.smartcity.local/v1
    description: Production
  - url: https://api-staging.smartcity.local/v1
    description: Staging
  - url: http://localhost:3000/api/v1
    description: Développement local

paths:
  /events:
    get:
      summary: Liste des événements
      description: |
        Retourne tous les événements à venir, triés par date de début.
        Supporte la pagination, le filtrage et le tri.
      operationId: listEvents
      tags:
        - Events
      parameters:
        - name: page
          in: query
          description: Numéro de page (commence à 1)
          required: false
          schema:
            type: integer
            minimum: 1
            default: 1
          example: 1
        - name: limit
          in: query
          description: Nombre d'événements par page
          required: false
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
          example: 20
        - name: type
          in: query
          description: Filtrer par type d'événement
          required: false
          schema:
            type: string
            enum: [concert, exposition, conference, festival, sport, autre]
          example: concert
        - name: city
          in: query
          description: Filtrer par ville
          required: false
          schema:
            type: string
          example: Toulouse
        - name: date_from
          in: query
          description: Date de début minimale (ISO 8601)
          required: false
          schema:
            type: string
            format: date-time
          example: "2025-07-01T00:00:00Z"
        - name: date_to
          in: query
          description: Date de début maximale (ISO 8601)
          required: false
          schema:
            type: string
            format: date-time
          example: "2025-12-31T23:59:59Z"
        - name: sort
          in: query
          description: |
            Critère de tri. Préfixez avec - pour un tri descendant.
            Exemples: start_date, -price, title
          required: false
          schema:
            type: string
            enum: [start_date, -start_date, price, -price, title, -title]
            default: start_date
          example: start_date
      responses:
        '200':
          description: Liste paginée d'événements
          content:
            application/json:
              schema:
                type: object
                required:
                  - data
                  - meta
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Event'
                  meta:
                    $ref: '#/components/schemas/PaginationMeta'
                  links:
                    $ref: '#/components/schemas/PaginationLinks'
              examples:
                success:
                  summary: Réponse avec événements
                  value:
                    data:
                      - id: "550e8400-e29b-41d4-a716-446655440000"
                        title: "Jazz sous les étoiles"
                        description: "Soirée jazz en plein air avec des artistes locaux"
                        type: "concert"
                        start_date: "2025-07-15T20:00:00Z"
                        end_date: "2025-07-15T23:00:00Z"
                        location:
                          name: "Place du Capitole"
                          address: "Place du Capitole, 31000 Toulouse"
                          city: "Toulouse"
                          postal_code: "31000"
                          coordinates:
                            latitude: 43.6047
                            longitude: 1.4442
                        price: 15.00
                        max_attendees: 200
                        current_attendees: 45
                        image_url: "https://cdn.smartcity.local/events/jazz-stars.jpg"
                        tags: ["musique", "jazz", "extérieur"]
                        created_at: "2025-06-01T10:00:00Z"
                        updated_at: "2025-06-15T14:30:00Z"
                      - id: "660e8400-e29b-41d4-a716-446655440001"
                        title: "Exposition Picasso"
                        description: "Rétrospective complète de l'œuvre de Picasso"
                        type: "exposition"
                        start_date: "2025-07-20T10:00:00Z"
                        end_date: "2025-09-30T18:00:00Z"
                        location:
                          name: "Musée des Augustins"
                          address: "21 Rue de Metz, 31000 Toulouse"
                          city: "Toulouse"
                          postal_code: "31000"
                        price: 0
                        max_attendees: null
                        current_attendees: 0
                        image_url: "https://cdn.smartcity.local/events/picasso.jpg"
                        tags: ["art", "culture", "peinture"]
                        created_at: "2025-05-15T09:00:00Z"
                        updated_at: "2025-05-15T09:00:00Z"
                    meta:
                      page: 1
                      limit: 20
                      total: 45
                      total_pages: 3
                    links:
                      self: "/events?page=1&limit=20"
                      next: "/events?page=2&limit=20"
                      last: "/events?page=3&limit=20"
                empty:
                  summary: Aucun événement trouvé
                  value:
                    data: []
                    meta:
                      page: 1
                      limit: 20
                      total: 0
                      total_pages: 0
        '400':
          $ref: '#/components/responses/BadRequest'
        '500':
          $ref: '#/components/responses/InternalError'

    post:
      summary: Créer un événement
      description: |
        Crée un nouvel événement culturel.
        Nécessite une authentification et le rôle 'organizer' ou 'admin'.
      operationId: createEvent
      tags:
        - Events
      security:
        - bearerAuth: []
      requestBody:
        required: true
        description: Données du nouvel événement
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EventInput'
            examples:
              concert:
                summary: Création d'un concert
                value:
                  title: "Concert de Rock"
                  description: "Soirée rock avec groupes locaux et nationaux"
                  type: "concert"
                  start_date: "2025-08-15T20:00:00Z"
                  end_date: "2025-08-15T23:30:00Z"
                  location:
                    name: "Zenith de Toulouse"
                    address: "11 Avenue Raymond Badiou, 31300 Toulouse"
                    city: "Toulouse"
                    postal_code: "31300"
                  price: 25.00
                  max_attendees: 5000
                  image_url: "https://cdn.smartcity.local/events/rock-night.jpg"
                  tags: ["musique", "rock", "concert"]
              exposition:
                summary: Création d'une exposition gratuite
                value:
                  title: "Photographies du Monde"
                  description: "Exposition de photographies de voyages"
                  type: "exposition"
                  start_date: "2025-09-01T10:00:00Z"
                  end_date: "2025-10-31T18:00:00Z"
                  location:
                    name: "Galerie Municipale"
                    address: "5 Rue de la République, 31000 Toulouse"
                    city: "Toulouse"
                    postal_code: "31000"
                  price: 0
                  tags: ["photographie", "culture", "voyage"]
      responses:
        '201':
          description: Événement créé avec succès
          headers:
            Location:
              description: URL du nouvel événement
              schema:
                type: string
                format: uri
              example: "/events/770e8400-e29b-41d4-a716-446655440002"
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Event'
              example:
                id: "770e8400-e29b-41d4-a716-446655440002"
                title: "Concert de Rock"
                description: "Soirée rock avec groupes locaux et nationaux"
                type: "concert"
                start_date: "2025-08-15T20:00:00Z"
                end_date: "2025-08-15T23:30:00Z"
                location:
                  name: "Zenith de Toulouse"
                  address: "11 Avenue Raymond Badiou, 31300 Toulouse"
                  city: "Toulouse"
                  postal_code: "31300"
                price: 25.00
                max_attendees: 5000
                current_attendees: 0
                image_url: "https://cdn.smartcity.local/events/rock-night.jpg"
                tags: ["musique", "rock", "concert"]
                created_at: "2025-06-20T15:45:00Z"
                updated_at: "2025-06-20T15:45:00Z"
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '422':
          $ref: '#/components/responses/ValidationError'
        '500':
          $ref: '#/components/responses/InternalError'

  /events/{id}:
    parameters:
      - name: id
        in: path
        required: true
        description: ID unique de l'événement (UUID v4)
        schema:
          type: string
          format: uuid
        example: "550e8400-e29b-41d4-a716-446655440000"

    get:
      summary: Détails d'un événement
      description: Récupère les détails complets d'un événement spécifique
      operationId: getEvent
      tags:
        - Events
      responses:
        '200':
          description: Détails de l'événement
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Event'
              example:
                id: "550e8400-e29b-41d4-a716-446655440000"
                title: "Jazz sous les étoiles"
                description: "Soirée jazz en plein air avec des artistes locaux"
                type: "concert"
                start_date: "2025-07-15T20:00:00Z"
                end_date: "2025-07-15T23:00:00Z"
                location:
                  name: "Place du Capitole"
                  address: "Place du Capitole, 31000 Toulouse"
                  city: "Toulouse"
                  postal_code: "31000"
                  coordinates:
                    latitude: 43.6047
                    longitude: 1.4442
                price: 15.00
                max_attendees: 200
                current_attendees: 45
                image_url: "https://cdn.smartcity.local/events/jazz-stars.jpg"
                tags: ["musique", "jazz", "extérieur"]
                created_at: "2025-06-01T10:00:00Z"
                updated_at: "2025-06-15T14:30:00Z"
        '404':
          $ref: '#/components/responses/NotFound'
        '500':
          $ref: '#/components/responses/InternalError'

    patch:
      summary: Modifier un événement
      description: |
        Modifie partiellement un événement existant.
        Seuls les champs fournis seront modifiés.
        Nécessite d'être le créateur de l'événement ou admin.
      operationId: updateEvent
      tags:
        - Events
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EventUpdate'
            example:
              title: "Jazz sous les étoiles - Edition 2025"
              price: 20.00
              max_attendees: 250
      responses:
        '200':
          description: Événement modifié avec succès
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Event'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          $ref: '#/components/responses/NotFound'
        '422':
          $ref: '#/components/responses/ValidationError'

    delete:
      summary: Supprimer un événement
      description: |
        Supprime définitivement un événement.
        Seul le créateur ou un admin peut supprimer.
      operationId: deleteEvent
      tags:
        - Events
      security:
        - bearerAuth: []
      responses:
        '204':
          description: Événement supprimé avec succès
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          $ref: '#/components/responses/NotFound'

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: |
        JWT token obtenu via POST /auth/login.
        Format du header: Authorization: Bearer 

  schemas:
    Event:
      type: object
      required:
        - id
        - title
        - type
        - start_date
        - location
        - price
        - created_at
        - updated_at
      properties:
        id:
          type: string
          format: uuid
          description: Identifiant unique de l'événement (UUID v4)
          readOnly: true
          example: "550e8400-e29b-41d4-a716-446655440000"
        title:
          type: string
          minLength: 3
          maxLength: 200
          description: Titre de l'événement
          example: "Jazz sous les étoiles"
        description:
          type: string
          maxLength: 2000
          description: Description détaillée de l'événement
          example: "Soirée jazz en plein air avec des artistes locaux"
        type:
          type: string
          enum: [concert, exposition, conference, festival, sport, autre]
          description: Type d'événement
          example: "concert"
        start_date:
          type: string
          format: date-time
          description: Date et heure de début au format ISO 8601
          example: "2025-07-15T20:00:00Z"
        end_date:
          type: string
          format: date-time
          description: Date et heure de fin au format ISO 8601 (optionnel)
          example: "2025-07-15T23:00:00Z"
        location:
          $ref: '#/components/schemas/Location'
        price:
          type: number
          format: float
          minimum: 0
          description: Prix d'entrée en euros (0 si gratuit)
          example: 15.00
        max_attendees:
          type: integer
          minimum: 1
          description: Nombre maximum de participants (null si illimité)
          nullable: true
          example: 200
        current_attendees:
          type: integer
          minimum: 0
          description: Nombre actuel d'inscrits
          readOnly: true
          example: 45
        image_url:
          type: string
          format: uri
          description: URL de l'image de l'événement
          example: "https://cdn.smartcity.local/events/jazz-stars.jpg"
        tags:
          type: array
          items:
            type: string
          description: Tags pour catégoriser l'événement
          example: ["musique", "jazz", "extérieur"]
        created_at:
          type: string
          format: date-time
          description: Date de création
          readOnly: true
          example: "2025-06-01T10:00:00Z"
        updated_at:
          type: string
          format: date-time
          description: Date de dernière modification
          readOnly: true
          example: "2025-06-15T14:30:00Z"

    EventInput:
      type: object
      required:
        - title
        - type
        - start_date
        - location
      properties:
        title:
          type: string
          minLength: 3
          maxLength: 200
          example: "Concert de Rock"
        description:
          type: string
          maxLength: 2000
          example: "Soirée rock avec groupes locaux"
        type:
          type: string
          enum: [concert, exposition, conference, festival, sport, autre]
          example: "concert"
        start_date:
          type: string
          format: date-time
          example: "2025-08-15T20:00:00Z"
        end_date:
          type: string
          format: date-time
          example: "2025-08-15T23:30:00Z"
        location:
          $ref: '#/components/schemas/LocationInput'
        price:
          type: number
          format: float
          minimum: 0
          default: 0
          example: 25.00
        max_attendees:
          type: integer
          minimum: 1
          example: 5000
        image_url:
          type: string
          format: uri
          example: "https://cdn.smartcity.local/events/rock-night.jpg"
        tags:
          type: array
          items:
            type: string
          example: ["musique", "rock"]

    EventUpdate:
      type: object
      description: Tous les champs sont optionnels pour une mise à jour partielle
      properties:
        title:
          type: string
          minLength: 3
          maxLength: 200
        description:
          type: string
          maxLength: 2000
        price:
          type: number
          format: float
          minimum: 0
        max_attendees:
          type: integer
          minimum: 1
        image_url:
          type: string
          format: uri
        tags:
          type: array
          items:
            type: string

    Location:
      type: object
      required:
        - name
        - city
      properties:
        name:
          type: string
          description: Nom du lieu
          example: "Place du Capitole"
        address:
          type: string
          description: Adresse complète
          example: "Place du Capitole, 31000 Toulouse"
        city:
          type: string
          description: Ville
          example: "Toulouse"
        postal_code:
          type: string
          pattern: '^\d{5}$'
          description: Code postal français (5 chiffres)
          example: "31000"
        coordinates:
          type: object
          description: Coordonnées GPS (optionnel)
          properties:
            latitude:
              type: number
              format: double
              minimum: -90
              maximum: 90
              example: 43.6047
            longitude:
              type: number
              format: double
              minimum: -180
              maximum: 180
              example: 1.4442

    LocationInput:
      type: object
      required:
        - name
        - city
      properties:
        name:
          type: string
          example: "Zenith de Toulouse"
        address:
          type: string
          example: "11 Avenue Raymond Badiou, 31300 Toulouse"
        city:
          type: string
          example: "Toulouse"
        postal_code:
          type: string
          pattern: '^\d{5}$'
          example: "31300"

    PaginationMeta:
      type: object
      required:
        - page
        - limit
        - total
        - total_pages
      properties:
        page:
          type: integer
          minimum: 1
          description: Page actuelle
          example: 1
        limit:
          type: integer
          minimum: 1
          description: Nombre d'éléments par page
          example: 20
        total:
          type: integer
          minimum: 0
          description: Nombre total d'éléments
          example: 45
        total_pages:
          type: integer
          minimum: 0
          description: Nombre total de pages
          example: 3

    PaginationLinks:
      type: object
      properties:
        self:
          type: string
          description: Lien vers la page actuelle
          example: "/events?page=1&limit=20"
        first:
          type: string
          description: Lien vers la première page
          example: "/events?page=1&limit=20"
        prev:
          type: string
          description: Lien vers la page précédente (null si première page)
          nullable: true
          example: null
        next:
          type: string
          description: Lien vers la page suivante (null si dernière page)
          nullable: true
          example: "/events?page=2&limit=20"
        last:
          type: string
          description: Lien vers la dernière page
          example: "/events?page=3&limit=20"

    ProblemDetails:
      type: object
      required:
        - type
        - title
        - status
      properties:
        type:
          type: string
          format: uri
          description: URI identifiant le type d'erreur
          example: "https://api.smartcity.local/errors/validation-error"
        title:
          type: string
          description: Titre court et lisible du problème
          example: "Validation Failed"
        status:
          type: integer
          description: Code de statut HTTP
          example: 422
        detail:
          type: string
          description: Explication détaillée du problème
          example: "One or more fields contain invalid data"
        instance:
          type: string
          description: URI de la requête qui a causé l'erreur
          example: "/events"
        correlation_id:
          type: string
          format: uuid
          description: ID unique pour tracer l'erreur dans les logs
          example: "123e4567-e89b-12d3-a456-426614174000"

  responses:
    BadRequest:
      description: Requête malformée ou paramètres invalides
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/ProblemDetails'
          example:
            type: "https://api.smartcity.local/errors/bad-request"
            title: "Bad Request"
            status: 400
            detail: "Invalid JSON in request body"
            instance: "/events"
            correlation_id: "123e4567-e89b-12d3-a456-426614174000"

    Unauthorized:
      description: Authentification requise ou token invalide
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/ProblemDetails'
          example:
            type: "https://api.smartcity.local/errors/unauthorized"
            title: "Unauthorized"
            status: 401
            detail: "Missing or invalid authentication token"
            instance: "/events"
            correlation_id: "223e4567-e89b-12d3-a456-426614174000"

    Forbidden:
      description: Accès refusé - permissions insuffisantes
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/ProblemDetails'
          example:
            type: "https://api.smartcity.local/errors/forbidden"
            title: "Forbidden"
            status: 403
            detail: "You do not have permission to perform this action"
            instance: "/events/123"
            correlation_id: "323e4567-e89b-12d3-a456-426614174000"

    NotFound:
      description: Ressource non trouvée
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/ProblemDetails'
          example:
            type: "https://api.smartcity.local/errors/not-found"
            title: "Not Found"
            status: 404
            detail: "The requested event does not exist"
            instance: "/events/999"
            correlation_id: "423e4567-e89b-12d3-a456-426614174000"

    ValidationError:
      description: Erreur de validation des données
      content:
        application/problem+json:
          schema:
            allOf:
              - $ref: '#/components/schemas/ProblemDetails'
              - type: object
                properties:
                  errors:
                    type: array
                    items:
                      type: object
                      properties:
                        field:
                          type: string
                        code:
                          type: string
                        message:
                          type: string
          example:
            type: "https://api.smartcity.local/errors/validation-error"
            title: "Validation Failed"
            status: 422
            detail: "One or more fields contain invalid data"
            instance: "/events"
            correlation_id: "523e4567-e89b-12d3-a456-426614174000"
            errors:
              - field: "title"
                code: "too_short"
                message: "Title must be at least 3 characters long"
              - field: "start_date"
                code: "invalid_format"
                message: "Date must be in ISO 8601 format"
              - field: "price"
                code: "negative_value"
                message: "Price cannot be negative"

    InternalError:
      description: Erreur interne du serveur
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/ProblemDetails'
          example:
            type: "https://api.smartcity.local/errors/internal-error"
            title: "Internal Server Error"
            status: 500
            detail: "An unexpected error occurred. Our team has been notified."
            instance: "/events"
            correlation_id: "623e4567-e89b-12d3-a456-426614174000"

tags:
  - name: Events
    description: Gestion des événements culturels et activités de la ville
```

### Éléments clés d'un bon fichier OpenAPI

**Section `info`** : Métadonnées complètes avec description, contact, licence. La description peut inclure du Markdown pour formater du texte riche.

**Section `servers`** : Liste les différents environnements. Le client peut choisir où envoyer ses requêtes.

**Section `paths`** : Le cœur de la spec. Chaque endpoint avec toutes ses méthodes HTTP, paramètres, corps de requêtes, réponses possibles.

**Section `components`** : Définitions réutilisables. Utilisez `$ref` pour éviter la duplication. Schémas, réponses d'erreur, mécanismes de sécurité.

**Section `tags`** : Permet de grouper les endpoints par thématique dans la documentation générée.

**Exemples multiples** : Utilisez `examples` (au pluriel) pour montrer différents cas d'usage. Très utile pour les développeurs.

## 2.3 Génération de documentation interactive

Le grand avantage d'OpenAPI : génération automatique d'une belle documentation interactive.

### Swagger UI

Swagger UI est l'outil le plus populaire. Il lit votre OpenAPI et génère une interface web où les développeurs peuvent :
- Voir tous les endpoints groupés par tags
- Voir les schémas de données avec exemples
- **Tester les endpoints directement depuis le navigateur**
- Voir les codes de réponse possibles
- Copier des exemples de requêtes

**Intégration Node.js/Express** :
```javascript
const swaggerUi = require('swagger-ui-express');
const YAML = require('yamljs');
const swaggerDocument = YAML.load('./openapi.yaml');

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument, {
  customCss: '.swagger-ui .topbar { display: none }',
  customSiteTitle: "SmartCity API Documentation"
}));
```

Allez sur `http://localhost:3000/api-docs` et vous avez votre documentation interactive.

### Redoc

Alternative plus moderne visuellement, optimisée pour la lecture plutôt que pour tester.
```javascript
const redoc = require('redoc-express');

app.get('/docs', redoc({
  title: 'SmartCity API
  Documentation',
  specUrl: '/openapi.yaml',
  nonce: '',
  redocOptions: {
    theme: {
      colors: {
        primary: { main: '#2563eb' }
      }
    }
  }
}));

Comparaison des outils
Swagger UI :

✅ Le plus populaire et mature
✅ Excellent pour tester les endpoints
✅ Try-it-out intégré
❌ Design un peu daté

Redoc :

✅ Très beau design moderne
✅ Optimisé pour la lecture
✅ Navigation fluide
❌ Moins bon pour tester interactivement

Stoplight Elements :

✅ Très moderne et personnalisable
✅ Composants web réutilisables
❌ Moins mature que les deux autres

Pour SmartCity, commencez avec Swagger UI. C'est le standard de l'industrie.
2.4 Bonnes pratiques de documentation
1. Design-first vs Code-first
Design-first : Vous écrivez d'abord le contrat OpenAPI, validez-le avec l'équipe, puis implémentez le code.

Avantage : Force à bien penser l'API avant de coder
Inconvénient : Risque de désynchronisation doc/code

Code-first : Vous codez d'abord, puis générez l'OpenAPI depuis le code (avec annotations/décorateurs).

Avantage : Doc toujours synchronisée
Inconvénient : Moins de réflexion sur le design

Pour des APIs publiques, design-first est meilleur. Pour des APIs internes, code-first peut suffire.
2. Exemples concrets et multiples
Chaque schéma devrait avoir au moins un exemple. Les endpoints complexes devraient avoir plusieurs exemples montrant différents cas d'usage.
yaml❌ Mauvais (juste un schéma abstrait):
schema:
  type: object
  properties:
    title: string
    price: number

✅ Bon (schéma + exemples multiples):
schema:
  $ref: '#/components/schemas/EventInput'
examples:
  concert:
    summary: Création d'un concert payant
    value:
      title: "Concert de Rock"
      type: "concert"
      price: 25.00
  exposition:
    summary: Exposition gratuite
    value:
      title: "Photos du Monde"
      type: "exposition"
      price: 0
3. Descriptions riches en Markdown
OpenAPI supporte le Markdown dans les descriptions. Utilisez-le !
yamldescription: |
  ## Authentification requise
  
  Cet endpoint nécessite un JWT token valide.
  
  ## Permissions
  
  - **user** : Peut créer des événements
  - **admin** : Peut créer et supprimer
  
  ## Limites
  
  - Maximum 100 événements par jour par utilisateur
  - Titre entre 3 et 200 caractères
4. Documentez TOUS les codes de réponse
Ne documentez pas seulement le 200 Success. Documentez les erreurs aussi !
yamlresponses:
  '200':
    description: Succès
  '400':
    description: JSON invalide ou paramètres manquants
  '401':
    description: Token manquant ou invalide
  '403':
    description: Permissions insuffisantes (rôle user au lieu d'admin)
  '404':
    description: Événement non trouvé
  '422':
    description: Validation échouée (titre trop court, date passée...)
  '429':
    description: Trop de requêtes (rate limit dépassé)
  '500':
    description: Erreur serveur
5. Réutilisez avec $ref
Au lieu de répéter les mêmes schémas partout :
yaml# ❌ Mauvais - répétition
paths:
  /events/{id}:
    get:
      responses:
        '404':
          content:
            application/problem+json:
              schema:
                type: object
                properties:
                  type: string
                  title: string
                  status: integer

# ✅ Bon - réutilisation
paths:
  /events/{id}:
    get:
      responses:
        '404':
          $ref: '#/components/responses/NotFound'
```

### 6. Versionnez la documentation avec le code

Votre fichier OpenAPI doit être versionné dans Git avec votre code. À chaque commit qui change l'API, le fichier OpenAPI change aussi.
```
git log openapi.yaml
commit abc123 - feat: add max_attendees field to events
commit def456 - fix: correct validation rules for postal_code
commit ghi789 - feat: add DELETE /events/{id} endpoint
7. Changelog visible
Maintenez un changelog pour que les développeurs voient ce qui a changé.
markdown## Changelog

### v1.2.0 (2025-11-15)
**New features:**
- Nouveau champ `tags` sur les événements
- Support filtrage par tag: `?tags=musique,jazz`
- Nouveau endpoint `GET /events/popular`

**Bug fixes:**
- Correction validation code postal (accepte maintenant 5 chiffres)

### v1.1.0 (2025-09-01)
**New features:**
- Ajout champ `image_url` optionnel
- Support tri par prix: `?sort=price` ou `?sort=-price`
2.5 Validation automatique
Un avantage énorme : validation automatique des requêtes/réponses contre la spec.
Validation côté serveur
Des bibliothèques comme express-openapi-validator valident automatiquement toutes les requêtes entrantes.
javascriptconst OpenApiValidator = require('express-openapi-validator');

app.use(
  OpenApiValidator.middleware({
    apiSpec: './openapi.yaml',
    validateRequests: true,     // Valide les requêtes entrantes
    validateResponses: true,     // Valide les réponses sortantes (dev uniquement)
    validateSecurity: true,      // Valide l'authentification
  })
);

// Si une requête ne match pas la spec, erreur 400 automatique
// Si une réponse ne match pas la spec, erreur 500 + log détaillé
Vous n'avez plus besoin d'écrire manuellement la validation de chaque champ !
Tests de contrat
Testez que votre implémentation respecte le contrat OpenAPI.
javascriptconst request = require('supertest');
const validator = require('swagger-parser');

describe('Contract Tests', () => {
  it('should validate OpenAPI spec', async () => {
    await validator.validate('./openapi.yaml');
  });
  
  it('GET /events should match spec', async () => {
    const res = await request(app).get('/api/v1/events');
    expect(res.status).toBe(200);
    expect(res.body).toHaveProperty('data');
    expect(res.body).toHaveProperty('meta');
    expect(Array.isArray(res.body.data)).toBe(true);
  });
});

### 🔗 PARTIE 3 : HATEOAS ET API HYPERMEDIA (45 min)

## 3.1 Qu'est-ce que HATEOAS ?

HATEOAS signifie "Hypermedia As The Engine Of Application State". C'est la contrainte REST la moins implémentée et la moins comprise.

L'idée : une API devrait être découvrable. Le client ne devrait pas avoir besoin de connaître toutes les URLs à l'avance. Le serveur envoie des liens vers les actions possibles, et le client suit ces liens.

C'est exactement comme le web fonctionne. Sur Wikipedia, vous ne connaissez pas toutes les URLs des articles. Vous cliquez sur des liens. Chaque page contient des liens vers d'autres pages pertinentes.

## 3.2 Exemple sans et avec HATEOAS

### Sans HATEOAS (API classique)
```json
{
  "id": 123,
  "status": "pending_payment",
  "total": 99.99
}
```

Le client doit connaître la logique : "Si status est pending_payment, je peux POST à /orders/123/payment". Cette logique est codée en dur côté client.

### Avec HATEOAS
```json
{
  "id": 123,
  "status": "pending_payment",
  "total": 99.99,
  "_links": {
    "self": {
      "href": "/orders/123",
      "method": "GET"
    },
    "payment": {
      "href": "/orders/123/payment",
      "method": "POST",
      "title": "Payer cette commande"
    },
    "cancel": {
      "href": "/orders/123",
      "method": "DELETE",
      "title": "Annuler cette commande"
    }
  }
}
```

Le serveur dit au client : "Voici les actions possibles". Si la commande était déjà payée :
```json
{
  "id": 123,
  "status": "paid",
  "total": 99.99,
  "_links": {
    "self": {
      "href": "/orders/123",
      "method": "GET"
    },
    "invoice": {
      "href": "/orders/123/invoice.pdf",
      "method": "GET",
      "title": "Télécharger la facture"
    },
    "track": {
      "href": "/orders/123/tracking",
      "method": "GET",
      "title": "Suivre la livraison"
    }
  }
}
```

Les liens changent selon l'état. Le client regarde quels liens sont présents et affiche les boutons correspondants. Pas besoin de connaître la logique métier.

## 3.3 Formats standards

### HAL (Hypertext Application Language)

Le format le plus simple et populaire pour HATEOAS.
```json
{
  "id": 123,
  "name": "Marie Dupont",
  "email": "marie@example.com",
  "_links": {
    "self": {
      "href": "/users/123"
    },
    "orders": {
      "href": "/users/123/orders"
    },
    "avatar": {
      "href": "/users/123/avatar.jpg"
    }
  },
  "_embedded": {
    "recent_orders": [
      {
        "id": 456,
        "total": 99.99,
        "_links": {
          "self": { "href": "/orders/456" }
        }
      }
    ]
  }
}
```

**`_links`** : Liens vers ressources liées  
**`_embedded`** : Ressources liées incluses directement (évite requêtes supplémentaires)

### JSON:API

Standard plus complet et strict.
```json
{
  "data": {
    "type": "users",
    "id": "123",
    "attributes": {
      "name": "Marie Dupont",
      "email": "marie@example.com"
    },
    "relationships": {
      "orders": {
        "links": {
          "self": "/users/123/relationships/orders",
          "related": "/users/123/orders"
        }
      }
    },
    "links": {
      "self": "/users/123"
    }
  }
}
```

JSON:API est très structuré avec conventions strictes. Excellent pour la cohérence, mais plus verbeux.

## 3.4 Avantages de HATEOAS

**Découvrabilité** : Un client peut explorer l'API en suivant les liens, comme on explore le web. Imaginons une API de e-commerce. Un client commence par `GET /`. La réponse contient des liens vers `/products`, `/categories`, `/cart`. En suivant ces liens, le client découvre toute l'API sans documentation préalable.

**Logique métier côté serveur** : Les règles métier ("peut-on annuler cette commande ?", "peut-on modifier cet événement ?") restent sur le serveur. Le serveur décide dynamiquement quels liens inclure selon l'état de la ressource et les permissions de l'utilisateur. Le client affiche simplement les actions disponibles.

**Évolutivité** : Si vous ajoutez une nouvelle action possible, vous ajoutez juste un nouveau lien. Les anciens clients qui ne connaissent pas ce lien l'ignorent. Les nouveaux clients qui le reconnaissent peuvent l'utiliser. Pas de breaking change.

**Documentation vivante** : La réponse elle-même documente ce qui est possible. Un développeur qui explore l'API voit immédiatement quelles actions sont disponibles sur chaque ressource.

**Exemple concret** : Imaginons un système de réservation d'événements.
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "title": "Concert de Jazz",
  "status": "open",
  "current_attendees": 45,
  "max_attendees": 200,
  "_links": {
    "self": "/events/550e8400-e29b-41d4-a716-446655440000",
    "register": {
      "href": "/events/550e8400-e29b-41d4-a716-446655440000/register",
      "method": "POST",
      "title": "S'inscrire à cet événement"
    },
    "attendees": "/events/550e8400-e29b-41d4-a716-446655440000/attendees"
  }
}
```

Si l'événement est complet :
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "title": "Concert de Jazz",
  "status": "full",
  "current_attendees": 200,
  "max_attendees": 200,
  "_links": {
    "self": "/events/550e8400-e29b-41d4-a716-446655440000",
    "attendees": "/events/550e8400-e29b-41d4-a716-446655440000/attendees"
  }
}
```

Le lien `register` a disparu. Le client sait immédiatement que l'inscription n'est plus possible sans avoir à connaître la logique "si current_attendees >= max_attendees alors pas d'inscription".

## 3.5 Inconvénients et limitations

**Complexité d'implémentation** : Générer les liens dynamiquement selon l'état de la ressource et les permissions de l'utilisateur est plus complexe que renvoyer juste les données. Vous devez écrire de la logique pour déterminer quels liens inclure dans chaque situation.

**Verbosité** : Les réponses sont plus grosses avec tous ces liens. Pour une API haute performance où chaque octet compte, c'est un coût. Une réponse qui faisait 500 octets peut facilement doubler de taille.

**Adoption faible** : Dans la réalité, très peu d'APIs publiques implémentent HATEOAS complètement. Même les APIs qui se disent "RESTful" ne le font généralement pas. GitHub, Twitter, Stripe... aucune n'implémente HATEOAS de manière stricte.

**Clients non préparés** : La plupart des clients (applications mobiles, SPAs) ne sont pas conçus pour suivre des liens dynamiquement. Ils ont leurs URLs codées en dur : `const API_URL = 'https://api.example.com/orders'`. Ils ne vont pas parser les liens de la réponse pour découvrir les actions possibles. Ils affichent des boutons selon leur propre logique.

**Overhead mental** : Pour les développeurs habitués aux APIs REST classiques, HATEOAS ajoute une couche de complexité conceptuelle. "Pourquoi je dois générer tous ces liens alors que le client connaît déjà les URLs ?"

**Pas de standard universel** : HAL, JSON:API, Siren... plusieurs standards existent. Choisir lequel utiliser et former l'équipe ajoute de la friction.

## 3.6 Approche pragmatique pour SmartCity

Pour votre projet, voici une approche progressive par niveaux :

**Niveau 0 : Pas de liens** (acceptable pour MVP)
```json
{
  "id": 123,
  "title": "Concert de Jazz",
  "status": "open"
}
```

Simple, direct. Aucune hypermedia. Le client connaît toutes les URLs. C'est ce que font 80% des APIs.

**Niveau 1 : Lien self uniquement** (recommandé minimum)
```json
{
  "id": 123,
  "title": "Concert de Jazz",
  "status": "open",
  "_links": {
    "self": "/events/123"
  }
}
```

Ajoute juste un lien vers la ressource elle-même. Très utile quand vous recevez une liste d'objets : chaque objet contient son URL complète. Le client n'a pas à construire l'URL lui-même.

**Niveau 2 : Liens vers ressources liées** (bon équilibre)
```json
{
  "id": 123,
  "title": "Concert de Jazz",
  "status": "open",
  "current_attendees": 45,
  "max_attendees": 200,
  "_links": {
    "self": "/events/123",
    "attendees": "/events/123/attendees",
    "creator": "/users/456",
    "register": {
      "href": "/events/123/register",
      "method": "POST",
      "title": "S'inscrire"
    }
  }
}
```

Inclut des liens vers les ressources liées et les actions possibles. Le client peut facilement naviguer vers les participants, le créateur de l'événement, ou s'inscrire. C'est un bon équilibre entre utilité et complexité.

**Niveau 3 : HAL ou JSON:API complet** (pour les puristes)

HAL complet avec `_embedded` :
```json
{
  "id": 123,
  "title": "Concert de Jazz",
  "status": "open",
  "_links": {
    "self": { "href": "/events/123" },
    "attendees": { "href": "/events/123/attendees" },
    "creator": { "href": "/users/456" }
  },
  "_embedded": {
    "creator": {
      "id": 456,
      "name": "Marie Dupont",
      "_links": {
        "self": { "href": "/users/456" }
      }
    }
  }
}
```

Très complet, mais aussi très verbeux. Réservé aux cas où vous avez vraiment besoin de ce niveau de sophistication.

**Recommandation pour SmartCity** : Visez le **niveau 2**. Assez de liens pour être utile et moderne, sans la complexité du HAL/JSON:API complet. C'est ce que font beaucoup d'APIs modernes bien conçues.

## 3.7 Implémentation simple

Voici comment ajouter des liens basiques dans votre API Node.js :
```javascript
// Helper pour générer les liens
function addLinks(event, baseUrl, user) {
  event._links = {
    self: `${baseUrl}/events/${event.id}`
  };
  
  // Toujours inclure le lien vers les participants
  event._links.attendees = `${baseUrl}/events/${event.id}/attendees`;
  
  // Lien vers le créateur
  if (event.creator_id) {
    event._links.creator = `${baseUrl}/users/${event.creator_id}`;
  }
  
  // Liens conditionnels selon l'état de l'événement
  if (event.status === 'open') {
    // Peut-on s'inscrire ? Vérifier qu'il reste de la place
    if (!event.max_attendees || event.current_attendees < event.max_attendees) {
      event._links.register = {
        href: `${baseUrl}/events/${event.id}/register`,
        method: 'POST',
        title: 'S\'inscrire à cet événement'
      };
    }
    
    // Peut-on modifier ? Vérifier les permissions
    if (user && (user.id === event.creator_id || user.role === 'admin')) {
      event._links.update = {
        href: `${baseUrl}/events/${event.id}`,
        method: 'PATCH',
        title: 'Modifier cet événement'
      };
    }
    
    // Peut-on supprimer ? Vérifier les permissions
    if (user && (user.id === event.creator_id || user.role === 'admin')) {
      event._links.delete = {
        href: `${baseUrl}/events/${event.id}`,
        method: 'DELETE',
        title: 'Supprimer cet événement'
      };
    }
  }
  
  return event;
}

// Utilisation dans vos endpoints
app.get('/api/v1/events/:id', authenticateOptional, async (req, res) => {
  const event = await prisma.event.findUnique({
    where: { id: req.params.id }
  });
  
  if (!event) {
    return res.status(404).json({
      type: 'https://api.smartcity.local/errors/not-found',
      title: 'Not Found',
      status: 404,
      detail: 'Event not found'
    });
  }
  
  const eventWithLinks = addLinks(
    event, 
    'https://api.smartcity.local/v1',
    req.user // peut être null si pas authentifié
  );
  
  res.json(eventWithLinks);
});

// Pour une collection
app.get('/api/v1/events', authenticateOptional, async (req, res) => {
  const events = await prisma.event.findMany({
    take: 20,
    skip: (req.query.page - 1) * 20
  });
  
  const eventsWithLinks = events.map(event => 
    addLinks(event, 'https://api.smartcity.local/v1', req.user)
  );
  
  res.json({
    data: eventsWithLinks,
    meta: {
      page: parseInt(req.query.page) || 1,
      limit: 20,
      total: await prisma.event.count()
    },
    _links: {
      self: `/events?page=${req.query.page || 1}`,
      next: `/events?page=${(parseInt(req.query.page) || 1) + 1}`
    }
  });
});
```

**Version Python avec FastAPI** :
```python
from typing import Optional
from pydantic import BaseModel

class EventLinks(BaseModel):
    self: str
    attendees: str
    creator: Optional[str] = None
    register: Optional[dict] = None
    update: Optional[dict] = None
    delete: Optional[dict] = None

class EventWithLinks(BaseModel):
    id: str
    title: str
    status: str
    current_attendees: int
    max_attendees: Optional[int]
    links: EventLinks = Field(alias="_links")

def add_links(event: Event, base_url: str, user: Optional[User] = None) -> EventWithLinks:
    links = {
        "self": f"{base_url}/events/{event.id}",
        "attendees": f"{base_url}/events/{event.id}/attendees"
    }
    
    if event.creator_id:
        links["creator"] = f"{base_url}/users/{event.creator_id}"
    
    if event.status == "open":
        if not event.max_attendees or event.current_attendees < event.max_attendees:
            links["register"] = {
                "href": f"{base_url}/events/{event.id}/register",
                "method": "POST",
                "title": "S'inscrire"
            }
        
        if user and (user.id == event.creator_id or user.role == "admin"):
            links["update"] = {
                "href": f"{base_url}/events/{event.id}",
                "method": "PATCH",
                "title": "Modifier"
            }
            links["delete"] = {
                "href": f"{base_url}/events/{event.id}",
                "method": "DELETE",
                "title": "Supprimer"
            }
    
    return EventWithLinks(**event.dict(), _links=links)

@app.get("/api/v1/events/{event_id}")
async def get_event(
    event_id: str,
    current_user: Optional[User] = Depends(get_current_user_optional)
):
    event = await db.events.find_one({"id": event_id})
    if not event:
        raise HTTPException(status_code=404, detail="Event not found")
    
    return add_links(event, "https://api.smartcity.local/v1", current_user)
```

## 3.8 Cas d'usage réels

**Cas 1 : Interface adaptative**

Imaginons une application front-end qui affiche un événement. Au lieu de coder en dur :
```javascript
// ❌ Logique en dur côté client
if (event.status === 'open' && event.current_attendees < event.max_attendees) {
  showButton('S\'inscrire');
}
if (user.id === event.creator_id || user.role === 'admin') {
  showButton('Modifier');
  showButton('Supprimer');
}
```

Avec HATEOAS :
```javascript
// ✅ Logique côté serveur
if (event._links.register) {
  showButton('S\'inscrire', event._links.register.href);
}
if (event._links.update) {
  showButton('Modifier', event._links.update.href);
}
if (event._links.delete) {
  showButton('Supprimer', event._links.delete.href);
}
```

Le client affiche juste les boutons pour les liens présents. La logique métier reste sur le serveur.

**Cas 2 : Évolution sans breaking change**

Vous ajoutez une nouvelle fonctionnalité : dupliquer un événement.

Sans HATEOAS : Vous devez mettre à jour tous les clients pour qu'ils sachent que `POST /events/{id}/duplicate` existe maintenant.

Avec HATEOAS : Vous ajoutez juste le lien dans la réponse :
```json
{
  "id": 123,
  "title": "Concert",
  "_links": {
    "self": "/events/123",
    "duplicate": {
      "href": "/events/123/duplicate",
      "method": "POST",
      "title": "Dupliquer cet événement"
    }
  }
}
```

Les anciens clients ignorent ce lien. Les nouveaux clients qui savent le gérer affichent un bouton "Dupliquer". Pas de breaking change.

**Cas 3 : Découverte d'API**

Un développeur teste votre API pour la première fois. Il fait `GET /` :
```json
{
  "message": "Bienvenue sur l'API SmartCity",
  "version": "1.0.0",
  "_links": {
    "events": "/events",
    "users": "/users",
    "auth": "/auth/login",
    "docs": "/api-docs"
  }
}
```

En suivant ces liens, il découvre progressivement toute l'API. C'est comme explorer Wikipedia en suivant les liens.

## 3.9 Quand ne PAS utiliser HATEOAS

**API interne entre microservices** : Si vos services se connaissent bien et communiquent en permanence, HATEOAS ajoute juste de la verbosité inutile.

**API haute performance** : Si chaque milliseconde et chaque octet comptent (gaming, trading haute fréquence), le overhead de HATEOAS n'en vaut pas la peine.

**Équipe pas formée** : Si votre équipe ne comprend pas HATEOAS et n'y voit pas de valeur, forcer son adoption créera plus de friction que de bénéfices.

**Clients simples** : Si vos clients sont de simples scripts qui font `GET /data` et c'est tout, pas besoin de sophistication.

## 3.10 Conclusion sur HATEOAS

HATEOAS est élégant en théorie. C'est la vision originale de REST par Roy Fielding. Mais dans la pratique, très peu d'APIs l'implémentent complètement car :
- La complexité d'implémentation n'en vaut pas toujours la peine
- Les clients modernes ne sont généralement pas conçus pour exploiter l'hypermedia
- Ça rend les réponses plus grosses

Pour SmartCity, une approche pragmatique : ajoutez au minimum un lien `self`, et si vous avez le temps, ajoutez des liens vers les ressources liées et les actions conditionnelles. Vous aurez une API moderne et bien conçue sans tomber dans la sur-ingénierie.

Le plus important : soyez cohérent. Si vous ajoutez des liens, faites-le partout de la même manière. Si vous ne les ajoutez pas, assumez ce choix. L'incohérence est pire que l'absence de liens.