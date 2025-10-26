Pause déjeuner : 12h30 - 13h30
Reprise pour le projet fil rouge : 13h30# 📚 JOUR 1 - FONDAMENTAUX DES WEB SERVICES

Cours Théorique - Mastère 2 (Durée : 3h30)
🎯 OBJECTIFS PÉDAGOGIQUES
À la fin de cette matinée, vous serez capables de :

Définir ce qu'est un web service et comprendre son rôle dans l'architecture moderne
Maîtriser le modèle client-serveur et le protocole HTTP
Utiliser correctement les méthodes HTTP (GET, POST, PUT, PATCH, DELETE)
Interpréter les codes de statut HTTP et gérer les erreurs
Travailler avec JSON et XML pour échanger des données
📖 PARTIE 1 : INTRODUCTION AUX WEB SERVICES (45 min)
1.1 Qu'est-ce qu'un Web Service ?
Imaginons une banque nationale en 1995. À Paris, le système informatique tourne en COBOL sur un mainframe IBM. À Lyon, les développeurs ont choisi du C++ sur des serveurs Unix. À Marseille, les agences utilisent du Visual Basic sur Windows. Trois systèmes, trois langages, trois architectures complètement différentes. Un jour, le directeur général annonce lors d'une réunion : "Nous devons permettre à nos clients de consulter leur compte depuis n'importe quelle agence, pas seulement celle où ils ont ouvert leur compte."

L'équipe technique se retrouve face à un casse-tête monumental. Comment faire communiquer ces trois systèmes qui ne parlent littéralement pas le même langage ? Comment s'assurer qu'une requête envoyée depuis Paris peut être comprise par le système de Lyon ? Et surtout, comment faire ça sans tout réécrire depuis zéro ?

C'est précisément ce type de problème que les web services sont venus résoudre. Un web service est un système logiciel qui permet à deux applications de communiquer entre elles via un réseau, généralement Internet, en utilisant des standards ouverts que tout le monde comprend. C'est un peu comme un interprète universel qui traduit entre différentes langues informatiques.

Prenons un exemple très concret du quotidien. Imaginons que vous développez une application météo pour smartphone. Votre application ne dispose d'aucun capteur météorologique, elle ne sait pas mesurer la température, l'humidité ou la pression atmosphérique. Pourtant, quand un utilisateur ouvre l'application, celle-ci affiche instantanément "25°C à Paris, risque de pluie à 15h". Comment est-ce possible ?

L'application envoie simplement une requête à un web service de météo : "Donne-moi les prévisions météorologiques pour Paris". Le service météo, qui lui agrège les données de milliers de stations météorologiques à travers le monde, répond avec des données structurées. L'application les reçoit, les met en forme joliment, et les affiche à l'utilisateur. Tout ce processus prend quelques centaines de millisecondes et reste complètement transparent pour l'utilisateur final.

Cette architecture repose sur trois composants fondamentaux. Le provider (fournisseur) est celui qui expose des fonctionnalités ou des données. Dans notre exemple météo, c'est le service qui collecte et traite les données météorologiques. Le consumer (consommateur) est l'application qui utilise ces fonctionnalités. Ici, c'est l'application mobile qui affiche la météo. Et entre les deux, il y a le contract (contrat), qui définit les règles du jeu : comment formuler une demande, quel format utiliser, quelles données peuvent être demandées, comment interpréter la réponse.

Pour comprendre cette relation, imaginons un restaurant. Vous êtes le client (consumer), le serveur est le provider, et le menu est le contrat. Le menu vous dit ce qui est disponible et comment le commander. Vous ne rentrez pas en cuisine pour préparer vous-même votre plat. Vous ne connaissez pas les recettes. Vous ne savez même pas où sont stockés les ingrédients. Tout ce que vous avez à faire, c'est passer une commande claire selon le format du menu, et faire confiance au restaurant pour livrer ce que vous avez demandé.

1.2 L'évolution : de la complexité à la simplicité
L'histoire des web services est celle d'un aller-retour fascinant entre la complexité et la simplicité. Dans les années 90, quand les premières tentatives de faire communiquer des applications distantes ont émergé, les ingénieurs ont créé des technologies comme CORBA (Common Object Request Broker Architecture) ou DCOM (Distributed Component Object Model). Ces technologies promettaient de résoudre tous les problèmes d'interopérabilité, mais elles étaient d'une complexité redoutable. Très peu d'entreprises arrivaient à les déployer correctement, et celles qui y parvenaient devaient maintenir des équipes d'experts spécialisés.

Au début des années 2000, une nouvelle approche a émergé : SOAP (Simple Object Access Protocol). L'ironie du nom "Simple" n'a échappé à personne par la suite. L'idée de départ était pourtant bonne : utiliser XML, un format texte structuré et lisible par l'homme, pour échanger des messages entre applications. Microsoft et IBM sont devenus les grands champions de SOAP. Ils ont construit tout un écosystème : WSDL pour décrire formellement les services, UDDI pour les découvrir et les référencer, WS-Security pour les sécuriser, et une dizaine d'autres standards aux acronymes complexes.

Imaginons une entreprise de e-commerce en 2007 qui souhaite intégrer un système de paiement par carte bancaire via SOAP. L'équipe technique reçoit un fichier WSDL de 3000 lignes. Pour effectuer un simple paiement de 50 euros, il faut construire une enveloppe SOAP avec des namespaces XML imbriqués, des headers de sécurité avec signatures numériques, des timestamps... Le moindre espace mal placé dans le XML fait échouer toute la communication, avec des messages d'erreur absolument cryptiques. L'intégration qui devait prendre une semaine se transforme en projet de trois mois.

Pendant ce temps, en 2000, Roy Fielding publiait sa thèse de doctorat introduisant REST (Representational State Transfer). Mais dans le tumulte de l'engouement pour SOAP, personne n'y prêtait vraiment attention. Les grands acteurs de l'industrie étaient occupés à promouvoir SOAP comme LA solution d'entreprise. REST semblait trop simple, trop basique. Comment quelque chose d'aussi minimaliste pourrait-il fonctionner pour de "vraies" applications d'entreprise ?

Puis, vers 2005, quelque chose d'intéressant s'est produit. Les startups du Web 2.0 – Flickr pour le partage de photos, del.icio.us pour les marque-pages sociaux, puis Twitter et Facebook – ont commencé à ouvrir leurs plateformes aux développeurs externes. Elles avaient besoin de créer des APIs simples, rapides à mettre en œuvre, et faciles à utiliser. Elles ont choisi REST, pas SOAP. Pourquoi ? Parce qu'un développeur pouvait comprendre comment utiliser une API REST en lisant la documentation pendant dix minutes. Avec SOAP, il fallait plusieurs jours juste pour configurer l'environnement de développement.

L'adoption a été fulgurante. En 2006, Amazon a publié des statistiques montrant que 85% du trafic vers son API de e-commerce utilisait REST plutôt que SOAP. En 2010, Twitter gérait déjà 65 millions de tweets par jour, tous transitant via une API REST élégante et simple. Aujourd'hui, si on analyse les APIs des 1000 plus grandes entreprises tech, environ 80% sont en REST.

Mais l'histoire ne s'arrête pas là. Vers 2015, Facebook a commencé à parler de GraphQL, un nouveau paradigme qu'ils avaient développé en interne depuis 2012. Le problème que GraphQL résolvait était bien réel. Imaginons une application mobile affichant un profil utilisateur. Avec REST, pour récupérer l'utilisateur, ses 10 derniers posts, et les commentaires de chaque post, il faut faire trois requêtes séparées : une pour l'utilisateur, une pour les posts, une pour les commentaires. GraphQL permet de tout demander en une seule requête, en spécifiant exactement les champs nécessaires, rien de plus, rien de moins.

De son côté, Google poussait gRPC, un système de RPC moderne basé sur HTTP/2 et Protocol Buffers. Imaginons une plateforme de streaming vidéo comme YouTube qui doit gérer des millions de requêtes par seconde entre ses microservices internes. gRPC, avec son format binaire compact et sa communication bidirectionnelle, s'avère jusqu'à 10 fois plus rapide que REST pour ce type d'usage.

