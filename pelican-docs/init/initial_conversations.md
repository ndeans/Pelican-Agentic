# Pelican
— Initial Conversations & Project Context

**Created:** 2026-05-31  
**Source:** Claude Code session @ Tornado — initial project scoping

---

## 1. The Problem Being Solved

The user manages a 5-week meal planning cycle manually. The process involves:

1. **Schedule generation** — assigning cook days and leftover days to a calendar grid
2. **Recipe selection** — choosing 10 batch-cook meals (5 two-day, 5 three-day) plus ad-hoc "whatever" meals
3. **Ingredient matrix** — cross-referencing ~80+ ingredients against 12 recipes with specific quantities
4. **Shopping list consolidation** — aggregating ingredients, merging duplicates, grouping by category/aisle

Steps 3 and 4 are the primary pain points — currently done entirely by hand in a spreadsheet.

---

## 2. Weekly Cooking Pattern (e/o/a System)

| Slot | Label    | When     | Batch Size | Covers Through |
|------|----------|----------|------------|----------------|
| e    | either   | Saturday | 3-day meal | Monday         |
| o    | or       | Sunday   | 2-day meal | Monday         |
| a    | always   | Tuesday  | 3-day meal | Thursday       |

- **e or o** — pick one each week for the weekend cook
- **a** — Tuesday is always a cook day (typically 3-day, sometimes 2-day)
- **Fridays and non-cook Saturdays** are "whatever" days (simple/ad-hoc meals)

### Day type totals across a 34-day (5-week) cycle:
- 5 two-day meals
- 5 three-day meals
- 15 leftover days
- 9 "whatever" days

---

## 3. Sample Meal Plan (5/23 – 6/25)

| Day | Date  | DOW  | Type       | Meal                         |
|-----|-------|------|------------|------------------------------|
| 1   | 5/23  | SAT  | whatever   |                              |
| 2   | 5/24  | SUN  | 2-day meal | Chicken Curry                |
| 3   | 5/25  | MON  | leftovers  |                              |
| 4   | 5/26  | TUE  | 3-day meal | Chicken Marsala              |
| 5   | 5/27  | WED  | leftovers  | "                            |
| 6   | 5/28  | THU  | leftovers  | "                            |
| 7   | 5/29  | FRI  | whatever   | Tuna Melt                    |
| 8   | 5/30  | SAT  | whatever   |                              |
| 9   | 5/31  | SUN  | 2-day meal | Teriyaki Beef                |
| 10  | 6/1   | MON  | leftovers  | "                            |
| 11  | 6/2   | TUE  | 3-day meal | Kung Pao Chicken             |
| 12  | 6/3   | WED  | leftovers  | "                            |
| 13  | 6/4   | THU  | leftovers  | "                            |
| 14  | 6/5   | FRI  | whatever   |                              |
| 15  | 6/6   | SAT  | 3-day meal | Shepherd's Pie               |
| 16  | 6/7   | SUN  | leftover   | "                            |
| 17  | 6/8   | MON  | leftover   | "                            |
| 18  | 6/9   | TUE  | 2-day meal | Ham Gammon                   |
| 19  | 6/10  | WED  | leftover   | "                            |
| 20  | 6/11  | THU  | whatever   |                              |
| 21  | 6/12  | FRI  | whatever   |                              |
| 22  | 6/13  | SAT  | 3-day meal | Shrimp Creole                |
| 23  | 6/14  | SUN  | leftover   | "                            |
| 24  | 6/15  | MON  | leftover   | "                            |
| 25  | 6/16  | TUE  | 3-day meal | Orange Chicken               |
| 26  | 6/17  | WED  | leftover   | "                            |
| 27  | 6/18  | THU  | leftover   | "                            |
| 28  | 6/19  | FRI  | whatever   | Pad Thai (Chicken or Shrimp) |
| 29  | 6/20  | SAT  | whatever   |                              |
| 30  | 6/21  | SUN  | 2-day meal | Chili                        |
| 31  | 6/22  | MON  | leftover   | "                            |
| 32  | 6/23  | TUE  | 2-day meal | Szechuan Beef                |
| 33  | 6/24  | WED  | leftover   | "                            |
| 34  | 6/25  | THU  | whatever   |                              |

---

## 4. Recipe Column Naming Convention

The manually-built spreadsheet used a structured column code:

`{number}-{typeCode}{CountryCode} ({abbreviation})`

