# CLAUDE.md

## Project Overview

This repository contains a **single-file price comparison and tracking web application** (`price-comparison.html`). It is a standalone, client-side SPA built with vanilla HTML5, CSS3, and JavaScript (ES6+) — no build tools, frameworks, or external dependencies required.

**Primary purpose:** Help users track product prices across multiple stores (Guardian, Watsons, Caring, Shopee), compare promotions, and manage a personal shopping list.

---

## Repository Structure

```
/home/user/personal/
├── CLAUDE.md                  # This file
└── price-comparison.html      # Entire application (~3700 lines)
```

The entire application lives in one file, structured as:

```
price-comparison.html
├── <head>
│   ├── meta / title tags
│   └── <style>           # ~1500 lines of CSS (lines 7–1517)
└── <body>
    ├── Sidebar            # Navigation, search, category list, to-buy preview
    ├── Tab container      # Guide | Calculator | To-Buy | Manage | Summary | [product tabs]
    └── <script>           # ~2170 lines of JavaScript (lines ~1519–3690)
```

---

## Running the Application

No build step is needed. Open the file directly in a browser:

```bash
# macOS / Linux
open price-comparison.html

# Or just double-click the file in a file manager
```

**Browser requirements:** Chrome 51+, Firefox 15+, Safari 9.1+, Edge 15+
(ES6, localStorage, CSS Grid/Flexbox, CSS custom properties)

---

## Architecture & Key Concepts

### Data Persistence

All data is stored in **`localStorage`** under these keys:

| Key | Contents |
|-----|----------|
| `ceravePriceData` | Price history for all products |
| `customProducts` | User-added products |
| `customStores` | User-added stores |
| `productStores` | Per-product store availability mapping |
| `customCategories` | User-added categories |
| `productCategories` | Product → category mapping |
| `toBuyList` | Shopping list items |
| `productBrands` | Product → brand mapping |
| `subcategories` | Category → subcategory structure |
| `productSubcategories` | Product → subcategory mapping |

Data is loaded once on page init (`loadData()`) and written back on every mutation (`saveData()` and per-entity save helpers).

### Default Data

The app ships with built-in defaults that load when localStorage is empty:
- **Products:** CeraVe Hydrating Cleanser (3 sizes), CeraVe Foaming Cleanser (3 sizes), Garnier Rose Water 400ml
- **Stores:** Guardian, Watsons, Caring, Shopee
- **Categories:** Skincare (护肤), Makeup (美妆), Clothing (衣服), Accessories (配饰)
- **Subcategories:** 5–7 per category (e.g., Cleanser, Toner, Serum… under Skincare)

### JavaScript Function Groups

**Data layer:**
- `loadData()` / `saveData()` — read/write localStorage
- `exportData()` / `importData()` — JSON backup and restore
- `clearAllData()` — full reset (with confirmation dialog)

**Product CRUD:**
- `addNewProduct()`, `deleteProduct()`, `updateProductList()`, `updateProductTable()`, `createProductPage()`

**Store CRUD:**
- `addNewStore()`, `deleteStore()`, `updateStoreList()`, `updateStoreTable()`, `toggleStoreForProduct()`

**Category / Subcategory CRUD:**
- `addNewCategory()`, `deleteCategory()`, `addSubcategory()`, `deleteSubcategory()`, `updateSubcategoryDropdown()`

**Price & Promo:**
- `savePriceEntry()` — log a price record
- `calculatePromo()` — evaluate promotional pricing (BOGO, 2nd -50%, fixed, % discount, bulk)
- `updateHistoryTable()` / `deleteEntryByDate()` / `toggleSortOrder()`

**UI & Navigation:**
- `switchTab()`, `generateTabs()` — tab management
- `globalSearch()`, `navigateToProduct()` — search and deep-link
- `updateToBuyList()`, `updateCategoryNav()`, `scrollToCategoryInSummary()`

**Statistics:**
- `updateStatisticsCards()` — min / avg / max per product
- `updateSummaryTable()` — all-products price overview

### CSS Design System

Custom properties defined in `:root`:

