# Cadeau Lik — Full Site Rebuild Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild `index.html` from scratch with a kie.ai-inspired modern layout — glassmorphism cards, gold grid backgrounds, glow orbs, scroll animations — while keeping the black & gold brand, Playfair Display headings, and all existing content/JS intact.

**Architecture:** The existing file has three layers: CSS (lines 1–1672), HTML (1673–2781), JS (2782–3464). We copy the JS verbatim, rewrite the CSS completely, and rebuild the HTML section by section. All content, data attributes, IDs, hrefs, and JS hooks are preserved exactly so no JS changes are needed.

**Tech Stack:** HTML5, CSS3 (custom properties, backdrop-filter, IntersectionObserver), Google Fonts (Playfair Display + Inter), Font Awesome 6

---

## File Map

| File | Action | Purpose |
|------|--------|---------|
| `index.html` | Rewrite | Single output file — new CSS + rebuilt HTML + copied JS |
| `index.html.bak` | Create (backup) | Preserve original before touching anything |

---

## Task 1: Backup the original file

**Files:**
- Create: `index.html.bak`

- [ ] **Step 1: Copy original to backup**

```bash
cp "index.html" "index.html.bak"
```

- [ ] **Step 2: Verify backup exists and matches**

```bash
diff index.html index.html.bak && echo "Backup matches"
```
Expected: no diff output, then `Backup matches`

- [ ] **Step 3: Commit**

```bash
git add index.html.bak
git commit -m "chore: backup original index.html before redesign"
```

---

## Task 2: New file — head, fonts, design system CSS

**Files:**
- Modify: `index.html` (full rewrite — start with this task, subsequent tasks append sections)

Write the new `index.html` from the `<!DOCTYPE>` opening through the end of the `<style>` block. This establishes the full design system.

- [ ] **Step 1: Write the file head + design system CSS**

