# ADR-001 : Choix de la stack technique <br>(FastAPI, Supabase, Vanilla JS & CSS) <a href="ADR_EN.md"><img src="https://img.shields.io/badge/🌍%20English%20Version-blue?style=for-the-badge" alt="English version" align="right" style="position:relative; top:4px;"></a>
- **Date :** 8 octobre 2026
- **Statut :** Accepté

## Contexte

Dans le cadre du projet **Smart Fridge Coach** (`Esqaaa/Smart-Fridge-fork`), l'objectif était de développer une application web de coaching nutritionnel intelligent permettant :

- Le calcul et la gestion des besoins caloriques/aliments (protéines, glucides, lipides).
- L'ajout et la suppression dynamique d'ingrédients dans un frigo virtuel sous forme de tags et de recherche.
- L'affichage de suggestions de recettes et l'accès à leurs détails.
- Le suivi quotidien des repas dans 3 emplacements libres avec calcul des jauges de progression et retour visuel ("Objectif atteint").
- Une authentification sécurisée et un affichage responsive sur mobile.

Le défi technique consistait à choisir une architecture capable d'assurer des mises à jour dynamiques côté client, sans introduire la complexité d'un bundler JS ou d'un déploiement lourd.

## Options

### Option 1 : Application dynamique FastAPI + Supabase + Vanilla JS & CSS sur-mesure (Option retenue)

- **Description :** Backend FastAPI (Python) servant l'API et les templates Jinja2, base de données PostgreSQL et authentification gérées via Supabase, manipulation du DOM côté client en Vanilla JS (`fetch` API), et styles responsifs en CSS sur-mesure.
- **Avantages :**
  - Aucun outil de compilation frontend (pas de `npm`, `Vite` ou `Webpack`).
  - Validation stricte des données de bout en bout grâce aux modèles Pydantic.
  - Temps de chargement instantané et absence de dépendances lourdes.
- **Limites :**
  - Gestion manuelle du DOM et du suivi d'état local en JavaScript nativement requise.

### Option 2 : Rendu full-serveur (HTML classique avec soumissions de formulaires)

- **Description :** Rendu serveur intégral où chaque interaction (ajout au frigo, ajout d'un repas) entraîne le rechargement complet de la page.
- **Avantages :** Modèle mental très simple, très peu de JavaScript à écrire.
- **Limites :**
  - Expérience utilisateur dégradée (rechargement de page systématique lors de l'ajout d'ingrédients ou d'actions dans le journal).
  - Absence de réactivité fluide pour la mise à jour dynamique des barres de progression des aliments.

## Décision et justification

**Décision retenue : Option 1 — FastAPI + Supabase + Vanilla JS & CSS sur-mesure.**

### Justification :

1. **Performance & Typage Backend :** FastAPI offre des temps de réponse très bas et un typage strict des requêtes avec Pydantic (ex: passage de `recipe_id` pour le suivi du journal).
2. **Infrastructure BaaS (Supabase) :** Centralise de manière sécurisée la gestion de la base de données PostgreSQL (`profiles`, `fridge`, `journal`) et l'authentification par jeton JWT sans serveur d'authentification supplémentaire à maintenir.
3. **Réactivité Client Sans Build :** Le choix de Vanilla JS couplé aux Fetch APIs permet de rafraîchir dynamiquement les jauges de macronutriments, la liste d'ingrédients du frigo et les 3 slots de repas sans recharger la page.
4. **Contrôle du CSS & Mobile First :** Le CSS sur-mesure (Flexbox/Grid) permet un contrôle fin de la mise en page responsive (dashboard et formulaires de login/signup) sans dépendance vers des frameworks lourds.

## Conséquences

### Bénéfices :

- Démarrage et exécution extrêmement rapides (livrables légers).
- Architecture et déploiement simplifiés sur une seule instance serveur FastAPI.
- Interactions fluides et réactives pour l'utilisateur sur le dashboard.

### Inconvénients / Contraintes :

- Nécessite une attention particulière sur l'organisation du code JavaScript (gestion déléguée des événements via attributs `data-*` pour éviter les erreurs de syntaxe sur les apostrophes ou injections HTML).
- Modifications du schéma de base de données à reporter manuellement (ex: ajout de la colonne `recipe_id` sur la table `journal`).

## Réexamen

Le choix de l'architecture sera réévalué si le projet intègre :

- **Des fonctionnalités sociales et collaboratives :** La possibilité de partager son frigo en famille, de commenter des recettes ou de suivre les journaux nutritionnels d'autres utilisateurs en temps réel.
- **Un scanner de code-barres mobile :** La possibilité de scanner les produits alimentaires directement via l'appareil photo du téléphone pour remplir le frigo virtuel.