```css
--primary: #667eea       /* blue-purple */
--secondary: #764ba2     /* darker purple */
--gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%)
--success: #28a745
--warning: #ffc107
--danger: #dc3545
--info: #2196f3
--light-bg: #f8f9fa
```

Sidebar is fixed at 280px; main content fills the remainder. Layouts use CSS Grid and Flexbox.

---

## Development Conventions

### File Editing

- **All changes go in `price-comparison.html`** — there is no separate JS/CSS/HTML source splitting.
- CSS lives inside the single `<style>` block in `<head>`.
- JavaScript lives inside the single `<script>` block at the bottom of `<body>`.
- When adding a new feature, follow the existing function-grouping pattern (data → CRUD → UI).

### Naming Conventions

- JavaScript functions: `camelCase` (e.g., `savePriceEntry`, `updateHistoryTable`)
- localStorage keys: `camelCase` (e.g., `ceravePriceData`, `customProducts`)
- CSS classes: `kebab-case` (e.g., `sidebar-nav`, `price-card`)
- CSS variables: `--kebab-case`
- IDs (used sparingly): `camelCase` or `kebab-case` matching the functional area

### Bilingual UI

The interface supports **Chinese (中文) and English**. When adding new UI text:
- Labels visible in the interface should have both languages where other labels use both (check nearby elements for the pattern — some use Chinese-first, some English-first).
- Form field placeholders and button text follow the existing bilingual style.

### No External Dependencies

Do **not** introduce npm packages, CDN-hosted libraries, or any external scripts. The zero-dependency constraint is intentional — the file must work offline.

### Data Safety

- Any destructive action (delete product, clear all data) must include a `confirm()` dialog.
- Export before destructive operations should be encouraged in UI copy.
- Never silently overwrite `localStorage` keys without first loading existing data.

---

## Testing

There is no automated test suite. Testing is manual:

1. Open `price-comparison.html` in a browser.
2. Use browser DevTools → Application → Local Storage to inspect persisted data.
3. Test each tab: Guide, Calculator, To-Buy, Manage Products, Summary, and individual product pages.
4. Verify export/import round-trips produce identical data.

When making changes, test the affected feature path end-to-end in the browser.

---

## Git Workflow

- **Main branch:** `master`
- **Feature/AI branches:** prefixed with `claude/` (e.g., `claude/claude-md-mmejbrn2polhdsw5-YGdUE`)
- Commit messages are imperative, descriptive, and reference the feature area (e.g., `Add global search functionality to price comparison tracker`).
- Commits are granular — one logical change per commit.

```bash
git add price-comparison.html
git commit -m "Add <feature description>"
git push -u origin <branch-name>
```

---

## Common Tasks

### Add a new product category
1. Find the `defaultCategories` object in `<script>` and add your entry.
2. Update `defaultSubcategories` with the new category's subcategory list.
3. Test via Manage Products → Categories tab.

### Add a new promotion type
1. Locate `calculatePromo()` in `<script>`.
2. Add a new `case` to the `switch` block.
3. Add the corresponding `<option>` to every promo-type `<select>` in the HTML.

### Change the color scheme
1. Update the CSS custom properties in the `:root` block inside `<style>`.
2. The gradient is used for sidebar, buttons, and headers — check all three.

### Export / import data format

```json
{
  "ceravePriceData": { ... },
  "customProducts": { ... },
  "customStores": { ... },
  "productStores": { ... },
  "customCategories": { ... },
  "productCategories": { ... },
  "toBuyList": [ ... ],
  "productBrands": { ... },
  "subcategories": { ... },
  "productSubcategories": { ... }
}
```

---

## Known Constraints

- **Browser-only:** No server, no API, no database.
- **Single-user:** localStorage is per-browser-origin; multi-device sync requires manual export/import.
- **File size:** The single file is ~146KB / ~3700 lines. Keep changes focused to avoid unmanageable growth.
- **No TypeScript / type safety:** All data manipulation is dynamically typed; be careful with null checks when accessing nested objects like `priceData[product][dateKey]`.
