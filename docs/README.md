# 🧊 Smart Fridge & Nutrition Coach <a href="../README.md"><img src="https://img.shields.io/badge/🌍%20Version%20Française-blue?style=for-the-badge" alt="Version Française" align="right" style="position:relative; top:4px;"></a>

Design and develop a full-stack web application for smart nutritional coaching.

## 📌 Project Presentation 
**Smart Fridge & Nutrition Coach** is a modern web application that allows users to:
- Create a secure account **(JWT)**
- Enter their physical profile: Weight, Height, Age, Gender, Physical Activity, Goal
- Manage the ingredients available in their fridge
- Receive recipe suggestions based on **TheMealDB**
- Calculate actual calories and macronutrients using the **USDA API**
- Generate a meal plan tailored to their metabolism

This project is being developed as part of the **Python & FastAPI** module (~35 hours).

## 📁 Project Tree Structure
```
Smart-Fridge
├── app
│   ├── core
│   ├── routers
│   ├── schemas
│   ├── services
│   ├── static
│   │   ├── css
│   │   └── js
│   ├── templates
│   ├── utils
│   └── __init__.py
├── docs 
│   ├── ADR.md
├── tests
├── config.py
├── database.py
├── json_store.py
├── main.py
└── venv/
```

## 🧩 Folder Descriptions
* `app/` Contains all of the application's code.
* `app/core/` Global settings: security, JWT management, Pydantic settings
* `app/routers/` All FastAPI endpoints: auth, profile, fridge, suggestions, recipes
* `app/schemas/` Pydantic Models: strict validation, structures for **TheMealDB**, structures for **USDA**, user profile, metabolic engine
* `app/services/` Business logic: API clients, suggestion engine, lexical mapping, nutritional calculations, error handling
* `app/static/` Static Files
* `app/templates/` Jinja2 Templates  
* `app/utils/` Utility Functions
* `docs/` Documentation 
* `config.py` Loading Environment Variables
* `database.py` Log in to Supabase
* `json_store.py` Local Storage of Ingredients
* `main.py` FastAPI entry point.