Create `index.html` with the following content (this replaces the entire file — subsequent tasks will add to it):

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CADEAU LIK | Luxury Gifts</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Playfair+Display:ital,wght@0,400;0,500;0,600;0,700;1,400&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    /* ============================================================
       DESIGN SYSTEM — CSS CUSTOM PROPERTIES
    ============================================================ */
    :root {
      --bg:            #080808;
      --bg-section:    #0a0a0a;
      --gold:          #d4af37;
      --gold-light:    #f1c750;
      --glass-bg:      rgba(212,175,55,0.06);
      --glass-border:  rgba(212,175,55,0.2);
      --glass-hover:   rgba(212,175,55,0.4);
      --text-main:     #fcfcfc;
      --text-muted:    rgba(255,255,255,0.55);
      --font-heading:  'Playfair Display', serif;
      --font-body:     'Inter', sans-serif;
      --nav-scrolled:  rgba(8,8,8,0.97);
      --radius-card:   16px;
      --radius-btn:    8px;
      --transition:    0.35s ease;
    }

    /* ============================================================
       RESET & BASE
    ============================================================ */
    *, *::before, *::after { margin:0; padding:0; box-sizing:border-box; }

    html { scroll-behavior: smooth; }

    body {
      background: var(--bg);
      color: var(--text-main);
      font-family: var(--font-body);
      line-height: 1.6;
      -webkit-font-smoothing: antialiased;
    }

    h1,h2,h3,h4 { font-family: var(--font-heading); font-weight:600; letter-spacing:0.5px; }
    a { text-decoration:none; color:inherit; transition: color var(--transition); }

    /* ============================================================
       BACKGROUND SYSTEM — grid + glow orbs (shared mixin via class)
    ============================================================ */
    .section-bg {
      position: relative;
      overflow: hidden;
    }
    /* Gold dot grid overlay */
    .section-bg::before {
      content:'';
      position: absolute;
      inset: 0;
      background-image:
        linear-gradient(rgba(212,175,55,0.05) 1px, transparent 1px),
        linear-gradient(90deg, rgba(212,175,55,0.05) 1px, transparent 1px);
      background-size: 32px 32px;
      pointer-events: none;
      z-index: 0;
    }
    /* Top-left glow orb */
    .section-bg::after {
      content:'';
      position: absolute;
      top: -80px;
      left: -80px;
      width: 320px;
      height: 320px;
      background: radial-gradient(circle, rgba(212,175,55,0.18) 0%, transparent 70%);
      border-radius: 50%;
      pointer-events: none;
      z-index: 0;
    }
    /* Optional second orb (bottom-right) via extra element */
    .orb-br {
      position: absolute;
      bottom: -80px;
      right: -60px;
      width: 260px;
      height: 260px;
      background: radial-gradient(circle, rgba(241,199,80,0.12) 0%, transparent 70%);
      border-radius: 50%;
      pointer-events: none;
      z-index: 0;
    }
    /* All direct children of section-bg need z-index:1 to sit above grid */
    .section-inner {
      position: relative;
      z-index: 1;
    }

    /* ============================================================
       GLASSMORPHISM CARD
    ============================================================ */
    .glass-card {
      background: var(--glass-bg);
      backdrop-filter: blur(12px);
      -webkit-backdrop-filter: blur(12px);
      border: 1px solid var(--glass-border);
      border-radius: var(--radius-card);
      transition: border-color var(--transition), transform var(--transition), box-shadow var(--transition);
    }
    .glass-card:hover {
      border-color: var(--glass-hover);
      transform: translateY(-4px);
      box-shadow: 0 8px 32px rgba(212,175,55,0.12);
    }

    /* ============================================================
       BUTTONS
    ============================================================ */
    .btn {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      font-family: var(--font-body);
      font-size: 0.9rem;
      font-weight: 600;
      cursor: pointer;
      transition: all var(--transition);
      text-decoration: none;
      border: none;
    }
    .btn-primary {
      background: linear-gradient(135deg, var(--gold), var(--gold-light));
      color: #000;
      padding: 13px 28px;
      border-radius: var(--radius-btn);
    }
    .btn-primary:hover {
      opacity: 0.9;
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(212,175,55,0.3);
    }
    .btn-secondary {
      background: transparent;
      color: var(--gold);
      border: 1px solid rgba(212,175,55,0.4);
      padding: 12px 28px;
      border-radius: var(--radius-btn);
    }
    .btn-secondary:hover {
      border-color: var(--gold);
      background: rgba(212,175,55,0.06);
      transform: translateY(-2px);
    }

    /* ============================================================
       BADGE / TAG
    ============================================================ */
    .tag-badge {
      display: inline-block;
      background: rgba(212,175,55,0.1);
      border: 1px solid rgba(212,175,55,0.3);
      color: var(--gold);
      border-radius: 50px;
      padding: 5px 16px;
      font-size: 0.7rem;
      font-weight: 500;
      letter-spacing: 2px;
      text-transform: uppercase;
    }

    /* ============================================================
       SECTION LABELS & HEADINGS
    ============================================================ */
    .section-label {
      font-size: 0.7rem;
      letter-spacing: 3px;
      text-transform: uppercase;
      color: var(--gold);
      font-weight: 500;
      margin-bottom: 12px;
      display: block;
    }
    .section-title {
      font-family: var(--font-heading);
      font-size: clamp(2rem, 4vw, 3rem);
      font-weight: 600;
      color: var(--text-main);
      line-height: 1.2;
    }
    .section-title em {
      color: var(--gold);
      font-style: italic;
    }
    .section-subtitle {
      font-size: 1rem;
      color: var(--text-muted);
      line-height: 1.7;
      max-width: 560px;
    }

    /* ============================================================
       SCROLL REVEAL ANIMATION
    ============================================================ */
    .reveal {
      opacity: 0;
      transform: translateY(40px);
      transition: opacity 0.65s ease, transform 0.65s ease;
    }
    .reveal.visible {
      opacity: 1;
      transform: translateY(0);
    }
    /* Staggered children */
    .reveal-group > * {
      opacity: 0;
      transform: translateY(30px);
      transition: opacity 0.55s ease, transform 0.55s ease;
    }
    .reveal-group.visible > *:nth-child(1) { opacity:1; transform:translateY(0); transition-delay:0s; }
    .reveal-group.visible > *:nth-child(2) { opacity:1; transform:translateY(0); transition-delay:0.1s; }
    .reveal-group.visible > *:nth-child(3) { opacity:1; transform:translateY(0); transition-delay:0.2s; }
    .reveal-group.visible > *:nth-child(4) { opacity:1; transform:translateY(0); transition-delay:0.3s; }
    .reveal-group.visible > *:nth-child(5) { opacity:1; transform:translateY(0); transition-delay:0.4s; }

    /* ============================================================
       NAVIGATION
    ============================================================ */
    header {
      position: fixed;
      top: 0; left: 0;
      width: 100%;
      padding: 22px 6%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      z-index: 1000;
      background: transparent;
      transition: background var(--transition), padding var(--transition), box-shadow var(--transition);
    }
    header.scrolled {
      background: var(--nav-scrolled);
      padding: 14px 6%;
      box-shadow: 0 2px 24px rgba(0,0,0,0.8);
      border-bottom: 1px solid rgba(212,175,55,0.08);
    }
    .logo-wrapper {
      display: flex;
      align-items: center;
      gap: 10px;
      z-index: 1001;
      user-select: none;
    }
    .logo-img { height: 52px; width: auto; object-fit: contain; display:block; }
    .logo-text {
      font-family: var(--font-heading);
      font-size: 1.7rem;
      font-weight: 600;
      letter-spacing: 3px;
      text-transform: uppercase;
      color: var(--text-main);
    }
    .logo-gold { color: var(--gold); }

    nav ul { display:flex; list-style:none; gap:2rem; }
    nav ul li a {
      font-size: 0.78rem;
      font-weight: 500;
      letter-spacing: 2px;
      text-transform: uppercase;
      padding-bottom: 4px;
      position: relative;
    }
    nav ul li a::after {
      content:'';
      position:absolute;
      bottom:0; left:0;
      width:0; height:1px;
      background: var(--gold);
      transition: width var(--transition);
    }
    nav ul li a:hover { color: var(--gold); }
    nav ul li a:hover::after { width:100%; }

    .menu-toggle { display:none; color:var(--text-main); font-size:1.4rem; cursor:pointer; z-index:1001; }

    /* product-header-actions: hidden by default, shown by JS when in product view */
    .product-header-actions { display:none; gap:12px; align-items:center; }
    .back-btn {
      font-size:0.8rem; color:var(--text-muted); display:flex; align-items:center; gap:6px;
      transition: color var(--transition);
    }
    .back-btn:hover { color: var(--gold); }
    .cart-nav-btn {
      background: var(--glass-bg);
      border: 1px solid var(--glass-border);
      color: var(--text-main);
      border-radius:8px;
      padding:8px 14px;
      cursor:pointer;
      position:relative;
      font-size:1rem;
      transition: border-color var(--transition);
    }
    .cart-nav-btn:hover { border-color: var(--gold); }
    .cart-count {
      position:absolute; top:-6px; right:-6px;
      background: var(--gold);
      color:#000;
      font-size:0.65rem; font-weight:700;
      border-radius:50%;
      width:18px; height:18px;
      display:flex; align-items:center; justify-content:center;
    }

    /* ============================================================
       HERO
    ============================================================ */
    .hero {
      min-height: 100vh;
      padding: 120px 6% 80px;
      display: flex;
      align-items: center;
      background: var(--bg);
      position: relative;
      overflow: hidden;
    }
    /* Hero grid */
    .hero::before {
      content:'';
      position:absolute; inset:0;
      background-image:
        linear-gradient(rgba(212,175,55,0.05) 1px, transparent 1px),
        linear-gradient(90deg, rgba(212,175,55,0.05) 1px, transparent 1px);
      background-size:32px 32px;
      z-index:0; pointer-events:none;
    }
    /* Hero center glow */
    .hero-glow {
      position:absolute;
      top:50%; left:30%;
      transform:translate(-50%,-50%);
      width:600px; height:600px;
      background: radial-gradient(ellipse, rgba(212,175,55,0.12) 0%, transparent 65%);
      border-radius:50%;
      pointer-events:none; z-index:0;
    }
    .hero-container {
      position:relative; z-index:1;
      width:100%; max-width:1300px;
      margin:0 auto;
      display:flex;
      align-items:center;
      gap:60px;
    }
    .hero-content { flex:0 0 55%; }
    .hero-tag { margin-bottom:20px; }
    .hero-headline {
      font-size: clamp(2.4rem, 4.5vw, 3.8rem);
      line-height: 1.15;
      color: var(--text-main);
      margin-bottom: 20px;
    }
    .hero-headline em { color: var(--gold); }
    .hero-subheadline {
      font-size: 1.05rem;
      color: var(--text-muted);
      margin-bottom: 40px;
      max-width: 480px;
      line-height: 1.75;
      font-weight:300;
    }
    .hero-buttons { display:flex; gap:16px; flex-wrap:wrap; }

    /* Hero visual (right side) */
    .hero-visual {
      flex: 1;
      display:flex;
      justify-content:center;
      align-items:center;
    }
    .hero-video-wrapper {
      width:100%;
      max-width:520px;
      border-radius:20px;
      overflow:hidden;
      border:1px solid var(--glass-border);
      box-shadow:0 0 60px rgba(212,175,55,0.15);
      aspect-ratio: 4/3;
      background: var(--glass-bg);
      display:flex;
      align-items:center;
      justify-content:center;
    }
    /* Placeholder shown when no video src is set */
    .hero-video-placeholder {
      display:flex;
      flex-direction:column;
      align-items:center;
      gap:16px;
      color:var(--text-muted);
    }
    .hero-video-placeholder i { font-size:3rem; color:var(--gold); opacity:0.5; }
    .hero-video-placeholder span { font-size:0.8rem; letter-spacing:1px; }
    .hero-video-wrapper video {
      width:100%; height:100%;
      object-fit:cover;
      display:block;
    }

    /* ============================================================
       ABOUT
    ============================================================ */
    .about-section {
      padding: 100px 6%;
      background: var(--bg-section);
    }
    .about-inner {
      max-width:1300px;
      margin:0 auto;
      display:flex;
      gap:60px;
      align-items:center;
    }
    .about-text-side { flex:1; }
    .about-title {
      font-size: clamp(1.8rem, 3.5vw, 2.6rem);
      margin-bottom:20px;
    }
    .about-body {
      font-size:1rem;
      color: var(--text-muted);
      line-height:1.8;
    }
    .about-image-side {
      flex:1;
      border-radius: var(--radius-card);
      overflow:hidden;
      min-height:280px;
      display:flex;
      align-items:center;
      justify-content:center;
    }
    .about-image-side img { width:100%; height:100%; object-fit:cover; border-radius:var(--radius-card); }

    /* ============================================================
       HOW IT WORKS
    ============================================================ */
    .how-it-works {
      padding: 100px 6%;
      background: var(--bg);
    }
    .how-inner {
      max-width:1300px;
      margin:0 auto;
    }
    .how-header {
      text-align:center;
      margin-bottom:60px;
    }
    .steps-grid {
      display:grid;
      grid-template-columns: repeat(3, 1fr);
      gap:24px;
    }
    .step-card {
      padding:36px 28px;
      text-align:center;
    }
    .step-number {
      font-family: var(--font-heading);
      font-size:3rem;
      color: var(--gold);
      opacity:0.35;
      line-height:1;
      margin-bottom:16px;
    }
    .step-icon-wrap {
      width:56px; height:56px;
      background: rgba(212,175,55,0.1);
      border:1px solid rgba(212,175,55,0.25);
      border-radius:12px;
      display:flex; align-items:center; justify-content:center;
      margin:0 auto 20px;
      font-size:1.4rem;
      color: var(--gold);
    }
    .step-title {
      font-family: var(--font-heading);
      font-size:1.2rem;
      margin-bottom:10px;
    }
    .step-desc {
      font-size:0.9rem;
      color: var(--text-muted);
      line-height:1.7;
    }

    /* ============================================================
       CATALOG
    ============================================================ */
    .catalog-section {
      padding: 100px 6%;
      background: var(--bg-section);
    }
    .catalog-inner { max-width:1300px; margin:0 auto; }
    .catalog-header-row {
      display:flex;
      justify-content:space-between;
      align-items:flex-end;
      margin-bottom:40px;
      flex-wrap:wrap;
      gap:20px;
    }

    /* Filters */
    .filters-wrapper {
      display:flex;
      gap:28px;
      margin-bottom:40px;
      flex-wrap:wrap;
    }
    .filter-group { display:flex; flex-direction:column; gap:8px; }
    .filter-label {
      font-size:0.7rem;
      letter-spacing:2px;
      text-transform:uppercase;
      color: var(--text-muted);
    }
    .filter-buttons { display:flex; gap:8px; flex-wrap:wrap; }
    .filter-btn {
      background: var(--glass-bg);
      border: 1px solid var(--glass-border);
      color: var(--text-muted);
      border-radius:6px;
      padding:7px 16px;
      font-size:0.78rem;
      font-family: var(--font-body);
      cursor:pointer;
      transition: all var(--transition);
    }
    .filter-btn:hover, .filter-btn.active {
      border-color: var(--gold);
      color: var(--gold);
      background: rgba(212,175,55,0.08);
    }

    /* Tier title */
    .tier-label {
      display:flex;
      align-items:center;
      gap:16px;
      margin-bottom:24px;
    }
    .tier-label-text {
      font-size:0.72rem;
      letter-spacing:3px;
      text-transform:uppercase;
      color: var(--gold);
      white-space:nowrap;
    }
    .tier-label-line {
      flex:1;
      height:1px;
      background: linear-gradient(to right, rgba(212,175,55,0.3), transparent);
    }
    .catalog-tier-section { margin-bottom:64px; }

    /* Gift cards grid */
    .gifts-grid {
      display:grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap:20px;
    }
    .gift-card {
      padding:24px;
      position:relative;
      cursor:pointer;
    }
    .gift-card[data-hidden="true"] { display:none; }

    .specific-label-badge {
      position:absolute;
      top:16px; right:16px;
      background: rgba(212,175,55,0.15);
      border:1px solid rgba(212,175,55,0.3);
      color: var(--gold);
      border-radius:50px;
      padding:3px 12px;
      font-size:0.68rem;
      font-weight:500;
      letter-spacing:1px;
      text-transform:uppercase;
    }
    .premium-tier .specific-label-badge {
      background: rgba(241,199,80,0.18);
    }

    .gift-image-wrapper {
      width:100%;
      aspect-ratio:4/3;
      border-radius:10px;
      overflow:hidden;
      margin-bottom:18px;
      background: rgba(212,175,55,0.04);
      display:flex;
      align-items:center;
      justify-content:center;
    }
    .gift-image-wrapper img { width:100%; height:100%; object-fit:cover; }
    .gift-image-placeholder {
      font-size:2.5rem;
      color: rgba(212,175,55,0.25);
    }

    .gift-title {
      font-family: var(--font-heading);
      font-size:1.15rem;
      margin-bottom:8px;
    }
    .gift-desc {
      font-size:0.85rem;
      color: var(--text-muted);
      margin-bottom:14px;
      line-height:1.6;
    }
    .gift-includes { margin-bottom:18px; }
    .gift-includes-title {
      font-size:0.7rem;
      letter-spacing:2px;
      text-transform:uppercase;
      color: var(--gold);
      margin-bottom:8px;
    }
    .gift-includes ul { list-style:none; display:flex; flex-direction:column; gap:4px; }
    .gift-includes ul li {
      font-size:0.82rem;
      color: var(--text-muted);
      padding-left:14px;
      position:relative;
    }
    .gift-includes ul li::before {
      content:'✦';
      position:absolute; left:0;
      color: var(--gold);
      font-size:0.55rem;
      top:4px;
    }

    .gift-footer { display:flex; align-items:center; justify-content:space-between; margin-bottom:16px; }
    .gift-price {
      font-family: var(--font-heading);
      font-size:1.4rem;
      color: var(--gold);
    }
    .gift-price span { font-size:0.75rem; color: var(--text-muted); margin-left:4px; }

    .btn-order {
      display:inline-flex;
      align-items:center;
      gap:8px;
      width:100%;
      justify-content:center;
      background: linear-gradient(135deg, var(--gold), var(--gold-light));
      color:#000;
      border:none;
      border-radius:8px;
      padding:11px;
      font-size:0.82rem;
      font-weight:700;
      font-family: var(--font-body);
      cursor:pointer;
      transition: opacity var(--transition), transform var(--transition);
      text-decoration:none;
    }
    .btn-order:hover { opacity:0.88; transform:translateY(-1px); }

    /* Premium tier — slightly warmer border */
    .premium-tier .glass-card {
      border-color: rgba(241,199,80,0.25);
    }
    .premium-tier .glass-card:hover {
      border-color: rgba(241,199,80,0.5);
    }

    /* ============================================================
       FAQ
    ============================================================ */
    .faq-section {
      padding:100px 6%;
      background: var(--bg);
    }
    .faq-inner { max-width:800px; margin:0 auto; }
    .faq-header { text-align:center; margin-bottom:56px; }

    .faq-accordion { display:flex; flex-direction:column; gap:12px; }
    .faq-item { border-radius: var(--radius-card); overflow:hidden; }

    .faq-trigger {
      width:100%;
      background: var(--glass-bg);
      border: 1px solid var(--glass-border);
      border-radius: var(--radius-card);
      padding:20px 24px;
      display:flex;
      align-items:center;
      justify-content:space-between;
      cursor:pointer;
      transition: border-color var(--transition), background var(--transition);
      color: var(--text-main);
      font-family: var(--font-body);
      text-align:left;
    }
    .faq-trigger:hover { border-color: var(--gold); background: rgba(212,175,55,0.08); }
    .faq-trigger[aria-expanded="true"] { border-color: var(--gold); border-bottom-left-radius:0; border-bottom-right-radius:0; }

    .faq-question { font-size:0.95rem; font-weight:500; }
    .faq-icon { color: var(--gold); font-size:0.85rem; transition: transform 0.3s ease; flex-shrink:0; }
    .faq-trigger[aria-expanded="true"] .faq-icon { transform:rotate(45deg); }

    .faq-panel {
      max-height:0;
      overflow:hidden;
      transition: max-height 0.4s ease;
      background: rgba(212,175,55,0.03);
      border: 1px solid var(--glass-border);
      border-top:none;
      border-bottom-left-radius: var(--radius-card);
      border-bottom-right-radius: var(--radius-card);
    }
    .faq-panel-inner {
      padding:20px 24px;
      font-size:0.9rem;
      color: var(--text-muted);
      line-height:1.75;
    }
    .faq-panel-inner p + p { margin-top:8px; }

    /* ============================================================
       FOOTER
    ============================================================ */
    .footer-lux {
      background: var(--bg);
      position:relative;
      overflow:hidden;
    }
    .footer-lux::before {
      content:'';
      position:absolute; inset:0;
      background-image:
        linear-gradient(rgba(212,175,55,0.04) 1px, transparent 1px),
        linear-gradient(90deg, rgba(212,175,55,0.04) 1px, transparent 1px);
      background-size:32px 32px;
      pointer-events:none;
    }
    .gold-line {
      height:1px;
      background: linear-gradient(to right, transparent, var(--gold), transparent);
      position:relative; z-index:1;
    }
    .footer-lux-inner {
      max-width:1300px;
      margin:0 auto;
      padding:60px 6%;
      display:grid;
      grid-template-columns: 2fr 1fr 1fr;
      gap:40px;
      position:relative; z-index:1;
    }
    .footer-brand-name {
      font-family: var(--font-heading);
      font-size:1.5rem;
      letter-spacing:3px;
      color: var(--text-main);
      margin-bottom:8px;
    }
    .footer-tagline {
      font-size:0.8rem;
      color: var(--text-muted);
      letter-spacing:1px;
    }
    .footer-col-title {
      font-size:0.7rem;
      letter-spacing:2.5px;
      text-transform:uppercase;
      color: var(--gold);
      margin-bottom:16px;
    }
    .footer-links-list { list-style:none; display:flex; flex-direction:column; gap:10px; }
    .footer-links-list a {
      font-size:0.85rem;
      color: var(--text-muted);
      display:flex; align-items:center; gap:8px;
      transition: color var(--transition);
    }
    .footer-links-list a:hover { color: var(--gold); }
    .footer-location { font-size:0.85rem; color: var(--text-muted); }
    .footer-bottom {
      max-width:1300px;
      margin:0 auto;
      padding:20px 6%;
      display:flex;
      justify-content:space-between;
      align-items:center;
      font-size:0.75rem;
      color: var(--text-muted);
      position:relative; z-index:1;
    }

    /* ============================================================
       FLOATING WHATSAPP BUTTON
    ============================================================ */
    .whatsapp-float {
      position:fixed;
      bottom:28px; right:28px;
      background: linear-gradient(135deg, #25d366, #128c4f);
      color:#fff;
      width:54px; height:54px;
      border-radius:50%;
      display:flex; align-items:center; justify-content:center;
      font-size:1.5rem;
      box-shadow:0 4px 20px rgba(37,211,102,0.4);
      z-index:900;
      transition: transform var(--transition), box-shadow var(--transition);
    }
    .whatsapp-float:hover { transform:scale(1.08); box-shadow:0 6px 28px rgba(37,211,102,0.55); }

    /* ============================================================
       PRODUCT VIEW (preserves JS hooks — minimal restyling)
    ============================================================ */
    .product-view {
      display:none;
      padding:120px 6% 80px;
      min-height:100vh;
      background: var(--bg);
    }
    .product-page { max-width:1200px; margin:0 auto; }
    .breadcrumb {
      display:flex; align-items:center; gap:8px;
      font-size:0.8rem; color: var(--text-muted);
      margin-bottom:40px;
    }
    .breadcrumb a:hover { color: var(--gold); }
    .product-layout {
      display:grid;
      grid-template-columns:1fr 1fr;
      gap:60px;
      margin-bottom:60px;
    }
    .product-image-main {
      aspect-ratio:1;
      border-radius:20px;
      background: var(--glass-bg);
      border:1px solid var(--glass-border);
      display:flex; align-items:center; justify-content:center;
      font-size:5rem;
      color: rgba(212,175,55,0.3);
      position:relative;
    }
    .product-image-badge {
      position:absolute; top:16px; left:16px;
      background: rgba(212,175,55,0.15);
      border:1px solid rgba(212,175,55,0.3);
      color: var(--gold);
      border-radius:50px;
      padding:4px 14px;
      font-size:0.72rem;
      letter-spacing:1px;
    }
    .product-badges { display:flex; gap:10px; margin-bottom:16px; flex-wrap:wrap; }
    .badge {
      padding:4px 14px;
      border-radius:50px;
      font-size:0.72rem;
      font-weight:500;
      letter-spacing:1px;
      text-transform:uppercase;
    }
    .badge-relation { background:rgba(212,175,55,0.1); border:1px solid rgba(212,175,55,0.25); color:var(--gold); }
    .badge-standard { background:rgba(255,255,255,0.07); border:1px solid rgba(255,255,255,0.15); color:var(--text-muted); }

    .product-title { font-size:clamp(1.6rem,3vw,2.4rem); margin-bottom:12px; }
    .product-desc { color: var(--text-muted); line-height:1.75; margin-bottom:24px; }
    .info-divider { height:1px; background: rgba(212,175,55,0.12); margin:24px 0; }
    .includes-title { font-size:0.7rem; letter-spacing:2px; text-transform:uppercase; color:var(--gold); margin-bottom:16px; }
    .includes-grid { display:grid; grid-template-columns:repeat(auto-fill,minmax(120px,1fr)); gap:10px; }
    .include-item {
      background: var(--glass-bg);
      border:1px solid var(--glass-border);
      border-radius:10px;
      padding:12px;
      font-size:0.82rem;
      color: var(--text-muted);
      text-align:center;
    }
    .price-row { display:flex; align-items:baseline; gap:6px; margin-bottom:6px; }
    .price-main { font-family:var(--font-heading); font-size:2.4rem; color:var(--gold); }
    .price-currency { font-size:0.9rem; color:var(--text-muted); }
    .price-note { font-size:0.78rem; color:var(--text-muted); }
    .qty-row {
      display:flex; align-items:center; gap:12px; margin:20px 0;
    }
    .qty-label { font-size:0.8rem; color:var(--text-muted); }
    .qty-btn {
      width:34px; height:34px;
      background: var(--glass-bg);
      border:1px solid var(--glass-border);
      border-radius:8px;
      color:var(--text-main);
      cursor:pointer;
      display:flex; align-items:center; justify-content:center;
      font-size:0.85rem;
      transition: border-color var(--transition);
    }
    .qty-btn:hover { border-color:var(--gold); }
    .qty-display {
      width:48px; text-align:center;
      background: var(--glass-bg);
      border:1px solid var(--glass-border);
      border-radius:8px;
      color:var(--text-main);
      font-family:var(--font-body);
      padding:6px;
      font-size:0.9rem;
    }
    .action-buttons { display:flex; flex-direction:column; gap:12px; margin-bottom:20px; }
    .btn-add-cart-main {
      background: var(--glass-bg);
      border:1px solid var(--glass-border);
      color:var(--text-main);
      border-radius:var(--radius-btn);
      padding:13px;
      font-family:var(--font-body);
      font-size:0.9rem;
      cursor:pointer;
      transition: border-color var(--transition), background var(--transition);
      display:flex; align-items:center; justify-content:center; gap:8px;
    }
    .btn-add-cart-main:hover { border-color:var(--gold); background:rgba(212,175,55,0.08); }
    .btn-buy-now {
      background: linear-gradient(135deg,var(--gold),var(--gold-light));
      border:none; color:#000;
      border-radius:var(--radius-btn);
      padding:13px;
      font-family:var(--font-body);
      font-size:0.9rem;
      font-weight:700;
      cursor:pointer;
      display:flex; align-items:center; justify-content:center; gap:8px;
      transition: opacity var(--transition);
    }
    .btn-buy-now:hover { opacity:0.9; }
    .shipping-note {
      display:flex; align-items:center; gap:10px;
      font-size:0.82rem; color:var(--text-muted);
      background: var(--glass-bg);
      border:1px solid var(--glass-border);
      border-radius:10px;
      padding:12px 16px;
    }
    .shipping-note i { color:var(--gold); }
    .related-section { margin-top:60px; }
    .related-title { font-family:var(--font-heading); font-size:1.8rem; margin-bottom:28px; }
    .related-title span { color:var(--gold); }
    .related-grid { display:grid; grid-template-columns:repeat(auto-fill,minmax(220px,1fr)); gap:16px; }

    /* ============================================================
       CART SIDEBAR
    ============================================================ */
    .cart-sidebar {
      position:fixed; top:0; right:-420px;
      width:400px; height:100vh;
      background:#0d0d0d;
      border-left:1px solid var(--glass-border);
      z-index:2000;
      display:flex; flex-direction:column;
      transition: right 0.4s ease;
    }
    .cart-sidebar.open { right:0; }
    .cart-header {
      padding:24px 20px;
      border-bottom:1px solid var(--glass-border);
      display:flex; justify-content:space-between; align-items:flex-start;
    }
    .cart-title-row h3 { font-family:var(--font-heading); font-size:1.2rem; }
    .cart-subtitle { font-size:0.75rem; color:var(--text-muted); margin-top:4px; }
    .cart-close-btn {
      background:none; border:none; color:var(--text-muted); font-size:1.1rem; cursor:pointer;
      transition:color var(--transition);
    }
    .cart-close-btn:hover { color:var(--gold); }
    .cart-items { flex:1; overflow-y:auto; padding:16px 20px; }
    .cart-footer-bar {
      padding:20px;
      border-top:1px solid var(--glass-border);
    }
    .cart-summary-row { display:flex; justify-content:space-between; font-size:0.85rem; margin-bottom:8px; }
    .cart-summary-label { color:var(--text-muted); }
    .cart-summary-amount { color:var(--text-main); }
    .cart-summary-divider { height:1px; background:var(--glass-border); margin:12px 0; }
    .cart-summary-row.total { font-weight:700; font-size:1rem; }
    .cart-overlay {
      position:fixed; inset:0;
      background:rgba(0,0,0,0.6);
      backdrop-filter:blur(4px);
      z-index:1999;
      display:none;
    }
    .cart-overlay.active { display:block; }
    .btn-submit {
      width:100%;
      background:linear-gradient(135deg,var(--gold),var(--gold-light));
      border:none; color:#000;
      border-radius:var(--radius-btn);
      padding:13px;
      font-family:var(--font-body);
      font-size:0.9rem; font-weight:700;
      cursor:pointer;
      display:flex; align-items:center; justify-content:center; gap:8px;
      transition:opacity var(--transition);
      margin-top:16px;
    }
    .btn-submit:hover { opacity:0.9; }
    .btn-submit:disabled { opacity:0.4; cursor:not-allowed; }

    /* ============================================================
       ORDER MODAL
    ============================================================ */
    #orderModalOverlay {
      position:fixed; inset:0;
      background:rgba(0,0,0,0.7);
      backdrop-filter:blur(6px);
      z-index:3000;
      display:none;
      align-items:center;
      justify-content:center;
      padding:20px;
    }
    #orderModalOverlay[aria-hidden="false"] { display:flex; }
    .order-modal {
      background:#0e0e0e;
      border:1px solid var(--glass-border);
      border-radius:20px;
      width:100%;
      max-width:640px;
      max-height:90vh;
      overflow-y:auto;
    }
    .order-modal-header {
      padding:28px;
      border-bottom:1px solid var(--glass-border);
    }
    .order-modal-close {
      background:none; border:none; color:var(--text-muted); font-size:1.1rem;
      cursor:pointer; float:right; transition:color var(--transition);
    }
    .order-modal-close:hover { color:var(--gold); }
    .order-header-title {
      font-family:var(--font-heading);
      font-size:1.5rem;
      margin-bottom:4px;
    }
    .order-header-title span { color:var(--gold); }
    .order-header-subtitle { font-size:0.82rem; color:var(--text-muted); }
    .order-summary-strip {
      display:flex; align-items:center; gap:14px;
      background:var(--glass-bg);
      border:1px solid var(--glass-border);
      border-radius:10px;
      padding:14px;
      margin-top:16px;
    }
    .order-summary-icon { font-size:1.4rem; color:var(--gold); }
    .order-summary-main { flex:1; }
    .order-summary-title { font-size:0.9rem; font-weight:500; }
    .order-summary-meta { font-size:0.78rem; color:var(--text-muted); }
    .order-summary-price { font-family:var(--font-heading); font-size:1.1rem; color:var(--gold); }
    .order-modal-body { padding:28px; }
    .order-modal-body h2 { font-family:var(--font-heading); font-size:1.2rem; margin-bottom:4px; }
    .order-subtitle { font-size:0.82rem; color:var(--text-muted); margin-bottom:24px; }
    .order-section-title {
      font-size:0.7rem; letter-spacing:2px; text-transform:uppercase;
      color:var(--gold); margin:20px 0 12px;
    }
    .order-section-divider { height:1px; background:var(--glass-border); margin:24px 0; }
    .order-grid { display:grid; grid-template-columns:1fr 1fr; gap:14px; }
    .order-field { display:flex; flex-direction:column; gap:6px; }
    .order-field.full { grid-column:1/-1; }
    .order-label { font-size:0.75rem; color:var(--text-muted); letter-spacing:0.5px; }
    .order-input, .order-select, .order-textarea {
      background:var(--glass-bg);
      border:1px solid var(--glass-border);
      border-radius:8px;
      color:var(--text-main);
      font-family:var(--font-body);
      font-size:0.88rem;
      padding:10px 14px;
      transition:border-color var(--transition);
      width:100%;
    }
    .order-input:focus, .order-select:focus, .order-textarea:focus {
      outline:none;
      border-color:var(--gold);
    }
    .order-textarea { resize:vertical; min-height:80px; }
    .order-select option { background:#111; }

    /* ============================================================
       MOBILE RESPONSIVE
    ============================================================ */
    @media (max-width: 900px) {
      .hero-container { flex-direction:column; text-align:center; gap:40px; }
      .hero-content { flex:unset; }
      .hero-subheadline { margin-left:auto; margin-right:auto; }
      .hero-buttons { justify-content:center; }
      .hero-visual { width:100%; }
      .hero-video-wrapper { max-width:100%; }

      .about-inner { flex-direction:column; }
      .steps-grid { grid-template-columns:1fr; }
      .product-layout { grid-template-columns:1fr; }
      .footer-lux-inner { grid-template-columns:1fr 1fr; }
    }
    @media (max-width: 640px) {
      header { padding:16px 5%; }
      header.scrolled { padding:12px 5%; }
      nav ul { display:none; flex-direction:column; gap:0; position:fixed; top:0; left:0; width:100%; height:100vh; background:rgba(8,8,8,0.98); align-items:center; justify-content:center; z-index:999; }
      nav ul.active { display:flex; }
      nav ul li a { font-size:1.1rem; padding:16px; }
      .menu-toggle { display:block; }
      .hero { padding:100px 5% 60px; }
      .about-section, .how-it-works, .catalog-section, .faq-section { padding:70px 5%; }
      .footer-lux-inner { grid-template-columns:1fr; padding:40px 5%; }
      .footer-bottom { flex-direction:column; gap:8px; text-align:center; padding:16px 5%; }
      .order-grid { grid-template-columns:1fr; }
      .cart-sidebar { width:100%; right:-100%; }
    }
  </style>
</head>
```

- [ ] **Step 2: Open `index.html` in browser and verify**

Open `index.html` directly in browser (drag to browser, or `open index.html`).
Expected: blank black page with no errors in console

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add design system CSS — variables, glass cards, animations, all sections"
```

---

## Task 3: Navigation HTML

**Files:**
- Modify: `index.html` (append to body)

- [ ] **Step 1: Append opening body tag + header HTML**

After the closing `</style></head>`, append:

```html
<body>

  <header id="navbar">
    <div class="logo-wrapper">
      <img src="logo.png" alt="Cadeau Lik Logo" class="logo-img">
      <span class="logo-text">CADEAU <span class="logo-gold">LIK</span></span>
    </div>

    <div class="menu-toggle" id="mobile-menu">
      <i class="fas fa-bars"></i>
    </div>

    <nav>
      <ul id="nav-links">
        <li><a href="#home">Home</a></li>
        <li><a href="#gifts">Gifts</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#faq">FAQ</a></li>
      </ul>
    </nav>

    <div class="product-header-actions" id="productHeaderActions" aria-label="Product actions">
      <a href="index.html#gifts" class="back-btn" id="backToGiftsBtn"><i class="fas fa-arrow-left"></i> Back to Gifts</a>
      <button id="cartToggle" class="cart-nav-btn" aria-label="View Cart" type="button">
        <i class="fas fa-shopping-bag"></i>
        <span class="cart-count" id="cartCount">0</span>
      </button>
    </div>
  </header>

  <main>
```

- [ ] **Step 2: Verify nav in browser**

Open `index.html`. Expected: transparent nav fixed at top with logo, nav links, hamburger hidden on desktop.
Scroll down past 60px: nav background turns dark (`rgba(8,8,8,0.97)`).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add navigation with glass scroll behavior"
```

---

## Task 4: Hero section HTML

**Files:**
- Modify: `index.html` (append inside `<main>`)

- [ ] **Step 1: Append hero section**

```html
    <!-- HERO -->
    <section class="hero" id="home">
      <div class="hero-glow"></div>

      <div class="hero-container">
        <div class="hero-content">
          <div class="hero-tag">
            <span class="tag-badge">✦ Marrakech · Morocco</span>
          </div>
          <h1 class="hero-headline">
            Luxury gift boxes<br>made to spread <em>happiness.</em>
          </h1>
          <p class="hero-subheadline">
            Curated boxes with flowers, chocolates, and elegant surprises for every occasion.
          </p>
          <div class="hero-buttons">
            <a href="https://wa.me/212783552622" class="btn btn-primary" target="_blank">
              <i class="fab fa-whatsapp"></i> Chat on WhatsApp
            </a>
            <a href="#gifts" class="btn btn-secondary">Shop Gifts</a>
          </div>
        </div>

        <div class="hero-visual">
          <div class="hero-video-wrapper" id="heroVisualWrapper">
            <!--
              HERO VIDEO SLOT
              When you have the AI-generated video:
              1. Remove the placeholder div below
              2. Uncomment the <video> tag
              3. Set the src attribute to your video file path
            -->
            <div class="hero-video-placeholder" id="heroPlaceholder">
              <i class="fas fa-gift"></i>
              <span>Hero animation coming soon</span>
            </div>
            <!--
            <video autoplay loop muted playsinline>
              <source src="assets/hero-animation.mp4" type="video/mp4">
            </video>
            -->
          </div>
        </div>
      </div>
    </section>
```

- [ ] **Step 2: Verify hero in browser**

Open `index.html`. Expected:
- Full viewport height section, black bg with subtle gold grid
- Gold glow blob top-center
- Text left: tag badge, Playfair heading with italic gold accent word, muted subtitle, two buttons
- Right: bordered glass box with gift icon placeholder
- No horizontal scroll

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add hero section — split layout with video placeholder"
```

---

## Task 5: About section HTML

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Append about section**

```html
    <!-- ABOUT -->
    <section class="about-section section-bg" id="about">
      <div class="orb-br"></div>
      <div class="section-inner">
        <div class="about-inner">
          <div class="about-text-side reveal">
            <span class="section-label">✦ Our Story</span>
            <h2 class="about-title">About <em style="color:var(--gold);font-style:italic;">CADEAU LIK</em></h2>
            <p class="about-body">
              We create carefully curated gift boxes designed to turn special moments into unforgettable memories. Each box is thoughtfully crafted with premium items and personalized touches — made to be yours.
            </p>
          </div>
          <div class="about-image-side glass-card reveal">
            <img src="assets/about-bg.png" alt="About Cadeau Lik" onerror="this.style.display='none'">
          </div>
        </div>
      </div>
    </section>
```

- [ ] **Step 2: Verify in browser**

Scroll to About section. Expected:
- Dark section bg with gold grid overlay
- Bottom-right glow orb
- Two-column: text left (label + Playfair heading + body), glass card image right
- Slides up from below when scrolled into view (animation added in Task 10)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add about section with glassmorphism layout"
```

---

## Task 6: How It Works section HTML

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Append how-it-works section**

```html
    <!-- HOW IT WORKS -->
    <section class="how-it-works section-bg" id="how-it-works">
      <div class="orb-br"></div>
      <div class="section-inner">
        <div class="how-inner">
          <div class="how-header reveal">
            <span class="section-label">✦ Simple Process</span>
            <h2 class="section-title">How It <em>Works</em></h2>
          </div>
          <div class="steps-grid reveal-group">
            <div class="step-card glass-card">
              <div class="step-number">01</div>
              <div class="step-icon-wrap"><i class="fas fa-clipboard-list"></i></div>
              <h3 class="step-title">Fill the Order Form</h3>
              <p class="step-desc">Choose your gift, fill in delivery details, and submit the form — takes under 2 minutes.</p>
            </div>
            <div class="step-card glass-card">
              <div class="step-number">02</div>
              <div class="step-icon-wrap"><i class="fab fa-whatsapp"></i></div>
              <h3 class="step-title">Confirm on WhatsApp</h3>
              <p class="step-desc">We'll send you a confirmation message on WhatsApp with your order summary and payment details.</p>
            </div>
            <div class="step-card glass-card">
              <div class="step-number">03</div>
              <div class="step-icon-wrap"><i class="fas fa-truck"></i></div>
              <h3 class="step-title">Delivered on Your Date</h3>
              <p class="step-desc">Your gift arrives on the exact date you chose — anywhere in Morocco.</p>
            </div>
          </div>
        </div>
      </div>
    </section>
```

- [ ] **Step 2: Verify in browser**

Scroll to How It Works. Expected:
- 3 glassmorphism cards side by side
- Each: large muted gold step number, icon in bordered box, Playfair title, Inter body text
- Hover on card: slight lift + brighter gold border

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add how-it-works section with glassmorphism step cards"
```

---

## Task 7: Catalog section HTML

**Files:**
- Modify: `index.html`

The catalog section must preserve all 20 gift cards exactly (data attributes, hrefs, content) for JS filter compatibility.

- [ ] **Step 1: Append catalog section opening + filters**

```html
    <!-- CATALOG -->
    <section class="catalog-section section-bg" id="gifts">
      <div class="orb-br"></div>
      <div class="section-inner">
        <div class="catalog-inner">

          <div class="catalog-header-row reveal">
            <div>
              <span class="section-label">✦ Gift Collections</span>
              <h2 class="section-title">Find the Perfect <em>Gift</em></h2>
            </div>
          </div>

          <!-- Filters -->
          <div class="filters-wrapper reveal">
            <div class="filter-group">
              <div class="filter-label">Gender</div>
              <div class="filter-buttons" id="filter-gender">
                <button class="filter-btn active" data-filter="all">All</button>
                <button class="filter-btn" data-filter="male"><i class="fas fa-mars" style="margin-right:5px;"></i> Male</button>
                <button class="filter-btn" data-filter="female"><i class="fas fa-venus" style="margin-right:5px;"></i> Female</button>
              </div>
            </div>
            <div class="filter-group">
              <div class="filter-label">Relation</div>
              <div class="filter-buttons" id="filter-relation">
                <button class="filter-btn active" data-filter="all">All</button>
                <button class="filter-btn" data-filter="parents">Parents</button>
                <button class="filter-btn" data-filter="partner">Partner</button>
                <button class="filter-btn" data-filter="friend">Friend</button>
                <button class="filter-btn" data-filter="sibling">Sibling</button>
                <button class="filter-btn" data-filter="kids">Kids</button>
              </div>
            </div>
          </div>
```

- [ ] **Step 2: Append Standard tier + all its gift cards**

Copy the entire Standard tier block from the old `index.html.bak` (lines 1793–2080) into the new file, but wrap it in the new tier structure:

```html
          <!-- STANDARD TIER -->
          <div class="catalog-tier-section standard-tier reveal">
            <div class="tier-label">
              <span class="tier-label-text">Standard Collection — 200 MAD</span>
              <div class="tier-label-line"></div>
            </div>
            <div class="gifts-grid">
              <!-- PASTE ALL STANDARD GIFT CARDS FROM index.html.bak HERE -->
              <!-- Each .gift-card div gets class="gift-card glass-card" added -->
            </div>
          </div>
```

Then copy each `<div class="gift-card" ...>` from `index.html.bak` (lines 1798–2080), changing only:
- `class="gift-card"` → `class="gift-card glass-card"`
- Keep all `data-gender`, `data-relation`, `data-tier` attributes unchanged
- Keep all `.specific-label-badge`, `.gift-image-wrapper`, `.gift-content`, `.btn-order` content unchanged

- [ ] **Step 3: Append Premium tier + all its gift cards**

Same pattern for Premium tier (lines 2081–2411 from `index.html.bak`):

```html
          <!-- PREMIUM TIER -->
          <div class="catalog-tier-section premium-tier reveal">
            <div class="tier-label">
              <span class="tier-label-text">Premium Collection — 400 MAD</span>
              <div class="tier-label-line"></div>
            </div>
            <div class="gifts-grid">
              <!-- PASTE ALL PREMIUM GIFT CARDS FROM index.html.bak HERE -->
              <!-- Each .gift-card div gets class="gift-card glass-card" added -->
            </div>
          </div>

        </div><!-- /.catalog-inner -->
      </div><!-- /.section-inner -->
    </section><!-- /.catalog-section -->
```

- [ ] **Step 4: Verify in browser**

Scroll to Catalog. Expected:
- Gold-labeled tier separators for Standard and Premium
- Gift cards in auto-fill grid, glassmorphism styling
- Premium tier: slightly warmer gold border
- Hover: card lifts, border brightens
- Filter buttons styled with gold active state (filter JS wired up in Task 11)

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add catalog section with tiered glassmorphism gift cards"
```

---

## Task 8: FAQ section HTML

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Append FAQ section**

```html
    <!-- FAQ -->
    <section class="faq-section section-bg" id="faq">
      <div class="orb-br"></div>
      <div class="section-inner">
        <div class="faq-inner">
          <div class="faq-header reveal">
            <span class="section-label">✦ Common Questions</span>
            <h2 class="section-title">Frequently Asked <em>Questions</em></h2>
          </div>

          <div class="faq-accordion reveal-group" id="faqAccordion">

            <div class="faq-item">
              <button class="faq-trigger" type="button" aria-expanded="false">
                <span class="faq-question">What is the estimated delivery date?</span>
                <span class="faq-icon"><i class="fas fa-plus"></i></span>
              </button>
              <div class="faq-panel">
                <div class="faq-panel-inner">
                  <p>You choose the delivery date.</p>
                  <p>Please order at least 2 days in advance.</p>
                </div>
              </div>
            </div>

            <div class="faq-item">
              <button class="faq-trigger" type="button" aria-expanded="false">
                <span class="faq-question">Where can I pay the advance payment?</span>
                <span class="faq-icon"><i class="fas fa-plus"></i></span>
              </button>
              <div class="faq-panel">
                <div class="faq-panel-inner">
                  <p>Custom orders require an advance payment.</p>
                  <p>After submitting the order form we will send bank transfer details on WhatsApp.</p>
                </div>
              </div>
            </div>

            <div class="faq-item">
              <button class="faq-trigger" type="button" aria-expanded="false">
                <span class="faq-question">Can I customize my own gift?</span>
                <span class="faq-icon"><i class="fas fa-plus"></i></span>
              </button>
              <div class="faq-panel">
                <div class="faq-panel-inner">
                  <p>Yes — submit the order form with your custom items and confirm on WhatsApp.</p>
                </div>
              </div>
            </div>

          </div>
        </div>
      </div>
    </section>
```

- [ ] **Step 2: Verify in browser**

Scroll to FAQ. Expected:
- Centered narrow layout (max 800px)
- 3 accordion items with glassmorphism trigger buttons
- Clicking a trigger: gold border, icon rotates 45°, panel slides open
- (Accordion JS added in Task 11)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add FAQ section with glassmorphism accordion"
```

---

## Task 9: Product view, footer, floating button, overlays HTML

**Files:**
- Modify: `index.html`

Copy the product view, footer, WhatsApp float, cart sidebar, and order modal blocks verbatim from `index.html.bak`, then close `</main>`.

- [ ] **Step 1: Append product view (copy verbatim from index.html.bak lines 2461–2535)**

```html
    <!-- PRODUCT VIEW — copy verbatim from index.html.bak lines 2461–2535 -->
    <!-- Change only: class="product-view" stays, no class changes needed -->
```

The product view section has its own complex JS-driven layout. Copy it exactly; the new CSS handles its visual styling via the `.product-*` class rules already defined in Task 2.

- [ ] **Step 2: Append closing main + footer**

```html
  </main><!-- end main -->

  <!-- FOOTER -->
  <footer class="footer-lux">
    <div class="gold-line"></div>
    <div class="footer-lux-inner">
      <div class="footer-brand">
        <div class="footer-brand-name">CADEAU LIK</div>
        <div class="footer-tagline">Made to be yours</div>
      </div>
      <div class="footer-col">
        <div class="footer-col-title">Contact</div>
        <ul class="footer-links-list">
          <li><a href="https://wa.me/212783552622" target="_blank" rel="noopener noreferrer"><i class="fab fa-whatsapp"></i> WhatsApp</a></li>
          <li><a href="https://instagram.com/aiss_a__mkf" target="_blank" rel="noopener noreferrer"><i class="fab fa-instagram"></i> Instagram</a></li>
          <li><a href="mailto:aissamkf@gmail.com"><i class="fas fa-envelope"></i> Email</a></li>
        </ul>
      </div>
      <div class="footer-col">
        <div class="footer-col-title">Location</div>
        <div class="footer-location">Morocco</div>
      </div>
    </div>
    <div class="gold-line"></div>
    <div class="footer-bottom">
      <div>&copy; 2026 CADEAU LIK. All Rights Reserved.</div>
      <div>Luxury gifts, delivered</div>
    </div>
  </footer>

  <!-- FLOATING WHATSAPP -->
  <a href="https://wa.me/212783552622" class="whatsapp-float" target="_blank" aria-label="WhatsApp Contact">
    <i class="fab fa-whatsapp"></i>
  </a>
```

- [ ] **Step 3: Append cart sidebar + order modal (copy verbatim from index.html.bak lines 2588–2781)**

These blocks contain IDs that the JS depends on. Copy them exactly — class names in the new CSS cover their styling.

- [ ] **Step 4: Verify footer in browser**

Scroll to bottom. Expected:
- Grid overlay footer
- Gold line separator (gradient left→gold→transparent→right)
- 3-column layout: brand left, Contact center, Location right
- Footer links hover gold
- WhatsApp float button fixed bottom-right

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add product view, footer, cart sidebar, order modal"
```

---

## Task 10: JavaScript — copy all scripts + add scroll reveal

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Copy all JS from index.html.bak (lines 2782–3464) verbatim**

After the cart/modal HTML, append:

```html
  <script>
    /* === All original JS from index.html.bak lines 2783–3463 pasted here === */
    /* Copy the exact content between <script> and </script> from the backup */
  </script>
```

The existing JS handles: navbar scroll, mobile menu, particles (remove particle spawn), FAQ accordion, gift card filters, product view open/close, cart, order modal, WhatsApp message building. All IDs and class hooks are preserved in the new HTML.

- [ ] **Step 2: Add IntersectionObserver for scroll reveal (append inside the same script block)**

After pasting the original JS, add:

```javascript
    // === SCROLL REVEAL — slide up from below on scroll ===
    (function() {
      const revealEls = document.querySelectorAll('.reveal, .reveal-group');
      if (!revealEls.length) return;

      const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            entry.target.classList.add('visible');
            observer.unobserve(entry.target);
          }
        });
      }, { threshold: 0.12 });

      revealEls.forEach(el => observer.observe(el));
    })();
