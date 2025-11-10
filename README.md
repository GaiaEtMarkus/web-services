# Formation Web Services — Documentation projet

## 📚 Aperçu

Ce dépôt regroupe les supports pédagogiques de la formation "Web Services" :
- Une page d’accueil (`index.html`) présentant la promotion, le programme et les métriques de la formation.
- Des pages dédiées par journée (`pages/dayX*.html`) couvrant théorie, fil rouge et ressources.
- Des feuilles de style et scripts communs assurant une expérience cohérente sur l’ensemble du site.

Le contenu met l’accent sur la montée en compétences progressive : architecture REST, fil rouge SmartCity, et désormais un focus avancé sur SOA/SOAP (Jour 6).

## 🗂 Structure principale

```
MicroServices/
├── index.html
├── pages/
│   ├── day1.html / day1FilRouge.html
│   ├── day2.html / day2FilRouge.html
│   ├── day3.html / day3FilRouge*.html
│   ├── day4.html
│   └── day5.html
├── styles/
│   ├── styles.css (global)
│   └── dayX*.css (théorie & fil rouge)
├── script.js (navigation, animations, counters)
└── cours/ (sources Markdown des supports)
```

## ✅ Fonctionnalités en place

- **Navigation unifiée** : barre de navigation cohérente sur toutes les pages avec retour vers l’accueil et les ancres principales.
- **Highlighting du code** : intégration de `highlight.js` avec balises `<pre><code>` uniformisées pour chaque exemple de code.
- **Pages par journée** :
  - Jour 1 à 3 : théorie + fil rouge (SmartCity) entièrement détaillés.
  - Jour 4 : sécurité des Web Services avec sections repliables, comparatifs, schémas et bonnes pratiques (nouvelle sous-partie 1.12 dédiée à la révocabilité des méthodes d’authentification).
  - Jour 6 : architecture SOA/SOAP avec plan complet (Parties 1 à 4) et synthèse finale.
- **Sections repliables** : cards interactives (Parties 1 à 4) pour alléger la lecture sur les longues pages.
- **Ressources pédagogiques** : contenu Markdown converti en HTML, synthèse & ressources intégrées pour Jour 6.
- **Design responsive** : grilles, cards et tableaux adaptés aux différents formats.

## 🔜 To-do / Prochaines étapes

- Ajouter les pages restantes du cursus (Jour 5 théorie/fil rouge, Jour 6 Fil Rouge si prévu, Jour 7 selon le programme initial).
- Compléter un éventuel `day6FilRouge.html` pour aligner théorie et pratique.
- Centraliser la gestion des toggles (facteur commun pour éviter la duplication des fonctions `togglePartieX`).
- Vérifier l’accessibilité (contrastes, aria-labels sur les éléments interactifs).
- Mettre en place une section “Changelog” ou “Journal de bord” si demandée par la pédagogie.
- Ajouter des scripts/tests automatisés (lint HTML/CSS, validation de liens) si le projet doit évoluer.

## 🚀 Démarrer le projet en local

1. Cloner le dépôt sur votre machine.
2. Ouvrir `index.html` ou l’une des pages du dossier `pages/` dans votre navigateur.
3. Pour un rendu optimal, servir les fichiers via un petit serveur local (ex. `npx serve` ou `python -m http.server`).

## 🤝 Contribuer

- Respecter la convention actuelle : contenu en français, commentaires minimalistes en anglais si nécessaire.
- Garder les mises à jour par sections (une page à la fois) et tester visuellement après chaque bloc.
- Mettre à jour ce README à chaque ajout de fonctionnalités majeures ou changement de roadmap.

Bonne lecture et bonne formation !
