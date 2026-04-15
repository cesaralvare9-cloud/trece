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

## Deployment & URLs

| Environment | URL | Platform |
|-------------|-----|----------|
| Landing page | `https://www.drinktrece.com` | Netlify |
| Product store | `https://shop.drinktrece.com` | Shopify |

**All CTA buttons** in `index.html` link to:
`https://shop.drinktrece.com/products/drink-club-13-original`

### Shopify — Header Logo Redirect
The logo in the Shopify store header (`header.liquid`) links back to the landing page instead of the store root. There are **2 anchor tags** to keep in sync — one for `logo_position != 'middle-center'` and one for `logo_position == 'middle-center'`:

```liquid
<!-- Both instances should read: -->
<a href="https://www.drinktrece.com" class="header__heading-link link link--text focus-inset">
```

Do **not** use `{{ routes.root_url }}` for the logo href — that would send users to the Shopify store root instead of the main landing page.

---

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

## Section: Elige Tu Elixir (Product Section) — Current State

### Layout
- **Desktop (>1024px):** 3-column grid (`repeat(3, 1fr)`, `gap: 2px`). All 3 flavor cards visible at once.
- **Tablet + Mobile (≤1024px):** Horizontal scroll carousel. Cards snap per item. Second card peeks to signal scrollability.

### Availability
- **Card 01 (Blue):** In stock. Has real product image (`lata frontal.png`).
- **Card 02 (Orange):** Out of stock (`Agotadas`).
- **Card 03 (Purple):** Out of stock (`Agotadas`).

### Out-of-stock treatment
Cards 02 and 03 use `.can-visual-placeholder` with class `.can-name-soldout` instead of `.can-name`:
```html
<span class="can-name-soldout" style="color: var(--orange);">AGOTADAS</span>
```
CSS — diagonal text (no writing-mode, just rotate):
```css
.can-visual-placeholder .can-name-soldout {
  font-family: var(--font-display);
  font-weight: 900;
  font-size: 32px;
  letter-spacing: 0.08em;
  transform: rotate(-45deg);
  color: var(--white);
}
```

### Carousel (≤1024px)
Key challenge: `.product-section` has `overflow: hidden` (for the TRECE watermark `::before`). Overridden to `visible` at ≤1024px so horizontal scroll works.

Negative-margin trick to break out of section's 24px padding while keeping first card indented:
```css
@media (max-width: 1024px) {
  .product-section { overflow: visible; }

  .flavors-grid {
    display: flex;
    flex-direction: row;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    -webkit-overflow-scrolling: touch;
    scrollbar-width: none;
    gap: 8px;
    padding-left: 24px;
    padding-right: 24px;
    margin-left: -24px;
    margin-right: -24px;
    padding-bottom: 16px;
    margin-bottom: 48px;
  }

  .flavor-card {
    flex: 0 0 calc(85vw - 24px);
    min-width: 0;
    scroll-snap-align: start;
    transition: none;
  }
}
```

**Peek math:** card = `(0.85 × vw) − 24px`. At 1024px → ~130px peek; at 390px → ~40px peek.

### Known bug — Reveal animation causes carousel jump

**Root cause:** The `.reveal` class sets `transform: translateY(24px)` on elements initially. The `IntersectionObserver` adds `.visible` when an element enters the viewport, snapping the transform to `translateY(0)`. In a horizontal scroll carousel, cards 2+ enter the viewport mid-swipe — causing a visible 24px vertical jump (glitch).

**Rule:** Any element inside a horizontal scroll carousel that has the `.reveal` class **must** have the reveal animation disabled in the mobile media query. Always add:

```css
@media (max-width: 1024px) {
  /* Kill reveal animation inside [carousel-container] — IntersectionObserver fires
     mid-swipe causing a 24px translateY jump that glitches the carousel */
  [carousel-container] .reveal,
  [carousel-container] .reveal.visible {
    opacity: 1;
    transform: none;
    transition: none;
  }
}
```

**Currently applied to:**
- `.flavors-grid .reveal` — flavor cards carousel (product section)
- `.stats-col .reveal` — stat cards carousel (manifesto section)

If you add a new horizontal scroll carousel on mobile and place `.reveal` elements inside it, apply the same fix.

---

## Section: Manifesto / Social Proof — Stat Cards Carousel (Mobile)

On mobile (≤768px), the three `.stat-item` cards inside `.stats-col` become a horizontal snap carousel (same pattern as flavor cards). The `.stats-carousel-wrap` becomes `position: absolute; inset: 0` and `.stats-col` switches to `flex-direction: row; overflow-x: auto; scroll-snap-type: x mandatory`.

Each `.stat-item` has `flex: 0 0 calc(100% - 40px)` so it nearly fills the banner width, with the next card peeking.

---

## GitHub
To push to GitHub after local setup:
```bash
git init
git add .
git commit -m "Initial TRECE website setup"
gh repo create trece-website --public --source=. --push
```