```

- [ ] **Step 3: Remove particle spawn code**

In the original JS block, find and remove the section that creates `.particle` elements and the `createParticles()` / `initParticles()` call — the new design uses CSS glow orbs instead.

Look for: `function createParticles` or `document.getElementById('particles')` and remove those blocks.

- [ ] **Step 4: Close HTML document**

Append at the very end:

```html
</body>
</html>
```

- [ ] **Step 5: Verify scroll animations in browser**

Scroll down the page slowly. Expected:
- Hero: visible immediately (no `.reveal` class on hero)
- About, How It Works, Catalog, FAQ: each section fades in and slides up from 40px below as it enters the viewport
- Step cards (3 children of `.reveal-group`): stagger in with 0s / 0.1s / 0.2s delay

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat: add IntersectionObserver scroll-reveal animations, remove particle JS"
```

---

## Task 11: Verify all interactive features

**Files:**
- Read: `index.html` (no changes — verification only)

- [ ] **Step 1: Test filters**

Open `index.html`. In the Catalog section:
- Click "Female" gender filter → only female cards visible
- Click "Partner" relation filter → only partner cards visible
- Click "All" → all cards visible
Expected: filter logic unchanged (JS from backup handles this)

- [ ] **Step 2: Test FAQ accordion**

Click each FAQ question. Expected:
- Panel slides open (max-height transition)
- Icon rotates 45° (CSS `[aria-expanded="true"] .faq-icon`)
- Clicking again: closes
- Only one open at a time (if original JS enforces this)