Alors, qui a gagné cette bataille des standards ? La réponse pragmatique : tout le monde a trouvé sa place. SOAP existe encore dans les banques et les grandes entreprises qui ont investi des millions dans des systèmes legacy. REST domine largement les APIs publiques pour sa simplicité. GraphQL a trouvé sa niche dans les applications complexes avec des interfaces riches. Et gRPC est devenu le standard de facto pour les communications internes entre microservices où la performance est critique.

1.3 Pourquoi utiliser des Web Services ?
L'histoire de Stripe illustre parfaitement la puissance des web services bien conçus. En 2010, les frères Patrick et John Collison, irlandais installés en Californie, constatent la complexité d'accepter des paiements en ligne. Ils fondent Stripe avec une promesse révolutionnaire : intégrer les paiements avec 7 lignes de code. Pas d'installation lourde, pas de configuration labyrinthique, juste quelques appels à une API REST élégante. En 2025, Stripe traite des centaines de milliards de dollars par an et est valorisée à plus de 50 milliards de dollars.

Premier avantage : l'interopérabilité universelle

Imaginons une startup qui développe une plateforme de réservation de restaurants. L'équipe souhaite être présente partout : application iPhone, application Android, site web desktop, site web mobile, voire même des bornes tactiles dans les restaurants. Sans web services, il faudrait développer la logique métier cinq fois : une fois en Swift pour iOS, une fois en Kotlin pour Android, une fois en JavaScript pour le web, une fois en Python pour les bornes...

Avec une API REST bien conçue, la logique métier est développée une seule fois dans le langage de choix (disons Node.js). Cette API unique sert ensuite tous les clients. L'application iOS appelle /api/v1/restaurants, l'application Android appelle le même endpoint, le site web aussi. Un seul backend pour tous les gouverner. Quand il faut ajouter une nouvelle fonctionnalité, un seul endroit à modifier. Quand il y a un bug dans le calcul des disponibilités, une seule correction résout le problème pour tous les clients.

Deuxième avantage : la réutilisabilité et l'économie des APIs

Imaginons un développeur qui souhaite créer une application de livraison de courses. Il a besoin de plusieurs fonctionnalités : afficher des cartes pour la géolocalisation, envoyer des SMS pour notifier les clients, traiter les paiements, envoyer des emails de confirmation. Sans APIs externes, il devrait créer toutes ces fonctionnalités lui-même, avec des années d'expérience dans chaque domaine.

Grâce aux web services, il peut utiliser l'API Google Maps pour la cartographie, l'API Twilio pour les SMS, l'API Stripe pour les paiements, l'API SendGrid pour les emails. Chaque service est fourni par des experts du domaine. Le développeur peut se concentrer sur sa valeur ajoutée réelle : la logique métier de la livraison de courses. On parle aujourd'hui d'une véritable "économie des APIs" où des entreprises créent de la valeur en exposant leurs capacités techniques.

Une anecdote célèbre : en 2006, Jeff Bezos a envoyé un mémo à tous les ingénieurs d'Amazon. Le message était clair et non négociable : "À partir de maintenant, toutes les équipes doivent exposer leurs fonctionnalités via des APIs. Plus aucun accès direct aux bases de données entre équipes. Pas de partage de mémoire. Rien. Uniquement des APIs. Et ces APIs doivent être conçues comme si elles allaient être exposées publiquement, même si ce n'est pas le cas initialement."

Les équipes techniques ont trouvé cette décision étrange. Pourquoi compliquer l'architecture interne avec toutes ces APIs ? Quelques années plus tard, Amazon a lancé Amazon Web Services (AWS). Comment ont-ils pu déployer des dizaines de services cloud aussi rapidement ? Parce que toutes les briques technologiques existaient déjà sous forme d'APIs internes. Il suffisait de les documenter proprement, d'ajouter une couche de facturation, et de les ouvrir au public. Aujourd'hui, AWS génère plus de 80 milliards de dollars de revenus annuels. Cette décision de 2006 s'est transformée en la plus grande source de profits d'Amazon.

Troisième avantage : la scalabilité et l'évolution

Imaginons Netflix au moment de passer du DVD par la poste au streaming vidéo. Leur application monolithique ne pouvait pas gérer des millions d'utilisateurs simultanés regardant des vidéos en haute définition. Leur solution a été radicale : découper leur application en centaines de microservices, chacun communiquant via des APIs bien définies.

Aujourd'hui, plus de 1000 microservices différents collaborent pour vous permettre de regarder vos séries préférées. Certains gèrent votre profil utilisateur, d'autres génèrent les recommandations personnalisées, d'autres transcodent les vidéos dans différentes qualités, d'autres gèrent les abonnements et la facturation. Le jour du lancement d'une saison très attendue d'une série populaire, Netflix peut multiplier par 10 les instances du service de streaming vidéo sans toucher aux autres services. Chaque brique scale indépendamment selon les besoins.

1.4 Les différentes familles de Web Services
Comparons les différents types de web services à des langues humaines, chacune avec sa philosophie et ses cas d'usage.

SOAP : L'allemand des web services

SOAP est comme l'allemand : très structuré, très formel, avec des règles grammaticales précises. Quand on lit un document SOAP, on sait immédiatement de quoi il s'agit car tout est explicitement décrit. C'est verbeux, ça prend de la place, mais c'est extrêmement rigoureux.

Imaginons une banque internationale qui doit transférer 10 millions d'euros entre deux systèmes bancaires situés dans des pays différents. Elle veut des garanties absolues : la transaction doit être sécurisée avec signatures numériques (WS-Security), fiable avec gestion des erreurs et retry automatiques (WS-ReliableMessaging), transactionnelle avec possibilité de rollback (WS-Transaction). SOAP fournit des standards pour tout ça. C'est lourd, c'est complexe, mais quand il s'agit de millions d'euros, on ne plaisante pas avec la robustesse.

REST : L'anglais des web services

REST est comme l'anglais : simple, pragmatique, flexible, et tout le monde le comprend. REST n'est pas vraiment un protocole rigide, c'est plutôt un ensemble de principes architecturaux. On utilise les verbes HTTP naturels (GET pour récupérer, POST pour créer, PUT pour modifier, DELETE pour supprimer) et des URLs pour identifier les ressources. C'est élégant, intuitif, et extrêmement flexible.

Imaginons une startup qui lance une API pour partager des photos. Avec REST, les endpoints sont évidents : GET /photos pour lister les photos, POST /photos pour en ajouter une, GET /photos/123 pour en voir une spécifique, DELETE /photos/123 pour la supprimer. Tout développeur comprend immédiatement comment ça fonctionne, même sans lire la documentation.

Le problème de cette flexibilité ? Deux APIs REST peuvent être très différentes. Il n'y a pas de standard strict comme WSDL pour SOAP, juste des conventions et des bonnes pratiques. C'est pourquoi une bonne documentation est absolument cruciale en REST.

GraphQL : L'espéranto des web services

GraphQL, c'est un peu l'espéranto : une langue construite pour être logique et efficace. L'idée centrale est brillante : au lieu que le serveur décide quelles données vous renvoyer, c'est vous, le client, qui spécifiez exactement ce dont vous avez besoin.

Imaginons le développement d'un réseau social. Pour afficher une page de profil, vous avez besoin du nom de l'utilisateur, sa photo de profil, et ses 10 derniers posts avec le nombre de likes. Avec REST, vous appelez GET /users/123 et récupérez peut-être 50 champs (adresse, téléphone, préférences, etc.) alors que vous n'en voulez que 5. Ensuite, vous appelez GET /users/123/posts et récupérez tous les champs de tous les posts. C'est du gaspillage de bande passante.

