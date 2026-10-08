# ADR-001 : Choix de la stack technique <br>(FastAPI, Supabase, Vanilla JS & CSS)<a href="ADR_FR.md"><img src="https://img.shields.io/badge/🌍%20Version%20française-blue?style=for-the-badge" alt="Version Française" align="right" style="position:relative; top:4px;"></a>

- **Date:** October 8, 2026
- **Status:** Accepted

## Background

As part of the **Smart Fridge Coach** project (`Esqaaa/Smart-Fridge-fork`), the goal was to develop a smart nutritional coaching web application that would allow users to:

- Calculate and manage caloric and nutritional needs (proteins, carbohydrates, fats).
- Dynamically add and remove ingredients from a virtual fridge using tags and search functionality.
- Display recipe suggestions and provide access to their details.
- Daily meal tracking in three customizable slots, with progress bar calculations and visual feedback (“Goal Achieved”).
- Secure authentication and a responsive mobile display.

The technical challenge was to choose an architecture capable of supporting dynamic client-side updates without introducing the complexity of a JavaScript bundler or a heavy deployment process.

## Options

### Option 1: Dynamic FastAPI application + Supabase + Vanilla JS & CSS (Selected option)

- **Description:** FastAPI backend (Python) serving the API and Jinja2 templates, PostgreSQL database and authentication managed via Supabase, client-side DOM manipulation using Vanilla JS (`fetch` API), and responsive styles using custom CSS.
- **Advantages:**
  - No frontend compilation tools (no `npm`, `Vite`, or `Webpack`).
  - Strict end-to-end data validation using Pydantic models.
  - Instant load times and no heavy dependencies.
- **Limitations:**
  - Manual management of the DOM and local state tracking in native JavaScript is required.

### Option 2: Full server-side rendering (traditional HTML with form submissions)

- **Description:** Full server-side rendering where every interaction (adding an item to the fridge, adding a meal) triggers a full page reload.
- **Advantages:** Very simple mental model; very little JavaScript to write.
- **Limitations:**
  - Degraded user experience (systematic page reloads when adding ingredients or logging actions in the journal).
  - Lack of fluid responsiveness for dynamically updating food progress bars.

## Decision and Rationale

**Selected Option: Option 1 — FastAPI + Supabase + Custom Vanilla JS & CSS.**

### Rationale:

1. **Backend Performance & Typing:** FastAPI offers very low response times and strict request typing with Pydantic (e.g., passing `recipe_id` for log tracking) .
2. **BaaS Infrastructure (Supabase):** Securely centralizes management of the PostgreSQL database (`profiles`, `fridge`, `journal`) and JWT token-based authentication without the need to maintain an additional authentication server.
3. **Client-Side Responsiveness Without a Build**: The use of Vanilla JS combined with Fetch APIs allows for dynamic refreshing of macronutrient gauges, the fridge’s ingredient list, and the three meal slots without reloading the page.
4. **CSS Control & Mobile-First Design:** Custom CSS (Flexbox/Grid) enables fine-grained control over the responsive layout (dashboard and login/signup forms) without relying on heavy frameworks.

## Consequences

### Benefits:

- Extremely fast startup and execution (lightweight deliverables).
- Simplified architecture and deployment on a single FastAPI server instance.
- Smooth and responsive user interactions on the dashboard.

### Drawbacks / Limitations:

- Requires special attention to the organization of the JavaScript code (delegated event handling via `data-*` attributes to avoid syntax errors involving apostrophes or HTML injections).
- Changes to the database schema must be applied manually (e.g., adding the `recipe_id` column to the `journal` table).

## Reevaluation

The choice of architecture will be reevaluated if the project includes:

- **Social and collaborative features:** The ability to share one’s virtual fridge with family, comment on recipes, or follow other users’ nutrition logs in real time.
- **A mobile barcode scanner:** The ability to scan food items directly using the phone’s camera to stock the virtual fridge.
