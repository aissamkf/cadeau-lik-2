# Cadeau Lik — Full Site Redesign Spec
Date: 2026-04-15

## Overview
Full rebuild of `index.html` to adopt a kie.ai-inspired modern layout while keeping the existing black & gold luxury brand identity. All content, assets, and page sections remain identical — only the visual layer changes.

## Design System

### Colors
| Token | Value | Usage |
|-------|-------|-------|
| `--bg` | `#080808` | Page background |
| `--gold` | `#d4af37` | Primary accent |
| `--gold-light` | `#f1c750` | Gradient end, highlights |
| `--glass-bg` | `rgba(212,175,55,0.06)` | Card backgrounds |
| `--glass-border` | `rgba(212,175,55,0.2)` | Card borders |
| `--text-main` | `#fcfcfc` | Headings |
| `--text-muted` | `rgba(255,255,255,0.55)` | Body text |

### Typography
- **Headings**: Playfair Display (kept — user decision)
- **Body / UI / labels**: Inter
- **Gold italic**: Playfair Display italic in `--gold` for hero accent word

### Background System (all sections)
- Base: `#080808`
- Grid overlay: `linear-gradient` at 32px × 32px, gold at 5% opacity
- Glow orbs: `radial-gradient` gold blobs, 200–300px, absolute positioned top-left and bottom-right of each section

### Glassmorphism Cards
- `background: rgba(212,175,55,0.06)`
- `backdrop-filter: blur(12px)`
- `border: 1px solid rgba(212,175,55,0.2)`
- `border-radius: 16px`
- Hover: border brightens to `rgba(212,175,55,0.4)`, slight `translateY(-4px)`

### Buttons
- **Primary**: `linear-gradient(135deg, #d4af37, #f1c750)`, black text, `border-radius: 8px`, bold
- **Secondary**: transparent, `border: 1px solid rgba(212,175,55,0.4)`, gold text
- **Badge/tag**: pill shape, `background: rgba(212,175,55,0.1)`, uppercase Inter, letter-spacing

### Scroll Animations
- All sections below hero: `opacity: 0; transform: translateY(40px)` initial state
- `IntersectionObserver` adds `.visible` class when element enters viewport
- `.visible`: `opacity: 1; transform: translateY(0); transition: 0.6s ease`
- Staggered delay for child elements within sections

---

## Page Sections

### 1. Navigation
- Fixed, transparent → dark on scroll (same behavior as current)
- Logo: existing `logo.png` image + "CADEAU LIK" text
- Nav links: Inter uppercase, gold underline on hover
- Mobile: hamburger menu

### 2. Hero
- **Layout**: Split — text left (55%), visual right (45%)
- **Left side**:
  - Small tag badge: `✦ Marrakech · Morocco`
  - H1 in Playfair Display — italic gold accent word
  - Subtitle in Inter muted
  - Two buttons: Primary CTA ("Explorer les cadeaux →") + Secondary ("Notre histoire")
- **Right side**:
  - Video placeholder `<video>` tag — user will provide the AI-generated animation video
  - Fallback: animated floating gift card mockup (CSS float animation) until video is ready
  - Video: autoplay, loop, muted, no controls, `border-radius: 20px`
- **Background**: grid + two large gold glow orbs
- No scroll animation on hero (it's the entry point)

### 3. About
- Slide-up on scroll
- Two-column: text left, decorative right (existing `about-bg.png`)
- Glass card wrapping the text block
- Gold label + Playfair heading + Inter body

### 4. How It Works
- Slide-up on scroll, staggered children
- 3-step cards in a row — glassmorphism cards
- Step number in gold, large Playfair
- Icon or number badge top of each card

### 5. Catalog
- Slide-up on scroll
- Section header: gold label + Playfair heading
- **Standard tier (200 MAD)**: horizontal scrollable row or grid of glassmorphism cards
- **Premium tier (400 MAD)**: same pattern, visually distinguished (slightly warmer gold border)
- Each card: gift name, description tags, price badge, "Commander" button
- Existing gift data and images retained exactly

### 6. FAQ
- Slide-up on scroll
- Accordion: glass card style, gold `+`/`−` toggle icon
- Smooth expand/collapse animation (CSS max-height transition)

### 7. Footer
- Dark with grid overlay
- Logo + nav links + social icons
- Gold divider line
- Copyright in muted Inter

---

## Implementation Approach
- **Method**: Full rewrite of `index.html` (Option 2)
- **Dependencies**: Google Fonts (Playfair Display + Inter), Font Awesome (icons already in use)
- **No new external libraries** — animations via IntersectionObserver + CSS transitions
- All existing assets (`assets/` folder) reused unchanged
- `gifts.html` and `product.html` not in scope for this redesign pass
- Hero video: placeholder `<video>` tag ready — user fills `src` when AI video is ready

---

## Out of Scope
- `gifts.html` and `product.html` pages
- Backend / ordering logic
- Hero animation content (user generating via OpenAI Sora — slot reserved)