Avec GraphQL, vous écrivez une seule query qui dit précisément : "Pour l'utilisateur 123, je veux son nom, sa photo, et les titres de ses 10 derniers posts avec leur nombre de likes". Une seule requête, exactement les données nécessaires. GitHub a migré son API vers GraphQL et a constaté une réduction de 40% de la quantité de données transférées.

gRPC : Le mandarin des web services

gRPC, c'est le mandarin : très efficace quand on le maîtrise, mais avec une courbe d'apprentissage raide. gRPC utilise Protocol Buffers, un format binaire développé par Google, plutôt que du texte comme JSON ou XML. Résultat : c'est 5 à 10 fois plus rapide que REST. C'est bidirectionnel grâce à HTTP/2, ce qui permet du streaming dans les deux sens.

Imaginons Uber qui doit gérer la communication en temps réel entre les smartphones des chauffeurs et leurs serveurs. La position du véhicule doit être mise à jour toutes les secondes, les demandes de courses doivent arriver instantanément, le tout avec une latence minimale. gRPC excelle dans ce type de scénario. Mais gRPC est moins accessible : on ne peut pas simplement tester un appel gRPC dans un navigateur web comme on le ferait avec REST.

Dans ce cours, nous allons nous concentrer principalement sur REST, pour trois raisons. Premièrement, c'est de loin le plus utilisé : environ 80% des APIs publiques sont en REST. Deuxièmement, les concepts appris avec REST sont transférables. Si vous comprenez bien REST, apprendre GraphQL ou gRPC sera beaucoup plus facile. Et troisièmement, REST est parfait pour l'apprentissage car il est transparent : on peut voir exactement ce qui se passe, déboguer facilement, tester manuellement.

⚙️ PARTIE 2 : ARCHITECTURE CLIENT-SERVEUR ET HTTP (50 min)
2.1 Le modèle client-serveur
Le modèle client-serveur fonctionne comme un restaurant bien organisé. Imaginons que vous entrez dans un restaurant. Vous êtes le client, vous consultez le menu (le contrat), vous appelez le serveur (qui joue le rôle d'intermédiaire) et vous passez une commande : "Un steak frites, s'il vous plaît". Le serveur note votre commande, va en cuisine, transmet l'information au chef, attend que le plat soit prêt, puis revient avec votre assiette.

Vous ne rentrez pas en cuisine pour cuisiner vous-même. Vous ne connaissez pas les recettes exactes. Vous ne savez pas où sont stockés les ingrédients. Vous n'avez même pas besoin de savoir comment le chef s'organise. Tout ce que vous devez faire, c'est passer une commande claire selon le format du menu, et faire confiance au restaurant pour livrer ce que vous avez demandé.

C'est exactement comme ça que fonctionne le web. Votre navigateur (ou votre application mobile) est le client. Il envoie des requêtes : "donne-moi la page d'accueil de Google", "enregistre ce nouveau post Instagram", "récupère mes derniers emails". Le serveur reçoit ces requêtes, exécute les opérations nécessaires – peut-être interroger une base de données, faire des calculs complexes, appeler d'autres services – et renvoie une réponse.

Ce qui est brillant dans ce modèle, c'est la séparation des responsabilités. Le client n'a pas besoin de savoir comment le serveur fait son travail. Le serveur n'a pas besoin de savoir comment le client va utiliser les données ni comment il va les afficher. Cette séparation permet une flexibilité extraordinaire. Vous pouvez changer complètement votre interface utilisateur, passer d'Angular à React, redesigner toute l'expérience mobile, sans toucher une seule ligne de code du serveur. De l'autre côté, vous pouvez optimiser votre serveur, le réécrire complètement dans un autre langage, migrer votre base de données, tant qu'il répond toujours de la même manière aux mêmes requêtes, les clients n'y verront que du feu.

2.2 Le protocole HTTP
HTTP, ou HyperText Transfer Protocol, c'est le protocole qui fait tourner le web depuis 1991. Quand Tim Berners-Lee a créé le web au CERN, il aurait pu concevoir un protocole binaire complexe, optimisé pour la performance et l'efficacité. Au lieu de ça, il a fait un choix radical pour l'époque : créer un protocole basé sur du texte brut, lisible par l'homme. Cette décision, qui semblait naïve à certains ingénieurs de l'époque, a en réalité changé l'histoire de l'informatique. Grâce à cette lisibilité, n'importe quel développeur peut comprendre ce qui se passe, déboguer facilement, et même écrire des requêtes HTTP à la main si nécessaire.

Regardons à quoi ressemble une vraie requête HTTP. Imaginons une application web où un utilisateur remplit un formulaire d'inscription pour créer un nouveau compte. Quand il clique sur "S'inscrire", le navigateur va envoyer quelque chose comme ça au serveur :

http
POST /api/v1/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: application/json
Content-Length: 98

{
  "name": "Marie Dubois",
  "email": "marie.dubois@example.com",
  "password": "SecureP@ssw0rd"
}
Décortiquons chaque partie de cette requête car elles ont toutes leur importance.

La première ligne, POST /api/v1/users HTTP/1.1, s'appelle la ligne de requête. Elle contient trois informations essentielles : la méthode HTTP (POST), le chemin de la ressource (/api/v1/users), et la version du protocole HTTP utilisée (1.1, bien qu'HTTP/2 et HTTP/3 soient maintenant disponibles).

Ensuite viennent les headers, les en-têtes. Chaque header est une paire clé-valeur qui fournit des métadonnées sur la requête. Le header Host indique le domaine du serveur cible – c'est nécessaire parce qu'un même serveur physique peut héberger plusieurs sites web différents. Content-Type informe le serveur du format des données envoyées, ici du JSON. Authorization contient les credentials d'authentification – nous verrons en détail les différentes méthodes d'authentification dans quelques jours. User-Agent identifie le client qui fait la requête, ici un navigateur Firefox tournant sur Windows 10. Accept indique au serveur les formats de réponse que le client est capable de traiter.

Puis, après une ligne vide obligatoire (cette ligne vide est importante, elle marque la séparation entre les headers et le body), vient le body, le corps de la requête. C'est là qu'on met les données qu'on veut envoyer au serveur. Dans notre cas, les informations du nouvel utilisateur à créer, formatées en JSON.

Maintenant, imaginons que le serveur reçoit cette requête, vérifie que l'email n'est pas déjà utilisé, crée l'utilisateur dans sa base de données, et renvoie une réponse :

http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/v1/users/550e8400-e29b-41d4-a716-446655440000
Date: Fri, 24 Oct 2025 14:30:00 GMT
Content-Length: 156

{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Marie Dubois",
  "email": "marie.dubois@example.com",
  "createdAt": "2025-10-24T14:30:00Z"
}
La première ligne de la réponse, HTTP/1.1 201 Created, contient le code de statut. Le code 201 signifie "Created" – la ressource a été créée avec succès. Nous allons voir en détail tous les codes de statut dans un instant, mais retenez déjà que les codes commençant par 2 signifient succès, ceux commençant par 4 signifient erreur du côté client, et ceux commençant par 5 signifient erreur du côté serveur.

Le header Location est particulièrement intéressant et illustre une bonne pratique REST. Il indique l'URL complète où le client peut maintenant accéder à la ressource qui vient d'être créée. C'est comme si le serveur disait : "J'ai bien créé ton utilisateur, et si tu veux le récupérer plus tard, tu le trouveras à cette adresse."

Une anecdote amusante sur l'évolution de HTTP : dans les années 90, quand le web a commencé à exploser, les serveurs étaient rapidement submergés. Les ingénieurs ont réalisé qu'énormément de bande passante était gaspillée à renvoyer les mêmes ressources encore et encore. Une image de logo qui ne change jamais était téléchargée des milliers de fois par les mêmes utilisateurs. C'est là qu'ils ont inventé le système de caching HTTP. L'idée était simple mais géniale : le serveur peut dire au client "cette image ne changera pas avant demain à minuit, garde-la en cache sur ton disque dur". La prochaine fois que le client a besoin de cette image, il utilise sa copie locale plutôt que de la re-télécharger. Simple, élégant, et ça a littéralement permis au web de scaler à l'échelle mondiale.

