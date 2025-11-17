Quiz - Sécurité des Web Services (Jour 4 & 5)
Questions sur l'Authentification
Question 1 : Quelle est la principale faiblesse de Basic Authentication ?
<details>
<summary>Voir la réponse</summary>
Les credentials sont encodés en Base64 (pas du chiffrement !). N'importe qui peut les décoder instantanément. De plus, ils sont envoyés à chaque requête, multipliant les risques d'interception. HTTPS est obligatoire, mais cette méthode reste déconseillée en production.
</details>

Question 2 : Quelle est la différence principale entre API Keys et Basic Authentication en termes de révocabilité ?
<details>
<summary>Voir la réponse</summary>
API Keys :

Révocables instantanément (supprimer en DB)
Révocation granulaire (une clé sans affecter les autres)
Traçables (savoir quelle app a été révoquée)
Sans impact utilisateur

Basic Authentication :

Révocation = changer le mot de passe
Coupe TOUS les accès simultanément
L'utilisateur doit se reconnecter partout
Pas de traçabilité

Conclusion : Les API Keys offrent une bien meilleure révocabilité.
</details>

Question 3 : Expliquez la structure d'un JWT (JSON Web Token) et ses trois parties.
<details>
<summary>Voir la réponse</summary>
Un JWT a 3 parties séparées par des points :

Header : Algorithme de signature (HS256, RS256) et type (JWT)
Payload : Données utilisateur (ID, email, rôle, expiration)
Signature : HMACSHA256(header + payload + secret) - garantit l'intégrité

⚠️ Point critique : Le payload est en Base64, pas chiffré. Jamais de données sensibles dedans !
</details>

Question 4 : Expliquez le cycle complet d'utilisation des Refresh Tokens avec les JWT.
<details>
<summary>Voir la réponse</summary>
Le cycle en 7 étapes :

Login : Email + password → Access Token (15min) + Refresh Token (7j)
Utilisation : Client envoie Access Token à chaque requête
Expiration : Access Token expire après 15min → erreur 401
Renouvellement : Client envoie Refresh Token → nouveau Access Token
Répétition : Cycle 2-4 pendant 7 jours
Expiration finale : Refresh Token expire → re-login obligatoire
Logout : Refresh Token révoqué en DB

Avantages : Sécurité (token court) + UX (pas de reconnexion fréquente) + Révocation possible
</details>

Question 5 : Quelles sont les 5 règles d'or pour sécuriser un JWT ?
<details>
<summary>Voir la réponse</summary>