**venv/** Python Virtual Environment.

## ⚙️ Key Features
### 🔐 1. User Area & Security
- Registration & login via JWT
- Password hashing
- Protection of private routes (Depends)
- CORS properly configured

### 🧮 2. Metabolic Profile & Nutritional Calculations
- Strict Pydantic charts
- Mifflin-St. Jeor formula
- TDEE calculation based on activity level
- Goal management: weight loss, maintenance, weight gain
- Automatic macronutrient breakdown

### 🧊 3. Smart Fridge
- Add / remove ingredients
- Standardize names
- Sync with Supabase

### 🍽️ 4. Suggestion Engine
- **TheMealDB** searches by ingredient
- **USDA** searches for each ingredient
- Extraction of macronutrients via foodNutrients
- Recipe scoring

## 🧪 Prerequisites
- Python **3.10+**
- pip or pipx
- Internet access (external APIs)
- API keys
- Supabase

## 🐍 Using a Virtual Environment (venv)
We’ve decided to use virtual environments to make it easier to remove or control the downloading of dependencies during project development.
Here’s how to create this environment:
### Creation
```bash
python -m venv venv
```
### Activation
Windows:
```Windows PowerShell
venv\Scripts\Activate.ps1
```
Linux:
```bash
source venv/bin/activate
```
### Installing dependencies
```bash
pip install -r requirements.txt
```

## 🔧 Installation & Configuration
### 1. Clone the project
```bash
git clone https://github.com/loschoe/Smart-Fridge.git
cd Smart-Fridge
```
### 2. Create and activate the venv
*(See the previous section)*

### 3. Configure the `.env` file
```py
SECRET_KEY=...
USDA_API_KEY=...
SUPABASE_URL=...
SUPABASE_KEY=...
```

## 📦 Usage

### 1. Authentication
#### ➤ Registration
**POST** `/auth/register`
```json
{
  “email”: “user@example.com”,
  “password”: “mypassword”
}
```

#### ➤ Login
**POST** `/auth/login`
```json
{
  “email”: “user@example.com”,
  “password”: “mypassword”
}
```

Response:
```json
{
  “access_token”: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.. .",
  “token_type”: “bearer”
}
```

This token must be included in subsequent requests:
`Authorization: Bearer <token>`

### 2. Metabolic Profile

#### ➤ Set Up Your Profile
**POST** `/profile/`
```json
{
  “weight”: 72,
  “height”: 178,
  “age”: 24,
  “sex”: “male”,
  “activity”: “moderate”,
  “goal”: “maintain”
}
```

The API automatically calculates:
- **BMR** (Mifflin-St Jeor)
- **TDEE**
- **Macronutrient** breakdown based on your goal

### 3. Fridge Management

#### ➤ Add an Ingredient
**POST** `/fridge/add`
```json
{
  “name”: “tomato”
}
```

#### ➤ Delete an ingredient
**DELETE** `/fridge/remove/tomato`

#### ➤ View the contents of the fridge
**GET** `/fridge/`

Response:
```json
{
  “ingredients”: [“tomato”, ‘rice’, “chicken”]
}
```

### 4. Recipe Suggestions

#### ➤ Get suggestions based on the ingredients in the fridge
**GET** `/recipes/suggestions`

Response:
```json
{
  “recipes”: [
    {
      “name”: “Chicken Fried Rice”,
      “score”: 0.87,
      “ingredients_found”: 3
    }
  ]
}
```

The engine:
- Queries **TheMealDB**
- Standardizes the ingredients
- Queries **USDA** for nutritional values
- Calculates a **relevance score**

### 5. Recipe Details

#### ➤ View a recipe in detail
**GET** `/recipes/<id>`

Response:
```json
{
  “name”: “Tomato Pasta”,
  “ingredients”: [
    {“name”: “tomato”, ‘quantity’: “2”},
    {“name”: ‘pasta’, “quantity”: “200g”}
  ],
  “nutrition”: {
    “calories”: 420,
    “protein”: 12,
    “carbs”: 70,
    “fat”: 8
  },
  “instructions”: “Boil pasta, cook tomatoes...”
}
```

### 6. Web Interface

Once the server is running:
[http://127.0.0.1:8000](http://127.0.0.1:8000)

The interface allows you to:
- Log in / sign up
- Manage your profile
- Manage your fridge
- Get recipe suggestions
- View detailed information about dishes
- Navigate smoothly between sections

## 🚀 Starting the server
```bash
uvicorn app.main:app --reload
```

If no errors appear, you should see the following output in the console:
```uvicorn
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [11352] using WatchFiles
INFO:     Started server process [23152]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```
You can now connect to: http://127.0.0.1:8000

## 🧠 Architecture & Technical Choices
We opted for a simple and logical architecture in which each file has its own role and functionality, making it easier to navigate during development and debugging.
The choice of technologies was dictated by the project specifications.

## 🤖 Using Artificial Intelligence
As part of this project, we adopted a dynamic and responsible approach to using generative AI, taking great care to avoid “vibe coding.”
<br>AI served as a learning tool and targeted assistance, with each model deployed for specific roles to maximize our productivity while maintaining full control over our code:

- **GitHub Copilot** (Project Management): Used as a virtual project manager to structure our reverse schedule and organize the daily allocation of features.
- **ChatGPT & Gemini** (Dev Assistants): Deployed as development support to help us overcome logic challenges, explain concepts, and accelerate our learning.
- **Claude** (CSS & Optimization): Used for CSS implementation—because in 2026, no one builds complex designs entirely by hand anymore!—as well as for reviewing, improving, and optimizing our most critical algorithmic functions.

## 🧩 Démo
<div align="center">
<img width="522" height="495" alt="Page de connexion"exion via e-mail et mot de passe" src="https://github.com/user-attachments/assets/345e1b23-eb38-425e-839b-6a85be677baa" /></div>
- Validation des informations saisies </br>
- Gestion des erreurs de connexion </br>
- Accès sécurisé aux fonctionnalités de l'application </br> </br>
 
--> Une interface simple et intuitive conçue pour offrir une expérience utilisateur fluide dès l'arrivée sur l'application. </br> </br> </br>


<div align="center">
  <img width="1042" height="1412" alt="Le dashboard principal" src="https://github.com/user-attachments/assets/91ac1235-5f2f-43d6-aae8-97843871a0af" /></div>
- Overview of stored food items </br>
- Tracking expiration dates </br>
- Statistics and key metrics </br>
- Quick access to product management </br>
- Intuitive navigation to different sections </br> </br>
 
--> The dashboard was designed to provide a clear, instant overview of the refrigerator’s contents and to facilitate the day-to-day management of food inventory. </br> </br>

<div align="center">
  <img width="913" height="1103" alt="image" src="https://github.com/user-attachments/assets/bb2c4e72-8bac-466e-a8cc-806745c7500c" /></div>
- Detailed view of a recipe </br>
- List of ingredients with corresponding quantities </br>
- Step-by-step preparation instructions </br>
- Nutritional information (calories, protein, carbohydrates, fat) </br>
- Dish category and origin </br>
- Quick navigation to the dashboard </br> </br>

--> This view allows users to move directly from managing their ingredients to using them through recipes tailored to the ingredients they have on hand. </br>

## ⚠️ App Limitations

Despite its robust architecture, **Smart Fridge & Nutrition Coach** has several technical limitations related to external APIs, nutritional data, and the approximations required for calculations.

### 1. Limitations of External APIs (TheMealDB & USDA)
- **Variable latency**: Some requests may exceed 300–500 ms, especially on the USDA side.
- **Network dependency**: In the event of slow or unavailable network connections, suggestions and nutritional data become inaccessible.
- **Daily quotas**: The USDA limits the number of requests per day based on the API key.
- **Incomplete data**: TheMealDB provides non-standardized ingredients, and the USDA may omit certain nutrients.

### 2. Network Connection
-- **Localhost**: Application not deployed

### 3. Loss of Accuracy in Calculations
- **Mandatory rounding** (BMR, TDEE, macros) → slight loss of accuracy (<5%).
- **Approximate conversions**: Units such as *“1 tbsp”*, *“1 tsp”*, and *“1 handful”* do not correspond to exact nutritional quantities.
- **Missing quantities in TheMealDB**: Recipes do not specify grams → estimation required.

### 4. Limitations of the Suggestion Engine
- **Imperfect lexical matching**: “chicken,” “chicken breast,” “cooked chicken” → different USDA values.
- **Estimated portions**: In the absence of precise quantities, calories and macros are approximate.
- **Non-scientific relevance score**: consistent, but heavily dependent on the quality of external data.

### 5. Limitations related to the fridge and storage
- **Imperfect normalization**: complex or regional ingredients may be misinterpreted.
- **Supabase latency**: updates are sometimes slightly delayed (1–2 seconds).
- **Quantity**: Store the amount of food

## 6. Limitations of the metabolic model
- **Mifflin-St Jeor formula**: Reliable but remains a statistical approximation.
- **Simplified goals**: Weight loss / maintenance / weight gain → no advanced customization (medical conditions, atypical metabolism, etc.).

## 👥 License & Contributors
* **Free to access and learn from:** You are free to view the code, fork it, and suggest improvements.
* **Contributions welcome:** Pull requests are welcome to fix bugs, optimize the code, or propose new features. Any approved additions will be incorporated into the original project.
* **No ownership:** This project remains the property of its original authors. You must retain the original authors’ attribution in any copy or modification.
* **Commercial use strictly prohibited:** No reuse, resale, or commercial exploitation (direct or indirect) of the code or the project is permitted.

[Loschoe](https://github.com/loschoe) [Esqaaa](https://github.com/Esqaaa)