2.3 Les méthodes HTTP : le vocabulaire des APIs
Les méthodes HTTP sont comme les verbes dans une phrase. Elles indiquent l'action que vous voulez effectuer sur une ressource. Il en existe plusieurs, mais cinq sont absolument essentielles pour créer des APIs RESTful : GET, POST, PUT, PATCH et DELETE. Comprendre quand et comment utiliser chacune d'elles est crucial pour concevoir une bonne API.

GET - Récupérer une ressource
GET est probablement la méthode que vous utilisez le plus souvent, même si vous ne vous en rendez pas compte. Chaque fois que vous tapez une URL dans votre navigateur et que vous appuyez sur Entrée, vous faites un GET. Chaque fois que vous cliquez sur un lien hypertexte, c'est un GET. GET signifie "récupère-moi cette ressource".

L'important avec GET, c'est qu'il ne doit jamais, jamais modifier quoi que ce soit sur le serveur. GET est ce qu'on appelle une méthode "safe" (sûre). Vous pouvez faire le même GET mille fois de suite, le résultat sera toujours identique, et surtout, l'état du serveur ne changera pas. Cette propriété est importante parce que les navigateurs, les proxies, les CDN peuvent cacher les réponses GET. Ils ne peuvent pas faire ça avec les autres méthodes car modifier quelque chose doit toujours être traité en temps réel.

Exemples concrets d'utilisation de GET :

GET /api/v1/products récupère la liste de tous les produits
GET /api/v1/products/789 récupère le produit avec l'ID 789
GET /api/v1/products?category=electronics&price_max=500 récupère les produits électroniques à moins de 500€
Notez dans ce dernier exemple comment on peut passer des paramètres dans l'URL avec le query string (après le point d'interrogation). C'est pratique pour le filtrage, la pagination, le tri.

Une erreur classique qu'on voit souvent chez les débutants : utiliser GET pour des actions. Par exemple, créer un endpoint comme GET /api/v1/users/123/delete. C'est une très mauvaise idée. Pourquoi ? Parce que GET ne devrait jamais supprimer quoi que ce soit. Imaginez qu'un moteur de recherche comme Google découvre votre API et qu'un crawler automatique suit ce lien... Boom, votre utilisateur 123 est supprimé. Utilisez toujours la méthode appropriée pour l'action appropriée.

POST - Créer une nouvelle ressource
POST est la méthode pour créer de nouvelles ressources. Quand vous remplissez un formulaire d'inscription sur un site web et que vous cliquez sur le bouton "S'inscrire", votre navigateur fait un POST. Quand vous publiez un tweet, c'est un POST. Quand vous ajoutez un produit à votre panier en ligne, POST.

POST n'est ni safe ni idempotent. Si vous faites POST deux fois avec exactement les mêmes données, vous créerez deux ressources différentes (deux comptes utilisateur distincts, deux tweets identiques, etc.). C'est une distinction importante avec PUT que nous allons voir dans un instant.

Exemple d'utilisation :

http
POST /api/v1/products
Content-Type: application/json

{
  "name": "iPhone 15 Pro",
  "price": 1199.99,
  "category": "electronics",
  "stock": 50
}
Le serveur répond typiquement avec un code 201 Created et inclut un header Location qui pointe vers la nouvelle ressource : Location: /api/v1/products/456. Le body de la réponse contient généralement la ressource créée avec son ID généré par le serveur.

PUT - Remplacer complètement une ressource
PUT est utilisé pour la mise à jour complète d'une ressource existante. La différence subtile mais importante avec POST, c'est que PUT est idempotent. Si vous faites PUT plusieurs fois avec exactement les mêmes données, le résultat final sera identique à si vous l'aviez fait une seule fois. L'état de la ressource après 10 PUT identiques est le même qu'après 1 seul PUT.

Avec PUT, vous remplacez complètement la ressource. Tous les champs doivent être fournis dans la requête.

http
PUT /api/v1/products/789
Content-Type: application/json