Examples:
- `22-crAI` (c. curry) — Chicken Curry, Indian
- `2-cpIT` (c. marsala) — Chicken Marsala, Italian
- `14-tpbUS` (t. melt) — Tuna Melt, American
- `8-brJP` (b. teriyaki) — Beef Teriyaki, Japanese
- `3-crCN` (kung pao c.) — Kung Pao Chicken, Chinese
- `13-bpUK` (s. pie) — Shepherd's Pie, British
- `21-hpUS` (h. gammon) — Ham Gammon, American
- `5-srUS` (s. creole) — Shrimp Creole, American
- `1-crCN` (orange c.) — Orange Chicken, Chinese
- `6-bcbUS` (b. chili) — Beef Chili, American
- `10-brCN` (szechuan b.) — Szechuan Beef, Chinese
- `16-cnTH` (pad thai) — Pad Thai, Thai

---

## 5. Ingredient Matrix Summary

The manually-built ingredient matrix contains:
- **~80+ distinct ingredients** as rows
- **12 recipe columns** (10 batch-cook + 2 named "whatever" meals)
- **Quantity values** in each cell (e.g., "3 lbs.", "1/4 cup", "2 cloves", "garnish", "x")
- **Color-coded ingredient categories** (proteins, produce, pantry staples, spices, canned goods, etc.)

This matrix is the core artifact that needs to be auto-generated.

---

## 6. Project Architecture — Pelican

### Overview

**Codename:** Pelican  
**Purpose:** Automate meal planning from recipe ingestion through schedule generation, matrix building, and shopping list output.

**Sister project:** Raven (JSF/Wildfly — a working model of a 2024-era enterprise Java stack)

### Two Modules

#### Pelican Ingest (Python CLI)
- CLI scripts to parse and normalize recipes into a canonical data store
- Input: typed-in recipes, pasted ingredient lists, potentially scraped URLs later
- Output: structured recipe data in MariaDB with normalized ingredients, quantities, units, categories, cuisine tags, batch-type metadata
- Low ceremony — just get data in clean

#### Pelican Planner (Svelte Web App)
- Visual, interactive planning experience
- Drag-and-drop meals onto a calendar grid following e/o/a pattern rules
- Auto-generates the ingredient x recipe matrix
- Consolidates shopping lists by week and by aisle/category
- **Aesthetic:** avant-garde ambience, clean and snappy — bold typography, fluid animations, glass/gradient touches, no clutter
- Locally hosted

### Tech Stack

| Layer        | Choice                       | Notes                                      |
|--------------|------------------------------|---------------------------------------------|
| UI Framework | SvelteKit                    | Recommended in prior conversation           |
| API Layer    | Thin REST (TBD framework)    | Future-proofing; CRUD over recipe/plan data |
| Database     | MariaDB (existing instance)  | Already running on dev box, shared with Raven |
| Ingest       | Python CLI scripts           | Low-ceremony data entry                     |
| Hosting      | Local                        | Dev box                                     |

### Tech Stack Contrast with Raven

| Aspect     | Raven (2024 era)    | Pelican (2026 edge)     |
|------------|---------------------|-------------------------|
| UI         | JSF                 | SvelteKit               |
| Server     | Wildfly / Java EE   | Node / Vite (local)     |
| Data       | MariaDB (enterprise)| MariaDB (shared instance)|
| Philosophy | Enterprise patterns | Modern, minimal, fast   |

---

## 7. Key Design Decisions

### MariaDB over SQLite
- **Concurrent access** — CLI ingest + web planner can hit the DB simultaneously without locking issues
- **Zero setup** — instance already running
- **Familiar tooling** — user already knows admin, backup, and migration patterns
- **API alignment** — standard DB-backed service pattern fits the thin API layer naturally

### Thin API Layer
- Provides clean contract between ingest and planner modules
- Future-proofs for mobile views or network sharing
- Starting razor-thin — essentially CRUD endpoints over recipe/ingredient/plan data

---

## 8. Data Flow

```
Recipes (text/CLI) → Python Ingest Scripts → MariaDB → Thin API → Svelte Planner UI → Matrix + Shopping Lists
```

---

## 9. Next Steps (TBD)

- [ ] Database schema design (recipes, ingredients, meal plans, schedule patterns)
- [ ] Python CLI ingest scaffolding
- [ ] API layer framework selection and scaffolding
- [ ] SvelteKit project scaffolding
- [ ] UI design direction / mockups
