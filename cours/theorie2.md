JOUR 2 - INTRODUCTION AUX API REST
Cours Théorique - Mastère 2 (Durée : 3h30)

🎯 OBJECTIFS PÉDAGOGIQUES
À la fin de cette matinée, vous serez capables de :

Comprendre les 6 contraintes architecturales REST et leur importance
Concevoir des APIs RESTful cohérentes et intuitives
Maîtriser les conventions de nommage et la structure des URLs
Gérer les relations entre ressources
Concevoir des réponses API structurées avec pagination, filtrage et tri
Implémenter une gestion d'erreurs professionnelle


📖 PARTIE 1 : PRINCIPES REST (50 min)
1.1 REST : Une philosophie, pas un protocole
Hier, nous avons vu que REST dominait le paysage des APIs modernes. Mais qu'est-ce que REST exactement ? Beaucoup de développeurs pensent que REST signifie simplement "utiliser HTTP avec du JSON". C'est réducteur. REST est une philosophie architecturale complète définie par Roy Fielding dans sa thèse de doctorat en 2000.
L'histoire de cette thèse est intéressante. Fielding était l'un des principaux auteurs de la spécification HTTP/1.1. Il observait comment le web évoluait et comment les développeurs utilisaient (et abusaient) du protocole HTTP. Il s'est demandé : quels sont les principes architecturaux qui font que le web fonctionne si bien à si grande échelle ? Sa réponse : REST (REpresentational State Transfer).
Le terme peut sembler abstrait, mais décomposons-le. Representational signifie que vous manipulez des représentations de ressources, pas les ressources elles-mêmes. Quand vous faites GET /users/123, vous ne récupérez pas l'utilisateur en tant qu'entité réelle, vous récupérez une représentation de cet utilisateur (probablement en JSON). State Transfer signifie que chaque requête transfère l'état nécessaire pour la traiter. Le serveur ne garde pas de mémoire des requêtes précédentes.
1.2 Les 6 contraintes architecturales REST
REST n'est pas une spécification rigide avec des règles à suivre à la lettre. Ce sont des contraintes architecturales, des principes directeurs. Une API peut être "plus ou moins RESTful" selon combien de ces contraintes elle respecte.
Contrainte 1 : Architecture Client-Serveur
Nous avons déjà vu ce principe hier. Le client et le serveur sont séparés, avec des responsabilités distinctes. Le client gère l'interface utilisateur et l'expérience utilisateur. Le serveur gère les données, la logique métier, et la persistance. Cette séparation permet aux deux de évoluer indépendamment.
Imaginons une banque qui a une application mobile pour ses clients. Le jour où elle décide de refaire complètement l'interface mobile avec un nouveau design, elle n'a pas besoin de toucher au serveur. Inversement, si elle migre sa base de données de MySQL à PostgreSQL, l'application mobile continue de fonctionner sans changement.
Contrainte 2 : Stateless (Sans état)
C'est probablement la contrainte la plus importante et la plus mal comprise. Stateless signifie que chaque requête du client vers le serveur doit contenir toutes les informations nécessaires pour comprendre et traiter cette requête. Le serveur ne stocke aucun contexte de session côté serveur entre deux requêtes.
Prenons un exemple concret. Imaginons un site de e-commerce traditionnel non-RESTful des années 2000. Vous vous connectez, le serveur crée une session et stocke votre ID utilisateur en mémoire avec un cookie de session. Vous naviguez, ajoutez des articles au panier (stocké en mémoire serveur), puis vous passez commande. Tout fonctionne tant que vous parlez toujours au même serveur. Mais si vous avez 10 serveurs derrière un load balancer et que votre deuxième requête arrive sur un serveur différent, celui-ci ne sait pas qui vous êtes. Solution classique : sticky sessions, où le load balancer vous colle toujours au même serveur. Mais ça complexifie la scalabilité.
Approche REST : chaque requête contient un token d'authentification (comme un JWT). Le serveur n'a aucune session en mémoire. Vous faites GET /cart avec votre token, le serveur décode le token, voit que vous êtes l'utilisateur 123, va chercher votre panier en base de données, et vous le renvoie. Peu importe quel serveur traite la requête, le résultat sera le même. Scaling horizontal trivial.
Cette contrainte a un coût : chaque requête est légèrement plus grosse (il faut envoyer le token). Mais les bénéfices en scalabilité et en simplicité sont énormes. C'est pour ça que toutes les grandes plateformes (Google, Facebook, Amazon) utilisent cette approche.
Contrainte 3 : Cacheable
Les réponses du serveur doivent explicitement se définir comme cacheable ou non-cacheable. Quand une réponse est cacheable, le client (ou un proxy intermédiaire comme un CDN) peut réutiliser cette réponse pour des requêtes futures identiques, sans re-contacter le serveur.
Imaginons un site de news. L'article que vous lisez maintenant ne va probablement pas changer dans les 5 prochaines minutes. Le serveur peut renvoyer cet article avec un header Cache-Control: max-age=300 (300 secondes = 5 minutes). Votre navigateur stocke cet article en cache local. Si vous actualisez la page dans les 5 minutes, le navigateur utilise sa copie locale sans faire de requête réseau. Résultat : page qui se charge instantanément, pas de charge sur le serveur.
Les codes de statut HTTP ont été conçus pour supporter le caching. Rappelez-vous le 304 Not Modified que nous avons vu hier : le client demande "j'ai cette ressource datée du 15 octobre, est-elle toujours valide ?", le serveur répond "oui" avec un 304 et aucun body. Économie massive de bande passante.
Contrainte 4 : Interface uniforme
C'est le cœur de REST. L'interface entre le client et le serveur doit être uniforme, standardisée. Cette contrainte se décompose en quatre sous-principes.
Identification des ressources : Chaque ressource est identifiée par une URI unique. /users/123 identifie l'utilisateur 123. /orders/456 identifie la commande 456. Cette URI est stable : elle ne change pas même si la représentation de la ressource change.
Manipulation via représentations : Quand un client détient une représentation d'une ressource avec suffisamment de métadonnées, il a assez d'informations pour modifier ou supprimer cette ressource. Si vous faites GET /users/123 et que vous récupérez l'utilisateur en JSON, vous avez tout ce qu'il faut pour faire ensuite PUT /users/123 avec les modifications.
Messages auto-descriptifs : Chaque message (requête ou réponse) contient assez d'informations pour décrire comment traiter ce message. Le header Content-Type: application/json dit au destinataire "ce body est du JSON, parse-le comme tel". Le code de statut dit "voilà ce qui s'est passé". Les headers Cache-Control disent "voilà comment cacher cette réponse".
HATEOAS (Hypermedia As The Engine Of Application State) : C'est le principe le plus avancé et le moins implémenté. L'idée est que les réponses contiennent des liens vers les actions possibles suivantes. Nous verrons ça en détail dans la partie 3.
Contrainte 5 : Système en couches
Le client ne peut pas savoir s'il est connecté directement au serveur final ou à un intermédiaire. Vous pourriez avoir une architecture comme : Client → CDN → Load Balancer → API Gateway → Microservice → Database. Du point de vue du client, il parle à une seule entité.
Cette contrainte permet d'ajouter des couches intermédiaires (caching, sécurité, load balancing) de manière transparente. Imaginons que votre API devient victime de son succès et reçoit trop de trafic. Vous pouvez ajouter un CDN Cloudflare devant sans changer une ligne de code côté client. Le client continue de faire ses requêtes, mais maintenant Cloudflare cache les réponses et absorbe 90% du trafic.
Contrainte 6 : Code on demand (optionnel)
C'est la seule contrainte optionnelle. Le serveur peut étendre les fonctionnalités du client en envoyant du code exécutable (JavaScript, applets Java à l'époque...). C'est exactement ce que font les applications web modernes : le serveur envoie du JavaScript que le navigateur exécute.
Cette contrainte est rarement mentionnée dans le contexte des APIs REST car elle est peu utilisée pour les APIs pures (qui renvoient des données, pas du code). Mais elle fait partie de la définition originale de REST.
1.3 Stateless vs Stateful : un débat pragmatique
Le principe stateless est magnifique en théorie, mais la réalité est plus nuancée. Certaines applications ont vraiment besoin d'état. Imaginons une application de vidéoconférence en temps réel. Vous avez une connexion WebSocket ouverte avec le serveur pendant toute la durée de l'appel. C'est intrinsèquement stateful. Le serveur maintient l'état de votre connexion, sait qui est dans la salle, qui parle, etc.
La solution pragmatique : être stateless pour les opérations CRUD standard (créer, lire, modifier, supprimer des ressources), et utiliser des mécanismes stateful (WebSockets, Server-Sent Events) quand c'est vraiment nécessaire pour des fonctionnalités temps réel. Vous pouvez même avoir une API REST stateless pour la gestion des utilisateurs et des salles, et des WebSockets stateful pour la communication temps réel pendant l'appel.
1.4 Ressources et URIs : penser en termes de "choses"
REST est centré sur les ressources. Une ressource est n'importe quelle information qui peut être nommée : un utilisateur, un document, une image, un service temporel (la météo actuelle), une collection d'autres ressources, etc.
La clé est de penser en termes de "choses" (noms) plutôt qu'en termes d'"actions" (verbes). Votre API expose des ressources que les clients peuvent manipuler avec les verbes HTTP standard.
Mauvais design (orienté actions) :
POST /createUser
POST /deleteUser?id=123
GET /getUserById?id=123
POST /updateUserEmail
Bon design (orienté ressources) :
POST /users          (créer)
DELETE /users/123    (supprimer)
GET /users/123       (lire)
PATCH /users/123     (modifier)
Dans le bon design, la ressource est /users et /users/123. Les actions sont exprimées par les verbes HTTP. C'est plus propre, plus cohérent, plus intuitif.