{
  "name": "iPhone 15 Pro Max",
  "price": 1299.99,
  "category": "electronics",
  "stock": 30,
  "description": "Dernier modèle avec écran Retina"
}
Si vous oubliez un champ, ce champ sera supprimé ou mis à null sur le serveur (selon l'implémentation). C'est pourquoi PUT est souvent moins pratique que PATCH dans la réalité.

PATCH - Mise à jour partielle
PATCH est une addition relativement récente aux méthodes HTTP couramment utilisées, mais elle est devenue essentielle. Avec PATCH, vous ne spécifiez que les champs que vous voulez modifier. Les autres champs restent intacts.

http
PATCH /api/v1/products/789
Content-Type: application/json

{
  "price": 1099.99,
  "stock": 25
}
Seuls le prix et le stock sont modifiés. Le nom, la catégorie, la description restent inchangés.

Pour bien comprendre la différence entre PUT et PATCH, imaginons que vous modifiez votre profil sur un réseau social. Avec PUT, c'est comme si vous deviez remplir tous les champs du formulaire à chaque fois, même ceux que vous ne voulez pas changer. Si vous voulez juste changer votre photo de profil, vous devez quand même renvoyer votre nom, votre adresse, votre téléphone, etc. Avec PATCH, vous envoyez seulement la nouvelle photo, et le reste ne bouge pas. PATCH est plus pratique dans 90% des cas d'usage réels.

DELETE - Supprimer une ressource
DELETE fait exactement ce que son nom indique : supprimer une ressource. Simple et efficace.

http
DELETE /api/v1/products/789
Le serveur supprime le produit 789 et répond généralement avec un code 204 No Content (succès, mais pas de contenu à renvoyer) ou 200 OK avec un message de confirmation.

DELETE est idempotent : si vous supprimez une ressource qui n'existe plus, le résultat est le même que si vous la supprimiez la première fois. La ressource n'existe plus dans les deux cas. Certaines APIs renvoient 404 Not Found quand on essaie de supprimer quelque chose d'inexistant, d'autres renvoient 204 No Content même dans ce cas. Les deux approches se défendent.

Un détail intéressant : contrairement à ce qu'on pourrait penser, DELETE peut avoir un body. C'est rare, mais parfois utile. Imaginons que vous voulez supprimer plusieurs produits en une seule requête. Vous pourriez faire DELETE /api/v1/products avec un body contenant un tableau d'IDs. Mais attention, c'est controversé dans la communauté REST. Certains puristes préfèrent créer un endpoint spécial comme POST /api/v1/products/bulk-delete.

HEAD et OPTIONS - Les méthodes auxiliaires
Deux autres méthodes méritent d'être mentionnées, même si elles sont moins utilisées au quotidien.

HEAD est exactement comme GET, sauf que le serveur ne renvoie que les headers, pas le body. Imaginons que vous voulez vérifier si un très gros fichier existe ou connaître sa taille avant de le télécharger. Avec HEAD, vous obtenez ces informations sans gaspiller de bande passante à télécharger tout le fichier.

http
HEAD /api/v1/files/huge-video.mp4
Le serveur répond avec les headers incluant Content-Length: 5368709120 (5 Go), mais sans envoyer les 5 Go de données.

OPTIONS permet au client de découvrir quelles méthodes HTTP sont supportées sur un endpoint particulier. Le serveur répond avec un header Allow listant les méthodes disponibles.

http
OPTIONS /api/v1/products/789

Réponse:
Allow: GET, PUT, PATCH, DELETE, HEAD, OPTIONS
OPTIONS est également utilisé automatiquement par le mécanisme CORS (Cross-Origin Resource Sharing) que nous verrons plus tard. Quand un navigateur veut faire une requête vers un domaine différent, il envoie d'abord une requête OPTIONS pour vérifier que c'est autorisé.

2.4 Les codes de statut HTTP : apprendre à parler au client
Les codes de statut HTTP sont organisés en cinq familles, et cette organisation n'est pas du tout aléatoire. Chaque famille a une signification claire qui aide à comprendre immédiatement ce qui s'est passé.

Famille 1xx - Informationnel (rare)
Les codes 1xx sont informationnels et vous les rencontrerez rarement. Le plus connu est 100 Continue, utilisé dans un pattern d'optimisation spécifique. Imaginons que vous voulez uploader un fichier de 2 Go vers un serveur. Avant d'envoyer tout ce fichier, votre client peut demander "puis-je envoyer ce gros fichier ?" avec un header Expect: 100-continue. Si le serveur répond 100 Continue, vous envoyez le fichier. Si le serveur répond 401 Unauthorized ou 413 Payload Too Large, vous économisez 2 GB de bande passante.

Famille 2xx - Succès
Les codes 2xx signifient que la requête a été traitée avec succès. C'est la famille heureuse des codes de statut.

200 OK est le plus commun : "J'ai bien traité ta requête et voilà le résultat". C'est le code par défaut pour une opération réussie.

201 Created signifie "J'ai créé la ressource que tu m'as demandé de créer". Utilisé typiquement en réponse à POST. Devrait idéalement inclure un header Location pointant vers la nouvelle ressource.

204 No Content est intéressant : "J'ai bien traité ta requête, mais je n'ai rien à te renvoyer". Souvent utilisé pour DELETE ou pour des mises à jour où le client n'a pas besoin de récupérer la ressource modifiée.

Une anecdote révélatrice : imaginons un développeur junior qui crée son première API. Il fait en sorte que tous les endpoints renvoient toujours 200 OK, même en cas d'erreur. Son raisonnement ? "La requête HTTP s'est bien passée techniquement, donc c'est un succès". Le problème devient apparent très vite : son client front-end affiche toujours "Opération réussie avec succès !", même quand l'utilisateur qu'on essaie de créer existe déjà, même quand le mot de passe est incorrect, même quand la base de données est hors ligne. Résultat : confusion totale des utilisateurs et cauchemar de debugging. Il faut tout refactoriser pour utiliser les bons codes de statut. Les codes de statut ne parlent pas du succès de la transmission HTTP en tant que telle, mais du succès de l'opération métier demandée.

Famille 3xx - Redirection
Les codes 3xx concernent les redirections. Ils indiquent que le client doit effectuer une action supplémentaire pour compléter sa requête.

301 Moved Permanently signifie "Cette ressource a déménagé de façon permanente, utilise désormais cette nouvelle URL". Les navigateurs et moteurs de recherche mettent à jour leurs liens. Utilisé lors de refonte de sites web ou de réorganisation d'APIs.

302 Found (ou 307 Temporary Redirect en HTTP/1.1) signifie "La ressource est temporairement à une autre URL, mais continue d'utiliser l'URL originale à l'avenir". Utilisé pour les maintenances temporaires.

304 Not Modified est crucial pour le caching. Imaginons un client qui a une copie d'une ressource et veut savoir si elle est toujours à jour. Il fait une requête GET avec un header If-Modified-Since: Tue, 15 Oct 2025 10:00:00 GMT. Si la ressource n'a pas changé depuis cette date, le serveur répond 304 Not Modified sans body, économisant énormément de bande passante.

Famille 4xx - Erreur client
Les codes 4xx indiquent une erreur du côté du client. C'est lui qui a fait quelque chose de mal dans sa requête. Le serveur, lui, fonctionne correctement.

400 Bad Request signifie "Ta requête est malformée, je ne peux même pas la comprendre". Utilisé quand le JSON est invalide, quand des paramètres obligatoires manquent, quand le format ne correspond pas à ce qui est attendu.

401 Unauthorized (nom historiquement très mauvais, devrait être "Unauthenticated") signifie "Tu n'es pas authentifié, je ne sais pas qui tu es". Le client doit fournir des credentials valides. C'est la réponse appropriée quand un token d'authentification est manquant ou invalide.

403 Forbidden signifie "Je sais parfaitement qui tu es grâce à ton authentification, mais tu n'as pas le droit de faire cette action". Imaginons un utilisateur normal qui essaie de supprimer un compte administrateur. Il est authentifié (pas de 401), mais il n'a pas la permission (403).

404 Not Found - tout le monde connaît celui-là depuis les débuts du web. "La ressource que tu cherches n'existe pas". Important : 404 signifie que la ressource n'existe pas, pas que l'endpoint est invalide. Si quelqu'un appelle GET /api/v1/users/999 et que l'utilisateur 999 n'existe pas, c'est un 404. Si quelqu'un appelle GET /api/v1/inexistant, c'est aussi un 404, mais idéalement on pourrait même renvoyer 400 ou créer une page d'aide.

422 Unprocessable Entity est souvent mal compris. Il signifie "Ta requête est syntaxiquement correcte, mais sémantiquement incorrecte". Imaginons que vous envoyez du JSON parfaitement valide pour créer un utilisateur (pas de 400), mais l'email est déjà utilisé par un autre compte. La requête est bien formée, mais vous ne pouvez pas la traiter pour des raisons métier. C'est un 422.

429 Too Many Requests signifie "Tu fais trop de requêtes, ralentis". Utilisé par les systèmes de rate limiting. Devrait inclure un header Retry-After indiquant quand le client peut réessayer.

Famille 5xx - Erreur serveur
Les codes 5xx indiquent que le serveur a rencontré un problème. Le client a fait une requête valide, mais le serveur n'a pas pu la traiter. C'est la faute du serveur, pas du client.

500 Internal Server Error est le code d'erreur générique : "Quelque chose a planté de mon côté, mais je ne peux pas (ou ne veux pas) te dire quoi exactement". C'est souvent le résultat d'une exception non gérée dans le code. Dans les logs serveur, vous trouverez la vraie cause (division par zéro, null pointer, base de données inaccessible...), mais le client reçoit juste un 500.

502 Bad Gateway signifie "Je suis un proxy ou une API gateway, et le serveur derrière moi a renvoyé une réponse invalide". Imaginons une architecture avec une API gateway devant plusieurs microservices. Si un microservice crash et renvoie du charabia, la gateway renvoie un 502 au client.

503 Service Unavailable signifie "Je suis temporairement indisponible (maintenance programmée, surcharge, problème temporaire)". Devrait inclure un header Retry-After pour indiquer quand réessayer. Utilisé également quand toutes les workers threads sont occupées ou quand la base de données est en maintenance.

504 Gateway Timeout signifie "Je suis un proxy ou une gateway, et le serveur derrière moi n'a pas répondu dans le délai imparti". Imaginons que votre API gateway attend maximum 30 secondes une réponse d'un microservice, mais celui-ci ne répond pas. La gateway renvoie un 504 au client.

La règle d'or à retenir : 4xx = c'est la faute du client, 5xx = c'est la faute du serveur. Cette distinction est cruciale pour le debugging et le monitoring.

📊 PARTIE 3 : FORMATS D'ÉCHANGE DE DONNÉES (40 min)
3.1 Pourquoi des formats structurés ?
Imaginons que vous devez transmettre les informations d'un utilisateur d'une application à une autre. Vous pourriez simplement concaténer les valeurs : "John Doe|john@example.com|30|true". Mais cette approche pose immédiatement plusieurs problèmes. Comment le destinataire sait-il que le premier élément est le nom, le deuxième l'email, le troisième l'âge, le quatrième le statut actif ? Que se passe-t-il si le nom de l'utilisateur contient un pipe (|) ? Comment représenter des structures plus complexes, comme l'adresse avec rue, ville et code postal ? Et si l'utilisateur n'a pas fourni son âge, comment représenter cette absence d'information ?

C'est exactement pour résoudre ces problèmes que des formats structurés standardisés ont été créés. Les deux principaux acteurs dans le monde des web services sont JSON et XML. Chacun a ses forces, ses faiblesses, et ses cas d'usage privilégiés.

3.2 JSON - Le roi incontesté du web moderne
JSON (JavaScript Object Notation) a été créé par Douglas Crockford au début des années 2000. L'histoire de sa création est amusante et illustre parfaitement comment les meilleures solutions sont souvent les plus simples. Crockford cherchait un format léger pour échanger des données entre du code JavaScript côté client et le serveur. Il a proposé JSON à ses collègues de travail qui ont répondu : "C'est trop simple, ça ne marchera jamais pour des applications sérieuses". Crockford a persisté, publié la spécification, et aujourd'hui JSON a littéralement mangé le monde du web.

Structure et syntaxe de base
Voici un exemple complet de données utilisateur en JSON :

json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Marie Dubois",
  "age": 28,
  "email": "marie.dubois@example.com",
  "isActive": true,
  "roles": ["user", "moderator", "contributor"],
  "address": {
    "street": "10 rue de la Paix",
    "city": "Paris",
    "zipCode": "75001",
    "country": "France"
  },
  "preferences": null,
  "lastLogin": "2025-10-24T14:30:00Z"
}
JSON supporte six types de données fondamentaux. Les strings (chaînes de caractères) sont entourées de guillemets doubles : "texte". Les numbers peuvent être des entiers ou des décimaux : 42, 3.14159, -17, 1.5e10. Les booleans s'écrivent true ou false (en minuscules). Les arrays (tableaux) sont des listes entre crochets : [1, 2, 3] ou ["rouge", "vert", "bleu"]. Les objects sont des paires clé-valeur entre accolades : {"key": "value", "age": 30}. Et enfin null représente l'absence explicite de valeur.