- [ ] **Step 3: Test product view**

Click any "Order on WhatsApp" button on a gift card. Expected:
- Product view slides into view (or modal opens — depends on original JS)
- Back button returns to catalog
- Cart icon appears in nav header

- [ ] **Step 4: Test cart sidebar**

Add items to cart. Expected:
- Cart sidebar slides in from right
- Glassmorphism styling, gold accents
- Cart overlay dims the rest of the page

- [ ] **Step 5: Test order modal**

Proceed to checkout from cart. Expected:
- Order modal opens centered with backdrop blur
- Form fields styled with glass inputs
- WhatsApp submit button

- [ ] **Step 6: Test mobile (resize to 375px)**

Resize browser to 375px width. Expected:
- Nav links hidden, hamburger visible
- Tap hamburger: full-screen nav overlay slides in
- Hero stacks vertically (text top, video wrapper bottom)
- Step cards stack to 1 column
- Footer stacks to 1 column

- [ ] **Step 7: Final commit**

```bash
git add index.html
git commit -m "feat: complete Cadeau Lik redesign — kie.ai-inspired black & gold rebuild"
```

---

## Adding the Hero Video (later — when you have the AI-generated video)

When you have the video file from OpenAI Sora:

1. Place it in `assets/hero-animation.mp4` (and optionally `assets/hero-animation.webm` for browser compatibility)
2. In `index.html`, find `id="heroPlaceholder"` and replace with:

