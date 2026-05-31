# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Maa Inti Vantalu** (మా ఇంటి వంటలు — "Our Home Cooking") is a personal family recipe catalog — a mobile-first single-page web app for browsing and searching Telugu/South Indian home recipes from Dharani's Kitchen. No frameworks, no build tools, no dependencies.

## Running the App

Open `index.html` directly in a browser, or serve the folder with any static file server (e.g. `npx serve .` or Python `http.server`). There is no build step, no package manager, and no tests.

## Architecture

The entire app lives in two files:

- **`index.html`** — Single HTML file containing all CSS (`<style>`) and JavaScript (`<script>`). No external JS or CSS files; only a Google Fonts CDN link.
- **`recipes.json`** — The sole data source. An object with a `recipes` array, loaded at runtime via `fetch()`.

### Data Flow

`loadRecipes()` fetches `recipes.json` → stores in `RECIPES` global array → `render()` is called → `filterRecipes()` applies active filters → DOM is rebuilt as HTML strings injected via `innerHTML`.

### Key Concepts in index.html

- **Recipe schema** (in `recipes.json`): `{ id, name, category, time, preference, keyIngredients[] }`
  - `time`: `"quick"` | `"medium"` | `"time-consuming"`
  - `preference`: `"both"` | `"dharani"` | `"shashi"`
  - `category`: maps to a meal-type group via `MEAL_TYPES`
- **`MEAL_TYPES`** object — defines how individual categories (e.g. `"Tiffin"`, `"Curry"`) are grouped into meal-type sections (e.g. `"Tiffins"`, `"Veg Entree"`). Each key is a display group name; each value is an array of category strings that belong to it.
- **`ICONS`** object — maps category names to emoji icons for card display.
- **Filter system** — three independent filter dimensions (`activeTime`, `activePref`, `activeCat`) plus a text `searchQuery`. All are combined in `filterRecipes()`.
- **Collapsible panels** — recipes are grouped into `<div class="panel">` sections. When no meal type filter is active, they group by `MEAL_TYPES`. When a meal type is selected, they group by cooking time. Panels and cards use CSS `max-height` transitions for expand/collapse.
- **Rendering** — the entire recipe list is re-rendered on every filter/search change by rebuilding HTML strings and setting `innerHTML`. No virtual DOM or templating library.

## Editing Recipes

Add new recipes to the `recipes` array in `recipes.json`. Each recipe needs a unique numeric `id`. Follow the existing schema exactly. The recipe count badge in the header updates automatically.

When adding a new `category` value, also update:
1. The `ICONS` object (emoji mapping)
2. The `MEAL_TYPES` object (meal-type grouping) — or it will fall into the catch-all "Other" section
