# TRECE — Project Context

## Brand
**TRECE** — Elixir Funcional Social
Non-alcoholic functional sparkling drink. Made in Rionegro, Antioquia, Colombia.
Tagline: *"Socializa sin ceder terreno."*
INVIMA: RSA-1472-2025

## Stack
- Single-file HTML (`index.html`) with inline CSS and JS
- No build tools, no frameworks, no dependencies
- Fonts loaded from Google Fonts: Barlow Condensed, Barlow, Space Mono

## Design Tokens
```
--black: #080808
--white: #F4F4F0
--blue: #6BAED4          ← primary accent
--blue-dark: #4A8FB5
--grey: #1A1A1A
--grey-mid: #2E2E2E
--grey-light: #8A8A8A
--orange: #D4673B        ← flavor 2 accent
--purple-accent: #9B8FD4 ← flavor 3 accent
```

## Products
| # | Color | Flavor | Ingredients |
|---|-------|--------|-------------|
| 01 | Blue | Mandarina — Limón — Cardamomo | Melena de León · Treonato de Mg · L-Teanina |
| 02 | Orange | Flor de Jamaica — Naranja — Jengibre | Melena de León · Treonato de Mg · L-Teanina |
| 03 | Purple | Arándano — Frambuesa | Melena de León · Treonato de Mg · L-Teanina |

**Bundle:** Pack Explorador x6 — $89,900 COP · Free shipping Medellín & Bogotá

## Key Ingredients
- **L-Teanina** (Camellia sinensis) — reduces social anxiety, calm alertness
- **Melena de León** (Hericium erinaceus) — NGF stimulation, cognitive clarity
- **Treonato de Magnesio** — crosses blood-brain barrier, cortisol regulation, sleep recovery

## File Structure
```
TRECE/
├── index.html          ← main site (all CSS/JS inline)
├── CLAUDE.md           ← this file
├── .gitignore
└── assets/
    └── images/         ← place product photos here
```

## Images
Product photos go in `assets/images/`. The main can photo should be `IMG_3212.jpg` (or update `src` attributes in index.html to match your filenames).

Image is used in two places:
1. Hero section (right side, desktop only)
2. Flavor card 01 (Blue / Mandarina-Limón-Cardamomo)

## Running Locally
```bash
# Option 1 — just open in browser
open index.html

# Option 2 — local server (avoids CORS issues)
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Page Sections (in order)
1. Navigation (fixed, blur on scroll)
2. Hero — headline + product image
3. Tension strip — scrolling marquee
4. Problem section ("El dilema")
5. Product section — 3 flavor cards + bundle CTA
6. Manifesto — quote + 3 market stats
7. Science — 3 ingredient cards
8. Ritual — 3-step "how it works"
9. Final CTA — "Bienvenido al club"
10. Footer

## Section: El Dilema (Problem Section) — Current State

### Layout
- **Desktop:** 2-column sticky grid (can left, text right). Section height `204vh`.
- **Mobile:** 1-column sticky grid (can top `44vh`, text below `auto`). Section height `200vh`.
- `.problem-pin` is `position: sticky; top: 0; height: 100vh` on desktop, `height: auto` on mobile.

### Animation — Sequential Replace (scroll-driven)
4 elements share the same space and replace each other as the user scrolls. Only one is visible at a time.

**HTML structure:**
```html
<div class="problem-stage">
  <div class="problem-header" data-stage-index="0">  <!-- title, visible first -->
  <div class="problem-statement" data-stage-index="1">  <!-- card 01 -->
  <div class="problem-statement" data-stage-index="2">  <!-- card 02 -->
  <div class="problem-statement" data-stage-index="3">  <!-- card 03 -->
</div>
```
- `.problem-header` is `position: relative` (gives the stage its natural height).
- `.problem-statement` are `position: absolute; top: 0; left: 0; right: 0` — stacked on top of header.

**Scroll slots (progress 0→1):**
| Slot | Element | Visible range |
|------|---------|---------------|
| 0 | Title (El dilema headline) | 0.00 – 0.25 |
| 1 | Card 01 | 0.25 – 0.50 |
| 2 | Card 02 | 0.50 – 0.75 |
| 3 | Card 03 | 0.75 – 1.00 |

Crossfade window: `0.15` centered at each boundary (0.25, 0.50, 0.75).
Transition: fade + `translateY(±20px)` slide (enters from below, exits upward).

**Can animation:** zooms from `scale(2.8)` to `scale(1.0)` over the full scroll range (progress 0→1).

### Key JS constants
```js
const SLOTS  = 4;
const SLOT_W = 0.25;
const FADE_W = 0.15;
```

### Mobile overrides (inside `@media (max-width: 768px)`)
```css
.problem-section { height: 200vh; }
.problem-pin { grid-template-columns: 1fr; grid-template-rows: 44vh auto; height: auto; align-items: start; }
.problem-left { height: 44vh; align-items: flex-end; }
.problem-can { width: 38%; max-width: 180px; }
.problem-right { padding: 36px 28px 48px; justify-content: flex-start; }
```

---

## GitHub
To push to GitHub after local setup:
```bash
git init
git add .
git commit -m "Initial TRECE website setup"
gh repo create trece-website --public --source=. --push
```