```html
<video autoplay loop muted playsinline>
  <source src="assets/hero-animation.mp4" type="video/mp4">
  <source src="assets/hero-animation.webm" type="video/webm">
</video>
```

3. Commit:

```bash
git add assets/hero-animation.mp4 index.html
git commit -m "feat: add hero video animation"
```

---

## Self-Review

**Spec coverage check:**
- [x] Black & gold brand retained — CSS custom properties preserve exact colors
- [x] Playfair Display headings — kept throughout all sections
- [x] kie.ai layout patterns — grid bg (`.section-bg::before`), glow orbs (`.section-bg::after` + `.orb-br`), glassmorphism (`.glass-card`)
- [x] All sections redesigned — hero, about, how-it-works, catalog, FAQ, footer
- [x] Hero split layout — 55/45 text/visual split
- [x] Hero video placeholder — slot ready for AI video
- [x] Scroll animations — IntersectionObserver `.reveal` + `.reveal-group`
- [x] All JS hooks preserved — IDs, data attributes, class names kept identical
- [x] Mobile responsive — 900px and 640px breakpoints
- [x] WhatsApp links — all hrefs preserved verbatim
- [x] Cart, product view, order modal — preserved and restyled

**Placeholder scan:** No TBDs. Task 7 instructions are clear: copy from backup and add one class. Task 10 is clear: copy verbatim.

**Type consistency:** CSS class names used in HTML match those defined in Task 2 CSS. JS class hooks (`.scrolled`, `.active`, `aria-expanded`) match CSS selectors.