🎨 PARTIE 2 : DESIGN D'API RESTFUL (60 min)
2.1 Conventions de nommage des endpoints
Le nommage est crucial. Une bonne API doit être intuitive. Un développeur devrait pouvoir deviner les endpoints sans lire la documentation. Voici les conventions établies.
Utiliser des noms au pluriel
Toujours utiliser le pluriel pour les collections, même quand on manipule une seule ressource.
✅ GET /users/123
❌ GET /user/123

✅ GET /products
❌ GET /product
Pourquoi ? Cohérence. Si /users liste tous les utilisateurs et /users/123 récupère un utilisateur spécifique, c'est logique. Si vous mélangez /user/123 et /users, c'est confus.
Exception rare : les singletons. Si votre API a une ressource unique comme /profile (le profil de l'utilisateur connecté), vous pouvez utiliser le singulier car il n'y a jamais de collection.
Utiliser des noms, pas des verbes
✅ GET /users
❌ GET /getUsers

✅ POST /orders
❌ POST /createOrder

✅ DELETE /products/123
❌ DELETE /deleteProduct/123
Le verbe est déjà dans la méthode HTTP. Pas besoin de le répéter dans l'URL.
Exception : les actions qui ne correspondent pas à CRUD. Imaginons une action "réinitialiser le mot de passe". Ce n'est ni un create, ni un update au sens strict. Vous pouvez créer un endpoint POST /users/123/reset-password. Le verbe est accepté quand l'action ne peut pas être modélisée simplement comme une opération CRUD.
Utiliser kebab-case pour les URLs
✅ /users/123/order-history
❌ /users/123/orderHistory
❌ /users/123/order_history
Les URLs sont case-insensitive techniquement, mais par convention on utilise kebab-case (tirets) en minuscules. C'est plus lisible dans les URLs.
Hiérarchie et ressources imbriquées
Quand une ressource appartient clairement à une autre, utilisez l'imbrication.
GET /users/123/orders          # Les commandes de l'utilisateur 123
GET /orders/456/items          # Les articles de la commande 456
GET /posts/789/comments        # Les commentaires du post 789
Mais attention à ne pas trop imbriquer. Maximum 2-3 niveaux. Si vous vous retrouvez avec /users/123/orders/456/items/789/options/012, c'est ingérable. Dans ce cas, mettez les ressources au même niveau et utilisez des query parameters pour filtrer.
✅ GET /order-items/789?order_id=456
❌ GET /users/123/orders/456/items/789
2.2 Structure des URLs : clarté et cohérence
Pattern de base pour les collections
GET    /resources              # Liste toutes les ressources
POST   /resources              # Crée une nouvelle ressource
GET    /resources/{id}         # Récupère une ressource spécifique
PUT    /resources/{id}         # Remplace complètement la ressource
PATCH  /resources/{id}         # Modifie partiellement la ressource
DELETE /resources/{id}         # Supprime la ressource
C'est le pattern fondamental. Chaque collection dans votre API devrait suivre ce pattern.
Gestion des actions complexes
Parfois, vous avez besoin d'actions qui ne rentrent pas dans le moule CRUD. Plusieurs approches existent.
Approche 1 : Sous-ressource
Au lieu de voir une action comme un verbe, voyez-la comme une sous-ressource.
POST /orders/123/payment        # Payer une commande
POST /users/456/activation      # Activer un utilisateur
POST /articles/789/publication  # Publier un article
Ici, payment, activation, publication sont modélisés comme des sous-ressources. Vous créez un paiement, vous créez une activation, vous créez une publication.
Approche 2 : Verbe explicite (dernier recours)
Quand vraiment l'action ne peut pas être modélisée comme une ressource :
POST /users/123/send-reset-email
POST /cart/calculate-shipping
POST /reports/generate
C'est moins "pur" REST, mais c'est pragmatique. Mieux vaut une API claire avec quelques verbes qu'une API alambiquée qui force tout dans le moule CRUD.
Versioning dans l'URL
Nous verrons le versioning en détail demain (Jour 3), mais la convention la plus courante est de préfixer vos endpoints avec /api/v1/, /api/v2/, etc.
GET /api/v1/users/123
GET /api/v2/users/123
Quand vous introduisez des breaking changes, vous incrémentez la version majeure.
2.3 Relations entre ressources
Les ressources sont rarement isolées. Un utilisateur a des commandes. Une commande a des articles. Un article appartient à une catégorie. Comment modéliser ces relations ?
Relation one-to-many
Un utilisateur a plusieurs commandes. Deux approches principales.
Approche 1 : Endpoint imbriqué
GET /users/123/orders
Avantages : Clair, explicite. Inconvénients : Si un même ordre peut appartenir à plusieurs utilisateurs (ex: commande groupée), ça devient compliqué.
Approche 2 : Query parameter
GET /orders?user_id=123
Avantages : Flexible, permet des filtres multiples. Inconvénients : Moins explicite sur la relation.
Choisissez l'approche 1 pour les relations fortes et évidentes. Choisissez l'approche 2 pour plus de flexibilité.
Relation many-to-many
Un étudiant suit plusieurs cours. Un cours a plusieurs étudiants. Comment gérer ça ?
Option 1 : Sous-ressource dans les deux sens
GET /students/123/courses       # Les cours de l'étudiant 123
GET /courses/456/students       # Les étudiants du cours 456
Option 2 : Ressource intermédiaire
Si la relation elle-même a des attributs (ex: date d'inscription, note), créez une ressource dédiée.
GET /enrollments?student_id=123    # Toutes les inscriptions de l'étudiant 123
GET /enrollments?course_id=456     # Toutes les inscriptions au cours 456
POST /enrollments                  # Créer une nouvelle inscription
Un enrollment contient student_id, course_id, enrollment_date, grade, etc.
Inclusion de ressources liées
Parfois, le client veut la ressource principale ET les ressources liées en une seule requête. Utilisez un query parameter include ou expand.
GET /users/123?include=orders,profile
Réponse :
json{
  "id": 123,
  "name": "Marie Dubois",
  "orders": [
    {"id": 1, "total": 99.99},
    {"id": 2, "total": 149.99}
  ],
  "profile": {
    "bio": "...",
    "avatar": "..."
  }
}
C'est pratique pour réduire le nombre de requêtes, mais attention : ne récupérez que ce qui est nécessaire. Si vous incluez trop de données, les performances se dégradent.
2.4 Utilisation appropriée des méthodes HTTP
Nous avons vu les méthodes hier, mais insistons sur quelques cas d'usage spécifiques dans le contexte REST.
POST vs PUT : une source de confusion
Règle simple :

POST pour créer une ressource quand le serveur génère l'ID
PUT pour créer ou remplacer une ressource quand le client fournit l'ID

Cas classique : création d'utilisateur.
POST /users
Body: {"name": "Marie", "email": "marie@example.com"}

Réponse 201 Created:
Location: /users/550e8400-e29b-41d4-a716-446655440000
Body: {"id": "550e8400...", "name": "Marie", ...}
Le serveur a généré l'ID (UUID). On utilise POST.
Cas rare : création avec ID fourni par le client.
PUT /users/marie-dubois
Body: {"name": "Marie", "email": "marie@example.com"}
Le client choisit l'ID (ici marie-dubois comme username). On utilise PUT. Si la ressource existe déjà, elle est remplacée (idempotence de PUT).
PATCH : subtilités
PATCH modifie partiellement. Mais comment spécifier exactement quoi modifier ? Deux approches.
Approche 1 : Merge patch (simple)
Vous envoyez uniquement les champs à modifier.
PATCH /users/123
Body: {"email": "new-email@example.com"}
Le serveur merge ce JSON avec la ressource existante. Simple, mais limité : vous ne pouvez pas supprimer un champ (mettre à null), vous ne pouvez pas modifier des éléments dans un tableau précisément.
Approche 2 : JSON Patch (puissant)
Format standardisé (RFC 6902) qui permet des opérations précises.
PATCH /users/123
Content-Type: application/json-patch+json
Body:
[
  {"op": "replace", "path": "/email", "value": "new@example.com"},
  {"op": "add", "path": "/roles/-", "value": "admin"},
  {"op": "remove", "path": "/temporary_field"}
]
Opérations : add, remove, replace, move, copy, test. Très puissant, mais plus complexe.
Pour la plupart des APIs, le merge patch simple suffit. Utilisez JSON Patch pour des cas avancés.

📦 PARTIE 3 : CONCEPTION DE RÉPONSES API (50 min)
3.1 Structure des réponses JSON
Une réponse API bien structurée facilite la vie des développeurs. Établissons des conventions.
Réponse pour une ressource unique
json{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Marie Dubois",
  "email": "marie@example.com",
  "created_at": "2025-10-24T14:30:00Z",
  "updated_at": "2025-10-25T09:15:00Z"
}
Simple, direct. Le body est directement l'objet.
Réponse pour une collection
Ne renvoyez JAMAIS un tableau nu au top-level. Enveloppez toujours dans un objet.
json❌ Mauvais :
[
  {"id": 1, "name": "Product A"},
  {"id": 2, "name": "Product B"}
]

✅ Bon :
{
  "data": [
    {"id": 1, "name": "Product A"},
    {"id": 2, "name": "Product B"}
  ],
  "meta": {
    "total": 156,
    "page": 1,
    "per_page": 20
  }
}
Pourquoi ? Flexibilité future. Si demain vous voulez ajouter des métadonnées (pagination, statistiques, etc.), vous pouvez. Avec un tableau nu, vous cassez la compatibilité.
Métadonnées utiles
json{
  "data": [...],
  "meta": {
    "total": 156,          // Nombre total d'éléments
    "count": 20,           // Nombre d'éléments dans cette page
    "page": 1,             // Page actuelle
    "per_page": 20,        // Éléments par page
    "total_pages": 8       // Nombre total de pages
  },
  "links": {
    "self": "/products?page=1",
    "next": "/products?page=2",
    "last": "/products?page=8"
  }
}
Ces métadonnées permettent au client de construire une pagination UI facilement.
3.2 Pagination : gérer les grandes collections
Renvoyer 10,000 produits en une seule réponse, c'est inacceptable. Temps de réponse long, consommation mémoire excessive, mauvaise UX. La pagination est essentielle.
Pagination offset-based (classique)
GET /products?page=1&per_page=20
GET /products?offset=40&limit=20
Deux variantes. Première : numéro de page + taille de page. Deuxième : offset (nombre d'éléments à sauter) + limit (nombre à récupérer).
Avantages : Simple, permet de sauter à n'importe quelle page directement.
Inconvénients : Performance qui se dégrade sur les grandes tables (offset 10000 est lent en SQL), problèmes si des éléments sont ajoutés/supprimés pendant la pagination.
Imaginons que vous consultez la page 1 (éléments 1-20). Puis 100 nouveaux produits sont ajoutés en tête de liste. Quand vous demandez la page 2, vous obtenez des éléments que vous avez déjà vus sur la page 1. Problème de cohérence.
Pagination cursor-based (moderne)
GET /products?cursor=eyJpZCI6MTIzfQ==&limit=20
Le cursor est un pointeur opaque (généralement encodé en base64) qui identifie où vous en êtes. Le serveur renvoie le cursor du prochain batch.
json{
  "data": [...],
  "meta": {
    "next_cursor": "eyJpZCI6MTQzfQ==",
    "has_more": true
  }
}
Avantages : Performance constante quelle que soit la position, cohérence même si des données sont ajoutées/supprimées.
Inconvénients : Ne permet pas de sauter directement à la page 5, plus complexe à implémenter.
Utilisez offset-based pour des cas simples (backoffice admin, recherches ponctuelles). Utilisez cursor-based pour des feeds infinis (réseaux sociaux, messagerie, applications temps réel).
Limites raisonnables
Imposez des limites. Ne laissez pas le client demander ?limit=1000000.
pythonper_page = min(request.get('per_page', 20), 100)  # Max 100 par page
Si le client demande 1000, clamp à 100 et documentez cette limite.
3.3 Filtrage et tri
Les clients ont souvent besoin de filtrer et trier les collections.
Filtrage basique
Utilisez des query parameters.
GET /products?category=electronics
GET /products?min_price=100&max_price=500
GET /products?in_stock=true
GET /users?status=active&role=admin
Convention : le nom du query parameter correspond au nom du champ. Simple et intuitif.
Filtrage avancé
Pour des filtres complexes, utilisez une syntaxe structurée.
GET /products?filter[category]=electronics&filter[price][gte]=100&filter[price][lte]=500
gte = greater than or equal, lte = less than or equal. Cette syntaxe permet des filtres riches sans ambiguïté.
Recherche textuelle
GET /products?search=laptop
GET /users?q=marie
search ou q pour une recherche full-text. Le serveur cherche dans plusieurs champs (nom, description, tags...).
Tri
GET /products?sort=price            # Tri ascendant par prix
GET /products?sort=-price           # Tri descendant par prix (le - indique desc)
GET /products?sort=category,price   # Tri multiple : par catégorie puis prix
Convention : - préfixe pour descendant, , pour séparer plusieurs critères.
3.4 Gestion des erreurs : RFC 7807 Problem Details
Hier, nous avons vu les codes de statut HTTP. Mais le code seul ne suffit pas. Le client a besoin de détails : qu'est-ce qui a échoué exactement ? Comment corriger ?
Il existe un standard RFC 7807 "Problem Details for HTTP APIs" qui définit un format JSON pour les erreurs.
Format RFC 7807
json{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Failed",
  "status": 422,
  "detail": "The email address is already in use by another account.",
  "instance": "/users",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440000",
  "invalid_fields": [
    {
      "field": "email",
      "message": "Email already exists"
    }
  ]
}
Décomposons :

type : URI qui identifie le type d'erreur. Idéalement, cette URI pointe vers une documentation.
title : Titre human-readable du problème. Générique pour ce type d'erreur.
status : Le code de statut HTTP (répété pour commodité).
detail : Explication spécifique de cette instance du problème.
instance : URI de la requête qui a causé l'erreur.
correlation_id : ID unique pour tracer cette erreur dans vos logs (crucial pour le debugging).
Champs custom : Vous pouvez ajouter des champs supplémentaires comme invalid_fields.

Content-Type
Important : utilisez Content-Type: application/problem+json pour les erreurs RFC 7807, pas application/json. Ça permet au client de savoir qu'il reçoit une erreur structurée.
Exemples d'erreurs courantes
400 Bad Request - JSON invalide
json{
  "type": "https://api.example.com/errors/invalid-json",
  "title": "Invalid JSON",
  "status": 400,
  "detail": "Unexpected token < in JSON at position 0",
  "instance": "/users",
  "correlation_id": "123e4567-e89b-12d3-a456-426614174000"
}
401 Unauthorized - Token manquant
json{
  "type": "https://api.example.com/errors/unauthorized",
  "title": "Unauthorized",
  "status": 401,
  "detail": "No authentication token provided. Please include an Authorization header.",
  "instance": "/users/123",
  "correlation_id": "223e4567-e89b-12d3-a456-426614174000"
}
403 Forbidden - Permissions insuffisantes
json{
  "type": "https://api.example.com/errors/forbidden",
  "title": "Forbidden",
  "status": 403,
  "detail": "You do not have permission to delete this user. Admin role required.",
  "instance": "/users/123",
  "correlation_id": "323e4567-e89b-12d3-a456-426614174000",
  "required_role": "admin",
  "current_role": "user"
}
404 Not Found - Ressource inexistante
json{
  "type": "https://api.example.com/errors/not-found",
  "title": "Not Found",
  "status": 404,
  "detail": "User with ID 999 does not exist.",
  "instance": "/users/999",
  "correlation_id": "423e4567-e89b-12d3-a456-426614174000"
}
422 Unprocessable Entity - Validation métier
json{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Failed",
  "status": 422,
  "detail": "One or more fields failed validation.",
  "instance": "/users",
  "correlation_id": "523e4567-e89b-12d3-a456-426614174000",
  "errors": [
    {
      "field": "email",
      "code": "email_already_exists",
      "message": "This email address is already registered."
    },
    {
      "field": "password",
      "code": "password_too_weak",
      "message": "Password must contain at least 8 characters, including uppercase, lowercase, and numbers."
    }
  ]
}
500 Internal Server Error - Erreur serveur
json{
  "type": "https://api.example.com/errors/internal-error",
  "title": "Internal Server Error",
  "status": 500,
  "detail": "An unexpected error occurred while processing your request. Our team has been notified.",
  "instance": "/orders/123/payment",
  "correlation_id": "623e4567-e89b-12d3-a456-426614174000"
}
Note importante : pour les 500, ne révélez JAMAIS les détails techniques (stack traces, messages d'exception bruts). Ces informations vont dans les logs serveur, pas dans la réponse au client. C'est une faille de sécurité de révéler des détails d'implémentation.
Cohérence des erreurs
Toutes vos erreurs doivent suivre le même format. Si une endpoint renvoie une structure d'erreur et une autre endpoint une structure différente, c'est cauchemardesque pour le client qui doit gérer plusieurs formats.
Créez des fonctions helper réutilisables pour générer vos erreurs.
javascript// Node.js/Express exemple
function problemDetails(type, title, status, detail, instance, extra = {}) {
  return {
    type: `https://api.example.com/errors/${type}`,
    title,
    status,
    detail,
    instance,
    correlation_id: uuidv4(),
    ...extra
  };
}

// Utilisation
res.status(422)
   .type('application/problem+json')
   .json(problemDetails(
     'validation-error',
     'Validation Failed',
     422,
     'Email already exists',
     req.path,
     { errors: [{field: 'email', message: 'Email already exists'}] }
   ));

🎓 RÉCAPITULATIF ET POINTS CLÉS (15 min)
Ce qu'il faut absolument retenir
1. REST est une philosophie architecturale, pas un protocole
Roy Fielding a défini 6 contraintes architecturales. Respecter ces contraintes donne une API scalable, maintenable, et intuitive. Les contraintes les plus importantes : stateless (chaque requête est indépendante), cacheable (optimise les performances), et interface uniforme (cohérence).
2. Penser ressources, pas actions
Une bonne API REST expose des ressources (noms) manipulées par des verbes HTTP standards. /users est une ressource, GET/POST/PUT/PATCH/DELETE sont les verbes. Évitez /createUser, /deleteUser, etc.
3. Conventions de nommage cohérentes

Pluriel pour les collections : /users, pas /user
Noms, pas verbes : /products, pas /getProducts
Kebab-case en minuscules : /order-history
Maximum 2-3 niveaux d'imbrication : /users/123/orders, pas /users/123/orders/456/items/789/details

4. Les méthodes HTTP ont un sens précis

GET : récupérer (safe, idempotent, cacheable)
POST : créer quand le serveur génère l'ID
PUT : remplacer entièrement (idempotent)
PATCH : modifier partiellement
DELETE : supprimer (idempotent)

5. Structure de réponse cohérente
Enveloppez les collections dans un objet avec métadonnées. Ne renvoyez jamais un tableau nu au top-level. Incluez pagination, filtrage, tri dès la conception.
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
6. Pagination obligatoire pour les grandes collections
Offset-based pour la simplicité (?page=1&per_page=20), cursor-based pour la performance et cohérence (?cursor=abc&limit=20). Imposez toujours une limite maximale.
7. Gestion d'erreurs selon RFC 7807
Format structuré avec type, title, status, detail, instance, correlation_id. Content-Type: application/problem+json. Cohérence absolue entre tous les endpoints.
8. Relations entre ressources
Pour one-to-many : endpoint imbriqué (/users/123/orders) ou query parameter (/orders?user_id=123). Pour many-to-many : ressource intermédiaire si la relation a des attributs (/enrollments).
Préparation pour cet après-midi
Cet après-midi, vous allez implémenter vos premiers endpoints avec des données en dur (mock data). Concrètement :
Implémenter 2-3 endpoints fonctionnels avec des données stockées dans des tableaux en mémoire JavaScript/Python/Java. Pas de base de données aujourd'hui. Focus sur la logique et la structure des réponses.
Générer des mocks avec Prism : Prism est un outil qui lit votre fichier OpenAPI et génère automatiquement un serveur mock. Vous pourrez tester vos endpoints avant même d'écrire le code. L'équipe front pourra commencer à développer contre ces mocks.
Middleware de gestion d'erreurs : Implémenter un middleware centralisé qui catch toutes les erreurs et les formatte selon RFC 7807. Une seule fonction pour toutes les erreurs de votre API.
Premiers tests unitaires : Écrire 3-5 tests basiques pour vos endpoints. Tester les cas de succès et les cas d'erreur principaux (404, 422, etc.).
Conseils pratiques
Commencez vraiment simple : Un tableau JavaScript/Python avec 3-4 objets suffit. L'objectif est de valider la structure, pas de gérer des milliers de données.
javascript// Exemple Node.js
let users = [
  { id: '1', name: 'Alice Dupont', email: 'alice@example.com' },
  { id: '2', name: 'Bob Martin', email: 'bob@example.com' },
  { id: '3', name: 'Charlie Durand', email: 'charlie@example.com' }
];

app.get('/api/v1/users', (req, res) => {
  res.json({
    data: users,
    meta: { total: users.length, page: 1, per_page: 20 }
  });
});

app.get('/api/v1/users/:id', (req, res) => {
  const user = users.find(u => u.id === req.params.id);
  if (!user) {
    return res.status(404).type('application/problem+json').json({
      type: 'https://api.smartcity.local/errors/not-found',
      title: 'Not Found',
      status: 404,
      detail: `User with ID ${req.params.id} does not exist.`,
      instance: req.path,
      correlation_id: uuidv4()
    });
  }
  res.json(user);
});
Utilisez Postman pour tester : Créez une collection avec vos requêtes. Testez GET, POST, cas d'erreur. Exportez la collection pour que toute l'équipe puisse l'utiliser.
Équipe front : ne bloquez pas : Même si les backends ne sont pas prêts, utilisez les mocks générés par Prism pour commencer à développer. Vous aurez des données de test immédiatement.
Équipe auth : concentrez-vous sur la structure : Aujourd'hui, implémentez juste POST /auth/register et POST /auth/login avec validation basique. Pas de JWT aujourd'hui, on verra ça Jour 4. Juste valider que les données sont correctes et renvoyer une réponse structurée.
Questions fréquentes anticipées
"On doit implémenter la pagination dès aujourd'hui ?"
Implémentez la structure de réponse avec data et meta, mais la pagination réelle peut attendre. Avec 3 objets en mémoire, pas besoin de paginer. Préparez juste la structure.
"Comment gérer les IDs sans base de données ?"
Utilisez des IDs séquentiels simples ('1', '2', '3') ou générez des UUIDs. Peu importe aujourd'hui, vous changerez ça demain quand vous connecterez la vraie BDD.
"Faut-il implémenter tous les filtres et tris ?"
Non. Implémentez peut-être un ou deux filtres basiques pour montrer le concept. Vous étofferez progressivement.
"Prism, c'est obligatoire ?"
Fortement recommandé, surtout pour l'équipe front. Ça permet d'avoir des mocks instantanés. Mais si vous préférez coder directement, c'est OK. Prism c'est un gain de temps, pas une obligation.
"Les tests, on teste quoi exactement ?"
Testez que GET /users renvoie 200 avec le bon format. Testez que GET /users/999 renvoie 404. Testez que POST /users avec des données invalides renvoie 422. Basique mais essentiel.

📚 RESSOURCES COMPLÉMENTAIRES
Documentation et standards
RFC et spécifications :

RFC 7807 : Problem Details for HTTP APIs
OpenAPI Specification 3.0 : https://swagger.io/specification/

JSON:API : https://jsonapi.org/ (alternative à REST avec conventions très strictes)

Guides de style d'APIs :

Google API Design Guide : https://cloud.google.com/apis/design
Microsoft REST API Guidelines : https://github.com/microsoft/api-guidelines
Zalando RESTful API Guidelines : https://opensource.zalando.com/restful-api-guidelines/

Ces guides montrent comment les grandes entreprises conçoivent leurs APIs. Excellentes sources d'inspiration.
Outils pour aujourd'hui
Prism : Mock server à partir d'OpenAPI
Installation : npm install -g @stoplight/prism-cli
Usage : prism mock openapi.yaml
Postman : Tester et documenter vos APIs
Téléchargement : https://www.postman.com/downloads/
Jest / Pytest / JUnit : Frameworks de tests selon votre stack
Commencez avec des tests simples sur vos endpoints.
Nodemon / Flask auto-reload : Redémarrage auto du serveur
Gagne du temps pendant le développement.
Lectures recommandées
Articles :

"RESTful API Design: 13 Best Practices" sur Stack Overflow Blog
"REST API Error Handling - Problem Details Response" sur medium.com
"Pagination in APIs" sur Slack Engineering Blog

Livres :

"REST API Design Rulebook" par Mark Massé
"Web API Design: The Missing Link" par Brian Mulloy (gratuit en PDF)

Exercices optionnels pour ce soir
1. Explorez des APIs publiques bien conçues
Testez dans Postman :

GitHub API : https://api.github.com/users/github/repos
Stripe API : https://stripe.com/docs/api (nécessite clé)
JSONPlaceholder : https://jsonplaceholder.typicode.com/posts

Analysez leur structure de réponse, leur gestion d'erreurs, leur pagination. Inspirez-vous.
2. Créez une mini-collection Postman
Créez 5-6 requêtes pour votre domaine métier :

GET collection
GET ressource spécifique
POST créer
PATCH modifier
DELETE supprimer
Cas d'erreur (404, 422)

3. Lisez la doc de Prism
https://stoplight.io/open-source/prism
Regardez comment générer des mocks, comment Prism valide les requêtes contre votre OpenAPI.
4. Préparez votre structure de tests
Créez le dossier et le fichier de config :
tests/
  api/
    users.test.js  (ou .py, .java selon votre stack)
Un test simple pour commencer :
javascriptdescribe('GET /api/v1/users', () => {
  it('should return 200 and list of users', async () => {
    const res = await request(app).get('/api/v1/users');
    expect(res.status).toBe(200);
    expect(res.body).toHaveProperty('data');
    expect(Array.isArray(res.body.data)).toBe(true);
  });
});