Pourquoi JSON a gagné la bataille des formats
Plusieurs raisons expliquent la domination absolue de JSON dans le monde des APIs modernes. Premièrement, sa simplicité. Un humain peut lire et comprendre du JSON sans formation particulière. Un développeur peut même écrire du JSON à la main sans outil spécialisé. Deuxièmement, sa légèreté. JSON est beaucoup moins verbeux que XML – pour les mêmes données, un document JSON fait généralement 30 à 50% de moins qu'un document XML équivalent.

Troisièmement, et c'est crucial, JSON est natif en JavaScript. Pas besoin de bibliothèque externe, JSON.parse() et JSON.stringify() sont intégrés au langage. Quand JSON est arrivé, le web était dominé par JavaScript côté client. L'adoption a été immédiate. Quatrièmement, l'universalité. Aujourd'hui, absolument tous les langages modernes ont des bibliothèques JSON performantes et faciles à utiliser : Python avec json, Java avec Jackson ou Gson, Go avec encoding/json, Rust avec Serde...

Bonnes pratiques JSON
Nommage cohérent : Choisissez une convention de nommage et tenez-vous-y dans toute votre API. En JavaScript, on utilise généralement camelCase : firstName, lastName, isActive. En Python et Ruby, on préfère snake_case : first_name, last_name, is_active. Les deux sont valides, l'important est la cohérence. Évitez kebab-case (first-name) dans JSON car ça pose problème en JavaScript où le tiret est un opérateur de soustraction.

Structure cohérente pour les collections : Quand vous renvoyez une liste d'éléments, enveloppez toujours le tableau dans un objet avec des métadonnées utiles :

json
{
  "data": [
    {"id": 1, "name": "Product A", "price": 29.99},
    {"id": 2, "name": "Product B", "price": 49.99}
  ],
  "page": 1,
  "limit": 20,
  "total": 156,
  "hasMore": true
}
Pourquoi ne pas renvoyer directement le tableau ? Parce que si demain vous voulez ajouter des métadonnées (nombre total d'éléments, numéro de page, liens de pagination...), vous devrez changer la structure de réponse, ce qui casse la compatibilité avec les clients existants.

Gestion des dates : Utilisez toujours le format ISO 8601 : "2025-10-24T14:30:00Z". Le T sépare la date de l'heure, le Z signifie UTC (Coordinated Universal Time). Pour un fuseau horaire spécifique : "2025-10-24T14:30:00+02:00" (UTC+2). N'utilisez jamais des formats ambigus comme "24/10/2025" (est-ce octobre ou décembre ? Jour-mois ou mois-jour ?).

Pas de commentaires : JSON ne supporte pas les commentaires. Si vous avez besoin de documenter votre format, faites-le dans la documentation OpenAPI ou dans un fichier README séparé, pas dans le JSON lui-même. Certains parseurs acceptent des extensions avec commentaires (comme JSON5), mais ce n'est pas standard.

Validation avec JSON Schema : Pour des APIs professionnelles, utilisez JSON Schema pour définir et valider la structure de vos données :

json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "minLength": 1,
      "maxLength": 100
    },
    "age": {
      "type": "integer",
      "minimum": 0,
      "maximum": 150
    },
    "email": {
      "type": "string",
      "format": "email"
    }
  },
  "required": ["name", "email"]
}
Ce schéma dit : un objet avec un name (chaîne de 1 à 100 caractères), un age optionnel (entier de 0 à 150), et un email (chaîne au format email). Les champs name et email sont obligatoires.

3.3 XML - L'ancien champion toujours vivant
XML (eXtensible Markup Language) a dominé le monde des web services dans les années 2000. Aujourd'hui, son utilisation a dramatiquement diminué dans les nouvelles APIs, mais il reste omniprésent dans certains domaines : systèmes legacy des grandes entreprises, finance (standard SWIFT), santé (standard HL7), gouvernement, et dans tous les systèmes SOAP.

Voici les mêmes données utilisateur en XML :

xml
<?xml version="1.0" encoding="UTF-8"?>
<user id="550e8400-e29b-41d4-a716-446655440000">
  <name>Marie Dubois</name>
  <age>28</age>
  <email>marie.dubois@example.com</email>
  <isActive>true</isActive>
  <roles>
    <role>user</role>
    <role>moderator</role>
    <role>contributor</role>
  </roles>
  <address>
    <street>10 rue de la Paix</street>
    <city>Paris</city>
    <zipCode>75001</zipCode>
    <country>France</country>
  </address>
  <preferences xsi:nil="true"/>
  <lastLogin>2025-10-24T14:30:00Z</lastLogin>
</user>
Avantages de XML
XML a des atouts réels qui expliquent pourquoi il survit dans certains domaines. La validation stricte : avec XSD (XML Schema Definition), vous pouvez définir une structure extrêmement précise et contrainte. Les namespaces permettent d'éviter les conflits de noms quand vous combinez des données de différentes sources. La flexibilité attributs/éléments : vous pouvez mettre des données soit dans des attributs (<user id="123">) soit dans des éléments (<user><id>123</id></user>), selon ce qui a le plus de sens. Les standards robustes : XPath pour requêter dans un document XML, XSLT pour transformer XML en HTML ou autre format, tout un écosystème mature existe.

Pourquoi XML a perdu
Malgré ses qualités indéniables, XML a été progressivement abandonné au profit de JSON pour plusieurs raisons concrètes.

D'abord, sa verbosité excessive. Prenons un exemple simple : pour représenter une liste de trois produits avec leur nom et prix. En JSON, ça fait environ 180 caractères. En XML, le même contenu fait 320 caractères. Presque le double ! Les balises ouvrantes et fermantes (<product>...</product>), les déclarations XML en en-tête, tout ça prend de la place. Quand vous transmettez un million d'enregistrements, cette différence devient significative. Ça représente des coûts en bande passante, en stockage, en temps de transfert. À l'échelle d'une entreprise comme Amazon qui fait des milliards de requêtes par jour, économiser 40% de bande passante, c'est des millions d'euros par an.