Jamais de données sensibles dans le payload (visible en Base64)
Secret fort (256 bits minimum, en variable d'environnement)
Liste blanche des algorithmes (algorithms: ['HS256'] contre attaque "none")
Toujours une expiration (15min-1h pour access token)
Issuer et Audience (vérifier qui a créé le token et pour qui)

</details>

Question 6 : Quelle est la différence entre OAuth 2.0 et les autres méthodes d'authentification ?
<details>
<summary>Voir la réponse</summary>
OAuth 2.0 N'EST PAS une authentification, c'est de la délégation d'autorisation.

Authentification (Basic, JWT) : Prouve qui vous êtes
OAuth 2.0 : Permet à une app tierce d'accéder à vos ressources sans votre mot de passe

Exemple : EventFinder veut vos événements SmartCity → Vous autorisez via OAuth, sans donner votre password.
Les 4 acteurs :

Resource Owner (vous)
Client (EventFinder)
Authorization Server (SmartCity OAuth)
Resource Server (SmartCity API)

</details>

Question 7 : Expliquez les différents flows OAuth 2.0 et leurs cas d'usage.
<details>
<summary>Voir la réponse</summary>
1. Authorization Code (le plus sécurisé)

Pour : Apps web avec backend
Principe : Code temporaire échangé contre token (avec client_secret)
Le token n'est jamais exposé au navigateur

2. Client Credentials (Machine-to-Machine)

Pour : Communication entre services backend
Principe : Service s'authentifie avec client_id + client_secret
Pas d'utilisateur impliqué

3. Authorization Code + PKCE (apps publiques)

Pour : Apps mobiles, SPAs
Problème : Pas de client_secret sécurisable
Solution : code_verifier + code_challenge (SHA256)

Comment choisir :

Backend disponible → Authorization Code
Services backend → Client Credentials
Mobile/SPA → PKCE

</details>

Questions sur l'Autorisation
Question 8 : Expliquez les 5 niveaux de vérification d'autorisation sur une ressource.
<details>
<summary>Voir la réponse</summary>
Exemple : DELETE /api/events/:id
Niveau 1 - Authentification : Qui fait la requête ? (middleware JWT)
Niveau 2 - Existence : La ressource existe ? (404 si non)
Niveau 3 - Autorisation multi-critères :

Admin → permission 'manage:all' → Autorisé
Moderator → permission 'delete:any_events' → Autorisé
Organizer → permission 'delete:own_events' + ownership + événement futur + moins de 50 inscriptions → Autorisé si tout OK

Niveau 4 - Logging : Tracer qui a fait quoi et quand
Niveau 5 - Exécution : Seulement si tout passe
Erreurs à éviter :

❌ Vérifier seulement l'authentification
❌ Vérifier seulement le rôle
✅ Combiner rôle + ownership + règles métier

</details>

Question 9 : Expliquez le fonctionnement du RBAC (Role-Based Access Control).
<details>
<summary>Voir la réponse</summary>
RBAC = Permissions attachées à des rôles
Exemple SmartCity :

Citizen : read:events, create:registration
Organizer : create:events, update:own_events, delete:own_events
Moderator : update:any_events, delete:any_events, suspend:users
Admin : manage:all (tout)

Avantages : Simple, facile à auditer, bon pour 80% des apps
Inconvénients : Rigide avec beaucoup de cas particuliers
Quand utiliser : Rôles stables et bien définis
</details>

Question 10 : Qu'est-ce que l'ABAC et quand l'utiliser plutôt que le RBAC ?
<details>
<summary>Voir la réponse</summary>
ABAC = Décisions basées sur des attributs
4 types d'attributs :

Utilisateur : rôle, département, ancienneté
Ressource : type, statut, propriétaire, date
Action : read, write, delete
Contexte : heure, localisation, appareil

Exemple de règle : "Supprimer un événement SI : propriétaire + futur + moins de 50 inscriptions + entre 9h-18h"
Quand utiliser :

RBAC : Rôles simples et stables (80% des cas)
ABAC : Logiques métier complexes, règles contextuelles

</details>

Question 11 : Pourquoi combiner rôles et scopes OAuth ?
<details>
<summary>Voir la réponse</summary>
Double protection = Sécurité renforcée
Avantages côté SERVEUR :

Deux barrières (scope + rôle)
Révocation automatique si rôle change
Protection contre erreurs

Avantages côté CLIENT :

Connaissance claire des capacités
Adaptation de l'UI (afficher/cacher fonctionnalités)
Gestion d'erreur proactive
Meilleure expérience développeur

Exemple :

Token avec scope read:events mais user citizen → Autorisé
Token avec scope delete:events mais user citizen → Refusé (rôle insuffisant)

Résumé : Le scope dit ce que l'app peut demander, le rôle dit ce que l'user peut vraiment faire.
</details>

Questions sur HTTPS et Sécurité
Question 12 : Pourquoi HTTPS est-il obligatoire pour toute API en production ?
<details>
<summary>Voir la réponse</summary>
Sans HTTPS, un attaquant intercepte :

Passwords et tokens
Données personnelles
Contenu des requêtes/réponses

Conséquences :

Navigateurs marquent "Non sécurisé"
Impact SEO négatif
Non-conformité RGPD/PCI-DSS
Perte de confiance utilisateur

2024 : 95%+ du web en HTTPS. C'est un prérequis absolu.
</details>

Question 13 : Expliquez les 3 objectifs de TLS.
<details>
<summary>Voir la réponse</summary>
1. CONFIDENTIALITÉ

Chiffrement AES avec clé de session unique
Contenu illisible sans la clé

2. INTÉGRITÉ

Hash (HMAC) de chaque message
Toute modification détectée → message rejeté

3. AUTHENTIFICATION

Certificat SSL/TLS signé par une CA
Prouve l'identité du serveur
Client vérifie : CA confiance + domaine + expiration

Le Handshake : Client Hello → Server Hello + Certificat → Vérification → Échange de clés → Communication chiffrée
</details>

Question 14 : Quels sont les 3 types de certificats SSL/TLS ?
<details>
<summary>Voir la réponse</summary>
1. DV (Domain Validation)

Vérifie : contrôle du domaine (challenge DNS/HTTP)
Durée : quelques minutes
Coût : gratuit (Let's Encrypt)
Pour : 95% des sites web

2. OV (Organization Validation)

Vérifie : domaine + existence entreprise
Durée : 1-3 jours
Coût : 50-200€/an
Pour : sites corporate

3. EV (Extended Validation)

Vérifie : audit complet de l'organisation
Durée : 1-2 semaines
Coût : 300-1500€/an
Pour : banques, fintech

Recommandation SmartCity : Let's Encrypt (DV gratuit et automatique)
</details>

Question 15 : Décrivez 5 attaques courantes et leurs protections.
<details>
<summary>Voir la réponse</summary>
1. MITM (Man-in-the-Middle)

Attaque : Interception du trafic
Protection : HTTPS + HSTS + Certificate Pinning

2. SQL Injection

Attaque : Injection de code SQL malveillant
Protection : ORM + Requêtes paramétrées + Validation

3. XSS (Cross-Site Scripting)

Attaque : Injection de scripts JavaScript
Protection : CSP + Sanitization + Escaping + httpOnly cookies

4. CSRF (Cross-Site Request Forgery)

Attaque : Actions non désirées avec session active
Protection : sameSite cookies + CSRF tokens

5. Brute Force

Attaque : Essais massifs de passwords
Protection : Rate limiting + Blocage progressif + bcrypt + CAPTCHA

</details>

Question 16 : Comment protéger une API contre les attaques brute force ?
<details>
<summary>Voir la réponse</summary>
Protection multi-niveaux :

Rate limiting : 5 tentatives max / 15min
Blocage progressif : 5 échecs = 15min, 10 échecs = 1h
CAPTCHA : Après 3 échecs
Slow hash (bcrypt) :

rounds = 12 → 2^12 = 4096 itérations
~250ms par hash
Rend le brute force très coûteux en temps


Monitoring : Logger + alertes sur patterns suspects
MFA/2FA : Pour comptes admin

Résultat : Brute force devient impraticable (années nécessaires)
</details>

Question 17 : Pourquoi ne jamais stocker les JWT dans localStorage ?
<details>
<summary>Voir la réponse</summary>
Le problème : localStorage accessible via JavaScript → vulnérable XSS
Scénario d'attaque :

Attaquant injecte script XSS
Script lit localStorage.getItem('token')
Token envoyé au serveur attaquant
Usurpation d'identité

La solution : Cookie httpOnly

Pas accessible en JavaScript
Protection XSS automatique
Protection CSRF avec sameSite
Gestion automatique par navigateur

</details>

Question 18 : Qu'est-ce que le principe du moindre privilège ?
<details>
<summary>Voir la réponse</summary>
Principe : Donner uniquement les permissions nécessaires, rien de plus.
Application :

Utilisateurs : Accès minimal (citizen < organizer < moderator < admin)
API Keys : Scopes granulaires par clé
OAuth : Demander seulement les scopes nécessaires
Base de données : Compte avec permissions minimales
Systèmes : Containers sans root

Bénéfice : Limiter les dégâts en cas de compromission
</details>

Question 19 : Expliquez la défense en profondeur (Defense in Depth).
<details>
<summary>Voir la réponse</summary>
Principe : Plusieurs couches de sécurité. Si une échoue, les autres protègent.
Les 7 couches (exemple DELETE /api/events/:id) :

HTTPS/TLS → Protège le transport
WAF/Rate Limiting → Filtre trafic malveillant
Authentication (JWT) → Vérifie l'identité
Authorization (RBAC) → Vérifie les permissions
Validation/Sanitization → Nettoie les inputs
Application Logic → Traite la requête
Logging/Monitoring → Trace et alerte

Résultat : Une faille sur une couche ne compromet pas tout le système
</details>

Question 20 : Créez une checklist de déploiement sécurisé pour une API.
<details>
<summary>Voir la réponse</summary>
Transport :

☐ HTTPS activé (Let's Encrypt)
☐ TLS 1.2+ uniquement
☐ Redirect HTTP → HTTPS (redirection automatique avec code 301)
☐ HSTS activé (force HTTPS pendant X temps)

Headers :

☐ Helmet.js (configure automatiquement tous les headers de sécurité)
☐ CSP (limite les sources de scripts/CSS/images)
☐ X-Frame-Options: DENY (anti-clickjacking)
☐ X-Content-Type-Options: nosniff (anti-MIME sniffing)

Auth :

☐ Passwords hashés (bcrypt rounds >= 12)
☐ JWT expiration courte (15min-1h)
☐ Refresh tokens en DB
☐ Tokens en cookies httpOnly
☐ Rate limiting sur /login (5 max)

Data :

☐ Validation inputs stricte
☐ ORM ou requêtes paramétrées
☐ Pas de données sensibles dans logs/JWT

Monitoring :

☐ Logs centralisés
☐ Alertes sur activités suspectes

Tests :

☐ npm audit dans CI/CD
☐ OWASP Top 10 vérifié

Secrets :

☐ Variables d'environnement
☐ .env dans .gitignore

</details>

