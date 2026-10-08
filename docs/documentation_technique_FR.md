# Documentation Technique <a href="documentation_technique_EN"><img src="https://img.shields.io/badge/🌍%20English%20Version-blue?style=for-the-badge" alt="English version" align="right"></a>

## 1. Rôle et périmètre
**Smart Fridge & Nutrition Coach** est une application web fullstack permettant :

- La gestion d’un **profil métabolique** (BMR, TDEE, macros).
- La gestion d’un **frigo virtuel** synchronisé avec Supabase.
- La génération de **suggestions de recettes** via TheMealDB.
- Le calcul des **valeurs nutritionnelles réelles** via l’API USDA.
- Le suivi des repas dans un **journal nutritionnel**.
- Une interface web dynamique sans bundler (Vanilla JS + CSS sur‑mesure).

Le périmètre couvre l’ensemble du pipeline :  
**Utilisateur → Profil → Frigo → Suggestions → Recettes → Journal → Interface dynamique.**

## 2. Prérequis techniques
### Environnement
- Python **3.10+**
- Environnement virtuel :
  ```bash
  python -m venv venv
  pip install -r requirements.txt
  ```

### Connaissances nécessaires
- FastAPI (routing, dépendances, JWT)
- Pydantic (validation stricte)
- Supabase (PostgreSQL + Auth)
- Vanilla JS (fetch, DOM)
- Jinja2 (templates)

### Services externes
- Clé API **USDA**
- Projet **Supabase** configuré
- Accès à **TheMealDB**

### Fichier `.env`
```env
SECRET_KEY=...
USDA_API_KEY=...
SUPABASE_URL=...
SUPABASE_KEY=...
```

## 3. Fonctionnement global & interactions

### Architecture
```
Smart-Fridge
├── app
│   ├── core          → sécurité, JWT, settings
│   ├── routers       → endpoints FastAPI
│   ├── schemas       → modèles Pydantic
│   ├── services      → logique métier (APIs, calculs)
│   ├── static        → JS + CSS
│   ├── templates     → Jinja2
│   ├── utils         → utilitaires
├── docs              → documentation
├── database.py       → connexion Supabase
├── main.py           → point d’entrée FastAPI
```

### Cycle de fonctionnement
1. **Authentification** via JWT (Supabase gère les utilisateurs).
2. **Profil métabolique** : validation Pydantic + calculs (BMR/TDEE/macros).
3. **Frigo virtuel** : ajout/suppression d’ingrédients → stockage Supabase.
4. **Suggestions** :
   - Interrogation TheMealDB selon les ingrédients.
   - Normalisation des noms.
   - Requêtes USDA pour les valeurs nutritionnelles.
   - Calcul d’un score de pertinence.
5. **Recettes** : affichage détaillé via Jinja2.
6. **Journal** : ajout d’un repas → mise à jour dynamique via Vanilla JS.

### Interaction client ↔ serveur
- Le frontend utilise `fetch()` pour toutes les actions.
- Le DOM est mis à jour **sans rechargement de page**.
- Le backend renvoie des JSON strictement typés.

## 4. Exemple concret (frigo → suggestion → recette)

### 1) Ajout d’un ingrédient
```json
POST /fridge/add
{
  "name": "tomato"
}
```

### 2) Demande de suggestions
```json
GET /recipes/suggestions
```

Réponse :
```json
{
  "recipes": [
    {
      "name": "Tomato Pasta",
      "score": 0.87,
      "ingredients_found": 2
    }
  ]
}
```

### 3) Détails de la recette
```json
GET /recipes/<id>
```

### 4) Ajout au journal
```json
POST /journal/add
{
  "recipe_id": 52771
}
```

### 5) Mise à jour dynamique
Le JS met à jour les jauges nutritionnelles et le journal **sans reload**.

## 5. Paramètres & fichiers à modifier

### Configuration
- `.env` : clés API, secret JWT, URL Supabase.

### Backend
- `app/routers/` : ajouter/modifier des endpoints.
- `app/schemas/` : modifier les modèles Pydantic (profil, recettes, USDA).
- `app/services/` :
  - moteur de suggestions,
  - normalisation des ingrédients,
  - calculs nutritionnels.

### Frontend
- `app/static/js/` : logique DOM, fetch, événements `data-*`.
- `app/static/css/` : responsive, layout.
- `app/templates/` : structure HTML/Jinja2.

### Base de données
- `database.py` : connexion Supabase.
- Tables :
  - `profiles`
  - `fridge`
  - `journal`

## 6. Résultat attendu & diagnostic

### Résultat attendu
- Interface fluide et réactive.
- Frigo synchronisé avec Supabase.
- Suggestions cohérentes.
- Calculs nutritionnels corrects.
- Journal mis à jour instantanément.

### Problèmes courants

| Problème | Cause | Solution |
|---------|-------|----------|
| 401 Unauthorized | Token manquant | Ajouter `Authorization: Bearer <token>` |
| Lenteur | USDA ou réseau | Retry, loader, vérification clé API |
| Frigo non mis à jour | Latence Supabase | Vérifier `.env`, réseau |
| Erreur JS | DOM mal ciblé | Vérifier `data-*`, console navigateur |
| Recettes incohérentes | Normalisation imparfaite | Ajouter un mapping lexical |

## 7. Limites & cas particuliers

### APIs externes
- Latence USDA (300–500 ms)
- Quotas journaliers
- Données TheMealDB non standardisées

### Calculs nutritionnels
- Arrondis (<5%)
- Quantités approximatives (tbsp, handful)
- Matching lexical imparfait

### Frigo virtuel
- Quantité non gérée (présence uniquement)
- Normalisation parfois incorrecte

### Modèle métabolique
- Approximations statistiques
- Objectifs simplifiés (perte/maintien/prise)

### Déploiement
- Fonctionne en **localhost** uniquement pour l’instant.

## 8. Code & documents de référence

- **Code source** : `main.py`, `app/routers/`, `app/services/`, `app/schemas/`
- **Documentation générale** : `README.md`
- **Choix techniques** : `docs/ADR.md`
- **Dépôt GitHub** : [https://github.com/loschoe/Smart-Fridge.git](https://github.com/loschoe/Smart-Fridge.git)