Ensuite, sa complexité. XML est venu avec tout un écosystème de technologies associées : XSD pour les schémas, XSLT pour les transformations, XPath pour les requêtes, les namespaces pour éviter les conflits (xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"), les préfixes (xsi:type="string"). Pour un développeur débutant, c'est intimidant. Il faut comprendre tous ces concepts avant même de pouvoir utiliser XML correctement. JSON, au contraire, s'apprend en 20 minutes. Vous ouvrez un fichier JSON, vous comprenez immédiatement la structure.

La lisibilité joue également un rôle. Comparez ces deux représentations d'un objet simple :

JSON :

json
{"name": "Marie", "age": 28, "city": "Paris"}
XML :

xml
<person>
  <name>Marie</name>
  <age>28</age>
  <city>Paris</city>
</person>
JSON est plus compact et plus facile à scanner visuellement. Quand vous debuggez une API à 2h du matin, cette différence de lisibilité compte.

Enfin, le timing. Quand JSON est arrivé au milieu des années 2000, le web était en pleine explosion. JavaScript dominait côté client. Les développeurs web cherchaient quelque chose de simple et léger. JSON était natif en JavaScript (JSON.parse(), JSON.stringify()), alors que XML nécessitait des parsers lourds et complexes. L'adoption a été naturelle et rapide. XML n'a jamais eu cette chance : il est arrivé à une époque où les technologies étaient plus lourdes, puis est resté associé à SOAP et à la complexité de l'entreprise.

3.4 JSON vs XML : quand utiliser quoi ?
La décision n'est pas toujours évidente, mais voici quelques lignes directrices pragmatiques.

Utilisez JSON si vous construisez une nouvelle API en 2025, si la performance et la bande passante sont importantes, si vous travaillez principalement avec JavaScript ou des applications web/mobile modernes, si vous voulez quelque chose de simple que n'importe quel développeur peut comprendre rapidement.

Utilisez XML si vous devez intégrer avec des systèmes legacy existants qui ne parlent que XML, si vous travaillez dans un domaine fortement réglementé (finance, santé, aérospatial) où des standards XML sont imposés, si vous avez besoin d'une validation de schéma extrêmement stricte et complexe, si le client l'exige contractuellement (et vous ne pouvez pas négocier).

Une anecdote réaliste : imaginons une grande banque européenne qui souhaite moderniser ses APIs. L'équipe technique est enthousiaste à l'idée de passer de SOAP/XML à REST/JSON. Après une analyse approfondie, ils découvrent qu'ils ont 200 systèmes internes qui consomment ces APIs en XML, certains datant de 15 ans. Le coût de migration de tous ces systèmes serait astronomique - plusieurs millions d'euros et plusieurs années de travail. Solution pragmatique : créer une nouvelle API REST/JSON pour tous les nouveaux clients et projets, maintenir l'ancienne API SOAP/XML pour les systèmes internes legacy. Parfois, le pragmatisme l'emporte sur la modernité technologique.

3.5 Autres formats à connaître
Le monde ne se limite pas à JSON et XML. D'autres formats ont trouvé leur niche.

Protocol Buffers (Protobuf) est un format binaire développé par Google pour gRPC. Contrairement à JSON et XML qui sont texte, Protobuf est binaire. Il est extrêmement compact et très rapide à parser. Exemple de définition :

protobuf
message User {
  string id = 1;
  string name = 2;
  int32 age = 3;
  string email = 4;
  repeated string roles = 5;
}
Protobuf est utilisé quand la performance est absolument critique : communications entre microservices internes, IoT avec bande passante limitée, applications de gaming temps réel. Inconvénient : pas lisible par l'homme, nécessite un compilateur pour générer du code.

MessagePack est comme JSON mais en format binaire. Plus compact que JSON, plus rapide à parser, tout en gardant une structure similaire. Redis l'utilise pour certaines opérations. Utile quand vous voulez les avantages de JSON mais avec de meilleures performances.

YAML (YAML Ain't Markup Language) est super pour les fichiers de configuration (Docker Compose, Kubernetes, GitHub Actions), mais moins adapté pour les APIs. Il est lisible mais sensible à l'indentation, ce qui cause souvent des bugs subtils. Exemple :

yaml
user:
  name: Marie Dubois
  age: 28
  roles:
    - user
    - moderator
  address:
    city: Paris
    country: France
YAML est excellent pour la configuration, mais évitez-le pour les échanges de données en production. Un espace en trop peut casser tout le parsing.

🎓 RÉCAPITULATIF ET POINTS CLÉS (15 min)
Ce qu'il faut absolument retenir
1. Les web services permettent l'interopérabilité universelle

Un seul backend peut servir tous vos clients : applications mobiles iOS et Android, sites web, objets connectés IoT, systèmes internes. Vous développez la logique métier une fois, vous l'exposez via une API bien conçue, et tous vos clients en profitent.

2. REST domine le paysage actuel

Environ 80% des APIs publiques sont en REST. SOAP survit dans les systèmes legacy et les domaines réglementés. GraphQL et gRPC ont leurs niches (interfaces riches et communications internes performantes respectivement). Si vous créez une nouvelle API en 2025, REST est le choix par défaut sauf cas spéciaux.

3. HTTP est simple mais incroyablement puissant

Comprendre les méthodes HTTP, les headers et les codes de statut est fondamental. Ces concepts simples permettent de construire des systèmes extrêmement sophistiqués.

4. Chaque méthode HTTP a son rôle

GET : lire une ressource (safe, idempotent, pas de body)
POST : créer une ressource (ni safe ni idempotent, avec body)
PUT : remplacer complètement une ressource (idempotent, avec body complet)
PATCH : modifier partiellement une ressource (avec body partiel)
DELETE : supprimer une ressource (idempotent, généralement sans body)
Ne mélangez pas les rôles. N'utilisez jamais GET pour supprimer ou modifier quelque chose.

5. Les codes de statut ont du sens précis

2xx : succès (200 OK, 201 Created, 204 No Content)
3xx : redirection (301 Moved Permanently, 304 Not Modified)
4xx : erreur du client - c'est lui qui a mal fait (400, 401, 403, 404, 422, 429)
5xx : erreur du serveur - c'est notre faute (500, 502, 503, 504)
Utiliser les bons codes de statut facilite énormément le debugging et améliore l'expérience développeur.

6. JSON est le format standard moderne

Simple, léger, universel, natif JavaScript. À moins d'avoir une raison spécifique (legacy, réglementation, performance extrême), choisissez JSON. Utilisez ISO 8601 pour les dates, soyez cohérent dans le nommage, enveloppez les tableaux dans des objets avec métadonnées.

7. L'architecture client-serveur sépare les responsabilités

Le client n'a pas besoin de savoir comment le serveur fait son travail. Cette séparation permet de faire évoluer les deux indépendamment. Vous pouvez refaire complètement votre interface utilisateur sans toucher au backend. Vous pouvez optimiser ou réécrire votre serveur sans impacter les clients.

Préparation pour cet après-midi
Maintenant que vous avez compris les fondamentaux théoriques, vous allez passer à la pratique. Cet après-midi, vous allez construire les fondations de votre projet fil rouge SmartCity. Concrètement, vous allez :

Constituer vos équipes : 5 équipes au total. Une équipe front-end qui va consommer les APIs. Une équipe dédiée à l'authentification qui va fournir le service d'auth pour toute la plateforme. Et trois équipes qui vont chacune choisir un domaine métier : événements culturels, transports urbains, parkings intelligents, bibliothèque municipale, système de troc entre citoyens, ou autres.

Choisir votre stack technique : Node.js avec Express ? Python avec FastAPI ? Java avec Spring Boot ? C'est vous qui décidez. Choisissez ce qui correspond à vos compétences et à vos envies d'apprentissage. L'important n'est pas le langage choisi, c'est la qualité de votre API.

Rédiger un contrat OpenAPI : Vous allez documenter 2 ou 3 endpoints seulement pour commencer. Pas la peine de tout faire d'un coup. Commencez simple : un GET pour lister les ressources, un GET pour récupérer une ressource spécifique, peut-être un POST pour en créer une. Vous étofferez au fur et à mesure des 8 demi-journées.

Initialiser votre projet Git : Structure de dossiers propre, README clair, premier commit. Vous travaillez en équipe, donc Git sera votre outil de collaboration principal.

Définir votre roadmap : Qu'allez-vous faire demain ? Dans trois jours ? À la fin du projet ? Avoir une vision claire vous aidera à rester focalisés et à avancer progressivement.

Conseils pour l'après-midi
Gardez les choses simples aujourd'hui. Vous n'allez pas créer une API production-ready en 3h30. L'objectif est de poser les fondations solides. Il vaut mieux avoir 2 endpoints bien pensés et bien documentés que 10 endpoints mal ficelés.

Communiquez entre équipes. L'équipe front-end a besoin de savoir quels endpoints seront disponibles. Les équipes services ont besoin de savoir comment s'authentifier auprès du service auth. Parlez-vous, synchronisez-vous, mettez-vous d'accord sur les conventions (nommage, format des erreurs, pagination...).

N'ayez pas peur de l'itération. Si vous vous rendez compte demain que votre contrat OpenAPI n'est pas optimal, vous pourrez le modifier. C'est tout l'intérêt d'un projet fil rouge sur 8 demi-journées : apprendre progressivement, faire des erreurs, corriger, s'améliorer.

Amusez-vous. Vous allez construire une vraie plateforme de microservices. C'est exactement ce qu'on fait dans l'industrie aujourd'hui. Les compétences que vous allez acquérir sont directement applicables en entreprise.

Questions fréquentes anticipées
"Faut-il connecter une base de données dès aujourd'hui ?" Non. Aujourd'hui, concentrez-vous sur le contrat OpenAPI et la structure du projet. Demain (J2), vous implémenterez les endpoints avec des données en dur dans des tableaux en mémoire. Après-demain (J3), vous connecterez la vraie base de données.

"Peut-on changer de stack technique en cours de route ?" Théoriquement oui, mais ce sera coûteux. Choisissez bien aujourd'hui. Si vous hésitez entre deux options, prenez celle que votre équipe maîtrise le mieux.

"Comment on fait si deux équipes veulent le même domaine métier ?" Premier arrivé, premier servi. Ou négociez. Il y a largement assez de domaines différents pour tout le monde. L'important est que chaque équipe ait un domaine distinct.

"L'équipe front doit-elle connaître React/Vue/Angular ?" L'équipe front devrait avoir au moins un membre à l'aise avec un framework moderne. Si personne ne maîtrise ces technos, commencez avec du HTML/JavaScript vanilla, ça marchera aussi.

"C'est grave si on ne finit pas tout cet après-midi ?" Non. L'objectif minimum : un fichier OpenAPI avec 2 endpoints, un projet Git initialisé, un README. Si vous avez ça, c'est déjà très bien. Vous continuerez demain.

📚 RESSOURCES COMPLÉMENTAIRES
Pour approfondir vos connaissances
Documentation officielle et spécifications :

RFC 7231 : HTTP/1.1 Semantics and Content - La spec complète de HTTP
RFC 7807 : Problem Details for HTTP APIs - Standard pour les erreurs
Spécification OpenAPI 3.0 : https://swagger.io/specification/
JSON Schema : https://json-schema.org/
Livres recommandés :

"Architectural Styles and the Design of Network-based Software Architectures" par Roy Fielding (2000) - La thèse originale qui définit REST
"RESTful Web APIs" par Leonard Richardson et Mike Amundsen - Excellent guide pratique
"Designing Data-Intensive Applications" par Martin Kleppmann - Pour comprendre les systèmes distribués
Outils à explorer dès maintenant :

Postman : L'outil incontournable pour tester des APIs. Interface graphique intuitive, possibilité de sauvegarder des collections de requêtes, génération de code client.
Swagger Editor : Éditeur en ligne pour créer vos fichiers OpenAPI avec preview en temps réel. Disponible sur https://editor.swagger.io/
HTTPie : Client HTTP en ligne de commande, plus friendly que curl. Syntaxe simple : http GET api.example.com/users
JSON Formatter : Extension navigateur pour afficher du JSON formaté joliment
Prism : Outil que vous utiliserez demain pour générer des mocks automatiques à partir de votre OpenAPI
Exercices optionnels pour ce soir
Si vous voulez vous préparer pour demain, voici quelques exercices légers :

1. Installez les dépendances de votre stack Si vous avez choisi Node.js, installez Node et npm. Si c'est Python, installez Python et pip. Si c'est Java, vérifiez que le JDK est installé. Faites un hello world basique pour vérifier que tout fonctionne.

2. Explorez Swagger Editor Allez sur https://editor.swagger.io/ et regardez l'exemple par défaut. Modifiez quelques éléments, voyez comment la documentation à droite se met à jour automatiquement. C'est cet outil que vous utiliserez cet après-midi.

3. Testez quelques APIs publiques Prenez Postman, créez un compte gratuit, et testez quelques APIs publiques simples :

JSONPlaceholder : https://jsonplaceholder.typicode.com/ (API de test avec des posts, users, comments fictifs)
OpenWeatherMap : https://openweathermap.org/api (météo, nécessite une clé gratuite)
GitHub API : https://api.github.com/users/github (pas besoin d'auth pour les requêtes basiques)
Regardez les requêtes que vous envoyez (méthode, headers, body) et les réponses que vous recevez (status code, headers, body JSON). Ça vous donnera une intuition concrète de comment fonctionnent les APIs REST.

4. Lisez la documentation de votre framework Si vous partez sur Express.js, parcourez la doc officielle. Si c'est FastAPI, regardez les premiers tutoriels. Si c'est Spring Boot, familiarisez-vous avec les annotations de base. Pas besoin de tout retenir, juste avoir une vision d'ensemble.

Ce qui vous attend demain (Jour 2)
Théorie du matin : API REST avancé. Vous allez apprendre les principes architecturaux de REST en profondeur, les bonnes pratiques de design d'APIs, le versioning, la documentation OpenAPI complète, et une introduction aux concepts d'hypermedia (HATEOAS).

Pratique de l'après-midi : Implémentation basique avec mock data. Vous allez coder vos 2-3 premiers endpoints avec des données en dur stockées dans des tableaux en mémoire. Vous allez générer des mocks automatiques avec Prism. L'équipe front pourra commencer à consommer ces mocks. Vous ajouterez un middleware de gestion d'erreurs selon RFC 7807, et vous écrirez vos premiers tests unitaires.

À demain, l'objectif est d'avoir du code qui tourne. Pas du code production-ready, mais du code fonctionnel qui répond à des requêtes HTTP et renvoie du JSON valide. Ça va devenir très concret.

Un dernier mot
Les web services sont au cœur de l'informatique moderne. Chaque application que vous utilisez quotidiennement – Instagram, Uber, Netflix, WhatsApp – repose sur des APIs. Les compétences que vous allez acquérir dans ce cours sont parmi les plus demandées sur le marché du travail. Un développeur qui maîtrise la conception et l'implémentation d'APIs REST solides et sécurisées trouvera facilement un emploi n'importe où dans le monde.

Mais au-delà de l'aspect carrière, c'est intellectuellement fascinant. Vous allez comprendre comment des systèmes distribués communiquent, comment gérer l'authentification et la sécurité, comment scaler pour gérer des millions d'utilisateurs, comment architecturer des microservices. Ce sont des problèmes complexes avec des solutions élégantes.

Durant ces 9 jours, vous allez construire une vraie plateforme de microservices pour une ville intelligente. Ce n'est pas un exercice théorique dans le vide. C'est un projet concret que vous pourrez montrer dans votre portfolio, dont vous pourrez parler en entretien d'embauche, et qui vous donnera une expérience pratique précieuse.

Alors profitez de ces sessions. Posez des questions quand vous ne comprenez pas. Expérimentez, faites des erreurs, cassez des choses et réparez-les. C'est comme ça qu'on apprend vraiment. Les meilleurs développeurs que je connais ne sont pas ceux qui n'ont jamais fait d'erreurs, mais ceux qui ont fait énormément d'erreurs, en ont tiré des leçons, et sont devenus excellents grâce à ça.

Bon courage pour cet après-midi et pour les 8 jours à venir. Vous avez toutes les cartes en main pour réussir ce projet et créer quelque chose dont vous serez fiers.

Maintenant, place à la pratique ! 🚀

Fin du cours théorique Jour 1