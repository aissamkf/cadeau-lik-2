# Product Detail Page — Design Spec
**Date:** 2026-04-17  
**Project:** Cadeau Lik  

---

## Overview

Create `product.html` — a standalone product detail page that users land on when clicking a gift card in the Gifts section of `index.html`. The page reads all product data from URL parameters and renders a luxury two-column detail view matching the site's black-and-gold aesthetic.

---

## Architecture

### Files Changed
- **New:** `product.html` — standalone product detail page
- **Modified:** `index.html` — gift card clicks redirect to product.html instead of adding directly to cart

### Cart State
- Stored in `localStorage` under key `cadeauLikCart` as a JSON array
- Both pages read/write the same localStorage key
- Cart badge count on both pages reflects localStorage state on load

### URL Parameters
| Param | Example | Description |
|-------|---------|-------------|
| `name` | `Father — Standard` | Full product name |
| `tier` | `standard` / `premium` | Tier level |
| `price` | `200` | Price in MAD |
| `image` | `assets/father-standard.png` | Relative image path |
| `relation` | `father` | Relation key (father, husband, friend, mother, wife) |
| `gender` | `him` / `her` | Gender group for "You May Also Like" |

Items are derived from `tier` (not passed via URL):
- **Standard:** Luxury Gift Wrap, Artisan Chocolates, Premium Flowers, Scented Candle, Personal Card
- **Premium:** Luxury Gift Wrap, Artisan Chocolates, Premium Flowers, Scented Candle, Personal Card, Premium Perfume, Silk Ribbon, Gold Keepsake Box

---

## product.html Layout

### Navbar
- Left: `← BACK TO GIFTS` link → `index.html#gifts`
- Center: CADEAU LIK logo (text)
- Right: cart icon with badge (count from localStorage)
- Same scroll-on-scroll behavior as index.html nav

### Hero — Two Columns (desktop side-by-side, mobile stacked)

**Left column (50%)**
- Large image area, dark background (`#111`), rounded corners 16px
- Image fills area with `object-fit: contain`, centered
- Gold category badge overlay top-left: e.g. "FATHER — STANDARD BOX"
- Fallback: gold gift icon if no image

**Right column (50%)**
- Category pills: relation pill + tier pill (gold border, gold text, pill shape)
- Title: serif font, large — relation + tier in white/gold mix
- Description: paragraph with gold left border line (4px solid gold)
- "WHAT'S INSIDE THIS BOX" label in gold uppercase with divider
- Items grid: 2 columns, each item = dark card with gold star icon + item name
- Price: large bold white number + small grey "DH" unit
- "Cash on delivery available" grey subtext
- Qty selector: QTY label + circular −/+ buttons + number display
- "ADD TO CART" full-width white button with cart icon

---

## "You May Also Like" Section

Appears below the product detail columns, above the footer.

- Section heading: "You May Also <em>Like</em>" — serif, centered, "Like" in gold italic
- Shows 4–6 cards from the same gender, different relations or tiers
- Logic: filter all products of same gender, exclude current product
- Each card: product image, name, tier badge, price, "View Details" button
- "View Details" → redirects to product.html with that card's params

**Desktop:** 4 cards in a row, equal spacing, left/right arrow nav buttons  
**Mobile:** horizontal scroll with snap, 1.5 cards visible

### Product Data (hardcoded JS object in product.html)
```js
const ALL_PRODUCTS = [
  { name:'Father — Standard', tier:'standard', price:200, image:'assets/father-standard.png', relation:'father', gender:'him' },
  { name:'Father — Premium',  tier:'premium',  price:400, image:'assets/father-premium.png',  relation:'father', gender:'him' },
  { name:'Husband — Standard',tier:'standard', price:200, image:'assets/husband-standard.png',relation:'husband',gender:'him' },
  { name:'Husband — Premium', tier:'premium',  price:400, image:'assets/husband-premium.png', relation:'husband',gender:'him' },
  { name:'Friend — Standard (Him)', tier:'standard', price:200, image:'assets/friend-standard.png', relation:'friend', gender:'him' },
  { name:'Friend — Premium (Him)',  tier:'premium',  price:400, image:'assets/friend-premium.png',  relation:'friend', gender:'him' },
  { name:'Mother — Standard', tier:'standard', price:200, image:'assets/mother-standard.png', relation:'mother', gender:'her' },  // placeholder image
  { name:'Mother — Premium',  tier:'premium',  price:400, image:'assets/mother-premium.png',  relation:'mother', gender:'her' },
  { name:'Wife — Standard',   tier:'standard', price:200, image:'assets/wife-standard.png',   relation:'wife',   gender:'her' },
  { name:'Wife — Premium',    tier:'premium',  price:400, image:'assets/wife-premium.png',    relation:'wife',   gender:'her' },
  { name:'Friend — Standard (Her)', tier:'standard', price:200, image:'assets/friend-standard.png', relation:'friend', gender:'her' },
  { name:'Friend — Premium (Her)',  tier:'premium',  price:400, image:'assets/friend-premium.png',  relation:'friend', gender:'her' },
];
```

---

## Footer

Exact copy of index.html footer — logo, nav links, social icons, copyright line.

---

## index.html Changes

- `addToCartFromTier(btn)` → replaced with `goToProduct(btn)` which builds URL params and redirects
- Each tier card's button: `onclick="goToProduct(this)"` instead of `addToCartFromTier`
- Cart badge on index.html reads from localStorage on page load

---

## Design Tokens (inherited from index.html)
- `--gold: #d4af37`, `--gold-light: #f1c750`
- `--bg: #1a1a1a`, fonts: Playfair Display + Inter
- Same CSS variables, button styles, glass card styles

---

## Mobile Responsive
- Below 768px: columns stack (image top, details bottom)
- "You May Also Like": horizontal scroll snap, 1.5 cards visible
- Cart sidebar: full width
