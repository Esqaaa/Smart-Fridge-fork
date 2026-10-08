# Technical Documentation <a href="documentation_technique_FR"><img src="https://img.shields.io/badge/🌍%20Version%20française-blue?style=for-the-badge" alt="french version" align="right"></a>

## 1. Role and Scope
**Smart Fridge & Nutrition Coach** is a full-stack web application that enables:

- Management of a **metabolic profile** (BMR, TDEE, macros).
- Managing a **virtual fridge** synchronized with Supabase.
- Generating **recipe suggestions** via TheMealDB.
- Calculating **actual nutritional values** via the USDA API.
- Tracking meals in a **nutrition log**.
- A dynamic web interface without a bundler (Vanilla JS + custom CSS).

The scope covers the entire pipeline:
**User → Profile → Fridge → Suggestions → Recipes → Log → Dynamic Interface.**

## 2. Technical Prerequisites
### Environment
- Python **3.10+**
- Virtual environment:
  ```bash
  python -m venv venv
  pip install -r requirements.txt
  ```

### Required Knowledge
- FastAPI (routing, dependencies, JWT)
- Pydantic (strict validation)
- Supabase (PostgreSQL + Auth)
- Vanilla JS (fetch, DOM)
- Jinja2 (templates)

### External Services
- **USDA** API key
- Configured **Supabase** project
- Access to **TheMealDB**

### `.env` file
```env
SECRET_KEY=...
USDA_API_KEY=...
SUPABASE_URL=...
SUPABASE_KEY=...
```

## 3. Overall Functionality & Interactions

### Architecture
```
Smart-Fridge
├── app
│   ├── core          → security, JWT, settings
│   ├── routers       → FastAPI endpoints
│   ├── schemas       → Pydantic models
│   ├── services      → business logic (APIs, calculations)
│   ├── static        → JS + CSS
│   ├── templates     → Jinja2
│   ├── utils         → utilities
├── docs              → documentation
├── database.py       → Supabase connection
├── main.py           → FastAPI entry point
```

### Workflow
1. **Authentication** via JWT (Supabase manages users).
2. **Metabolic profile**: Pydantic validation + calculations (BMR/TDEE/macros).
3. **Virtual fridge**: add/remove ingredients → stored in Supabase.
4. **Suggestions**:
   - Query TheMealDB based on ingredients.
   - Standardize names.
   - Query the USDA for nutritional values.
   - Calculate a relevance score.
5. **Recipes**: detailed display via Jinja2.
6. **Journal**: add a meal → dynamic update via Vanilla JS.

### Client ↔ Server Interaction
- The frontend uses `fetch()` for all actions.
- The DOM is updated **without reloading the page**.
- The backend returns strictly typed JSON.

## 4. Real-world example (fridge → suggestion → recipe)

### 1) Adding an ingredient
```json
POST /fridge/add
{
  “name”: “tomato”
}
```

### 2) Requesting suggestions
```json
GET /recipes/suggestions
```

Response:
```json
{
  “recipes”: [
    {
      “name”: “Tomato Pasta”,
      “score”: 0.87,
      “ingredients_found”: 2
    }
  ]
}
```

### 3) Recipe Details
```json
GET /recipes/<id>
```

### 4) Add to Journal
```json
POST /journal/add
{
  “recipe_id”: 52771
}
```

### 5) Dynamic Update
The JavaScript updates the nutrition bars and the journal **without reloading the page**.

## 5. Settings & Files to Modify

### Configuration
- `.env`: API keys, JWT secret, Supabase URL.

### Backend
- `app/routers/`: add/modify endpoints.
- `app/schemas/`: modify Pydantic models (profile, recipes, USDA).
- `app/services/`:
  - suggestion engine,
  - ingredient normalization,
  - nutritional calculations.

### Frontend
- `app/static/js/`: DOM logic, fetch, `data-*` events.
- `app/static/css/`: responsive design, layout.
- `app/templates/`: HTML/Jinja2 structure.

### Database
- `database.py`: Supabase connection.
- Tables:
  - `profiles`
  - `fridge`
  - `journal`

## 6. Expected Result & Troubleshooting

### Expected Result
- Smooth and responsive interface.
- Fridge synchronized with Supabase.
- Consistent suggestions.
- Accurate nutritional calculations.
- Journal updated instantly.

### Common Issues

| Issue | Cause | Solution |
|---------|-------|----------|
| 401 Unauthorized | Missing token | Add `Authorization: Bearer <token>` |
| Slow performance | USDA or network | Retry, use a loader, verify API key |
| Frigo not updated | Supabase latency | Check `.env`, network |
| JS error | Incorrect DOM targeting | Check `data-*`, browser console |
| Inconsistent recipes | Imperfect normalization | Add a lexical mapping |

## 7. Limitations & Special Cases

### External APIs
- USDA latency (300–500 ms)
- Daily quotas
- Non-standardized TheMealDB data

### Nutritional calculations
- Rounding (<5%)
- Approximate quantities (tbsp, handful)
- Imperfect lexical matching

### Virtual Fridge
- Quantity not tracked (presence only)
- Normalization may be incorrect

### Metabolic Model
- Statistical approximations
- Simplified goals (weight loss/maintenance/gain)

### Deployment
- Currently runs on **localhost** only.

## 8. Code & Reference Documents

- **Source code**: `main.py`, `app/routers/`, `app/services/`, `app/schemas/`
- **General documentation**: `README.md`
- **Technical decisions**: `docs/ADR.md`
- **GitHub repository**: [https://github.com/loschoe/Smart-Fridge.git](https://github.com/loschoe/Smart-Fridge.git)