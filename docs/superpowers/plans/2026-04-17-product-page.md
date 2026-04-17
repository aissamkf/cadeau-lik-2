# Product Detail Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `product.html` — a luxury product detail page that gift card clicks redirect to, with a "You May Also Like" section, matching cart sidebar, and localStorage-based cart sync with index.html.

**Architecture:** Static HTML/CSS/JS, no build system. Cart state lives in `localStorage` under key `cadeauLikCart`. `product.html` reads URL params to populate content. `index.html` gift card buttons redirect instead of adding to cart directly. Both pages share the same gold/dark design system.

**Tech Stack:** Vanilla HTML, CSS, JavaScript. Google Fonts (Playfair Display + Inter). Font Awesome 6.

---

## File Map

| File | Action | Responsibility |
|------|--------|----------------|
| `product.html` | Create | Full product detail page |
| `index.html` | Modify | Redirect gift card clicks, sync cart badge from localStorage |

---

### Task 1: Create product.html skeleton with navbar

**Files:**
- Create: `product.html`

- [ ] **Step 1: Create product.html with head, fonts, and navbar**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CADEAU LIK | Gift Details</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Playfair+Display:ital,wght@0,400;0,500;0,600;0,700;1,400&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    /* ── Design tokens ── */
    :root {
      --bg: #1a1a1a;
      --gold: #d4af37;
      --gold-light: #f1c750;
      --glass-bg: rgba(212,175,55,0.06);
      --glass-border: rgba(212,175,55,0.2);
      --text-main: #fcfcfc;
      --text-muted: rgba(255,255,255,0.55);
      --font-heading: 'Playfair Display', serif;
      --font-body: 'Inter', sans-serif;
      --radius-card: 16px;
      --transition: 0.35s ease;
    }
    *, *::before, *::after { margin:0; padding:0; box-sizing:border-box; }
    html { scroll-behavior:smooth; }
    body { background:var(--bg); color:var(--text-main); font-family:var(--font-body); line-height:1.6; -webkit-font-smoothing:antialiased; }
    h1,h2,h3,h4 { font-family:var(--font-heading); font-weight:600; }
    a { text-decoration:none; color:inherit; }

    /* ── Navbar ── */
    .prod-nav {
      position:sticky; top:0; z-index:100;
      background:rgba(10,10,10,0.95); backdrop-filter:blur(12px);
      border-bottom:1px solid var(--glass-border);
      display:flex; align-items:center; justify-content:space-between;
      padding:16px 5%;
    }
    .prod-nav-back {
      font-size:0.82rem; font-weight:600; letter-spacing:1.5px;
      text-transform:uppercase; color:var(--gold);
      display:flex; align-items:center; gap:8px;
      transition:opacity 0.2s;
    }
    .prod-nav-back:hover { opacity:0.7; }
    .prod-nav-logo {
      font-family:var(--font-heading); font-size:1.1rem;
      color:var(--text-main); letter-spacing:2px;
      position:absolute; left:50%; transform:translateX(-50%);
    }
    .prod-nav-cart {
      position:relative; background:none; border:none;
      color:var(--text-main); font-size:1.2rem; cursor:pointer;
      transition:color 0.2s;
    }
    .prod-nav-cart:hover { color:var(--gold); }
    .prod-cart-badge {
      position:absolute; top:-6px; right:-8px;
      background:var(--gold); color:#000;
      font-size:0.6rem; font-weight:700; border-radius:50%;
      width:16px; height:16px; display:flex; align-items:center; justify-content:center;
    }
  </style>
</head>
<body>

  <nav class="prod-nav">
    <a href="index.html#gifts" class="prod-nav-back"><i class="fas fa-arrow-left"></i> Back to Gifts</a>
    <div class="prod-nav-logo">CADEAU LIK</div>
    <button class="prod-nav-cart" id="prodCartToggle" aria-label="Cart">
      <i class="fas fa-shopping-bag"></i>
      <span class="prod-cart-badge" id="prodCartCount">0</span>
    </button>
  </nav>

</body>
</html>
```

- [ ] **Step 2: Verify in browser**
Open `product.html` directly. Should show: dark sticky navbar, "← BACK TO GIFTS" in gold left, "CADEAU LIK" centered, cart icon right.

- [ ] **Step 3: Commit**
```bash
cd "/Users/aissa/Desktop/cadeau lik"
git add product.html
git commit -m "feat: product page — navbar skeleton"
```

---

### Task 2: Add two-column product detail layout CSS + HTML

**Files:**
- Modify: `product.html`

- [ ] **Step 1: Add product detail CSS inside `<style>`**

Add after the navbar CSS block:

```css
    /* ── Product layout ── */
    .prod-section {
      max-width:1200px; margin:0 auto; padding:60px 5%;
      display:grid; grid-template-columns:1fr 1fr; gap:60px; align-items:start;
    }
    @media (max-width:768px) {
      .prod-section { grid-template-columns:1fr; gap:32px; padding:32px 5%; }
    }

    /* Left — image */
    .prod-image-wrap {
      position:relative; background:#111; border-radius:var(--radius-card);
      overflow:hidden; aspect-ratio:1/1;
      display:flex; align-items:center; justify-content:center;
    }
    .prod-image-wrap img { width:100%; height:100%; object-fit:contain; display:block; }
    .prod-image-placeholder { font-size:5rem; color:var(--gold); }
    .prod-image-badge {
      position:absolute; top:16px; left:16px;
      background:rgba(0,0,0,0.7); border:1px solid var(--glass-border);
      color:var(--gold); font-size:0.62rem; font-weight:700;
      letter-spacing:2px; text-transform:uppercase;
      padding:5px 12px; border-radius:50px; backdrop-filter:blur(6px);
    }

    /* Right — details */
    .prod-details { display:flex; flex-direction:column; gap:24px; }
    .prod-pills { display:flex; gap:8px; flex-wrap:wrap; }
    .prod-pill {
      border:1px solid rgba(212,175,55,0.4); color:var(--gold);
      font-size:0.62rem; font-weight:700; letter-spacing:2px; text-transform:uppercase;
      padding:4px 14px; border-radius:50px;
    }
    .prod-pill.premium { background:rgba(124,58,237,0.15); border-color:rgba(124,58,237,0.5); color:#c4b5fd; }

    .prod-title {
      font-family:var(--font-heading); font-size:clamp(1.8rem,3.5vw,2.6rem);
      line-height:1.2; color:var(--text-main);
    }
    .prod-title em { color:var(--gold); font-style:italic; }

    .prod-desc {
      font-size:0.92rem; color:var(--text-muted); line-height:1.8;
      border-left:3px solid var(--gold); padding-left:16px;
    }

    /* Items inside */
    .prod-inside-label {
      font-size:0.68rem; letter-spacing:3px; text-transform:uppercase;
      color:var(--gold); font-weight:600;
    }
    .prod-items-grid {
      display:grid; grid-template-columns:1fr 1fr; gap:10px;
    }
    .prod-item-card {
      background:rgba(255,255,255,0.04); border:1px solid rgba(212,175,55,0.15);
      border-radius:8px; padding:10px 14px;
      display:flex; align-items:center; gap:10px;
      font-size:0.82rem; color:var(--text-muted);
    }
    .prod-item-card i { color:var(--gold); font-size:0.75rem; }

    /* Price */
    .prod-price-row { display:flex; align-items:baseline; gap:8px; }
    .prod-price-num {
      font-family:var(--font-heading); font-size:2.4rem; font-weight:700; color:#fff;
    }
    .prod-price-unit { font-size:0.9rem; color:var(--text-muted); }
    .prod-cod-note { font-size:0.78rem; color:var(--text-muted); margin-top:-16px; }

    /* Qty */
    .prod-qty-row { display:flex; align-items:center; gap:12px; }
    .prod-qty-label { font-size:0.78rem; font-weight:600; letter-spacing:1px; text-transform:uppercase; color:var(--text-muted); }
    .prod-qty-btn {
      width:32px; height:32px; border-radius:50%;
      border:1.5px solid rgba(212,175,55,0.4); background:transparent;
      color:var(--text-main); font-size:1rem; cursor:pointer;
      display:flex; align-items:center; justify-content:center;
      transition:border-color 0.2s, background 0.2s;
    }
    .prod-qty-btn:hover { border-color:var(--gold); background:rgba(212,175,55,0.08); }
    .prod-qty-num { font-size:1rem; font-weight:600; min-width:24px; text-align:center; }

    /* Add to cart button */
    .prod-add-btn {
      display:flex; align-items:center; justify-content:center; gap:10px;
      width:100%; padding:15px; border-radius:10px;
      background:#fff; border:none; color:#000;
      font-family:var(--font-body); font-size:0.88rem; font-weight:700;
      letter-spacing:1.5px; text-transform:uppercase;
      cursor:pointer; transition:opacity 0.2s, transform 0.2s, box-shadow 0.2s;
    }
    .prod-add-btn:hover { opacity:0.9; transform:translateY(-2px); box-shadow:0 6px 20px rgba(255,255,255,0.15); }
```

- [ ] **Step 2: Add the product HTML section after the `<nav>`**

```html
  <!-- PRODUCT DETAIL -->
  <section class="prod-section" id="prodSection">
    <!-- Left: image -->
    <div class="prod-image-wrap">
      <img id="prodImg" src="" alt="">
      <div class="prod-image-placeholder" id="prodImgPlaceholder" style="display:none;"><i class="fas fa-gift"></i></div>
      <div class="prod-image-badge" id="prodBadge"></div>
    </div>

    <!-- Right: details -->
    <div class="prod-details">
      <div class="prod-pills" id="prodPills"></div>
      <h1 class="prod-title" id="prodTitle"></h1>
      <p class="prod-desc" id="prodDesc"></p>
      <div class="prod-inside-label">✦ What's Inside This Box</div>
      <div class="prod-items-grid" id="prodItemsGrid"></div>
      <div>
        <div class="prod-price-row">
          <span class="prod-price-num" id="prodPriceNum"></span>
          <span class="prod-price-unit">DH</span>
        </div>
        <div class="prod-cod-note">Cash on delivery available</div>
      </div>
      <div class="prod-qty-row">
        <span class="prod-qty-label">Qty</span>
        <button class="prod-qty-btn" id="prodQtyMinus">−</button>
        <span class="prod-qty-num" id="prodQtyNum">1</span>
        <button class="prod-qty-btn" id="prodQtyPlus">+</button>
      </div>
      <button class="prod-add-btn" id="prodAddBtn"><i class="fas fa-gift"></i> Buy Gift</button>
    </div>
  </section>
```

- [ ] **Step 3: Verify layout in browser**
Open `product.html` — should show two empty columns on dark background, image area on left, placeholders on right.

- [ ] **Step 4: Commit**
```bash
git add product.html
git commit -m "feat: product page — two-column layout CSS and HTML"
```

---

### Task 3: Add JS to read URL params and populate the page

**Files:**
- Modify: `product.html`

- [ ] **Step 1: Add JS before `</body>`**

```html
  <script>
    // ── URL Params ──
    const params = new URLSearchParams(window.location.search);
    const name    = params.get('name')     || 'Gift Box';
    const tier    = params.get('tier')     || 'standard';
    const price   = params.get('price')    || '200';
    const image   = params.get('image')    || '';
    const relation= params.get('relation') || '';
    const gender  = params.get('gender')   || 'him';

    const ITEMS = {
      standard: ['Luxury Gift Wrap','Artisan Chocolates','Premium Flowers','Scented Candle','Personal Card'],
      premium:  ['Luxury Gift Wrap','Artisan Chocolates','Premium Flowers','Scented Candle','Personal Card','Premium Perfume','Silk Ribbon','Gold Keepsake Box'],
    };

    // ── Populate image ──
    const prodImg = document.getElementById('prodImg');
    const prodImgPlaceholder = document.getElementById('prodImgPlaceholder');
    if (image) {
      prodImg.src = image;
      prodImg.alt = name;
      prodImg.onerror = () => { prodImg.style.display='none'; prodImgPlaceholder.style.display='flex'; };
    } else {
      prodImg.style.display = 'none';
      prodImgPlaceholder.style.display = 'flex';
    }

    // ── Badge ──
    document.getElementById('prodBadge').textContent = name.toUpperCase();

    // ── Pills ──
    const tierLabel = tier === 'premium' ? 'Premium' : 'Standard';
    document.getElementById('prodPills').innerHTML = `
      <span class="prod-pill">${relation.toUpperCase()}</span>
      <span class="prod-pill ${tier}">${tierLabel}</span>
    `;

    // ── Title ──
    document.getElementById('prodTitle').innerHTML = `${tierLabel} — <em>${relation.charAt(0).toUpperCase()+relation.slice(1)}</em> Gift Box`;

    // ── Description ──
    const descs = {
      father:'A beautifully arranged luxury gift box perfectly suited for your father.',
      husband:'An elegant gift box curated with love for your husband or boyfriend.',
      friend:'A thoughtfully selected gift box designed to delight your friend.',
      mother:'A beautiful and thoughtfully arranged gift box perfectly suited for your mother.',
      wife:'A romantic and luxurious gift box crafted for your wife or girlfriend.',
    };
    document.getElementById('prodDesc').textContent = descs[relation] || 'A curated luxury gift box for someone special.';

    // ── Items ──
    const items = ITEMS[tier] || ITEMS.standard;
    document.getElementById('prodItemsGrid').innerHTML = items.map(item =>
      `<div class="prod-item-card"><i class="fas fa-star"></i>${item}</div>`
    ).join('');

    // ── Price ──
    document.getElementById('prodPriceNum').textContent = price;

    // ── Qty ──
    let qty = 1;
    document.getElementById('prodQtyMinus').addEventListener('click', () => {
      if (qty > 1) { qty--; document.getElementById('prodQtyNum').textContent = qty; }
    });
    document.getElementById('prodQtyPlus').addEventListener('click', () => {
      if (qty < 10) { qty++; document.getElementById('prodQtyNum').textContent = qty; }
    });
  </script>
```

- [ ] **Step 2: Verify with URL params**
Open: `product.html?name=Father+%E2%80%94+Standard&tier=standard&price=200&image=assets/father-standard.png&relation=father&gender=him`

Should show: father-standard image, "FATHER — STANDARD BOX" badge, pills "FATHER" + "STANDARD", title "Standard — Father Gift Box", description about father, 5 item cards, price "200 DH".

- [ ] **Step 3: Commit**
```bash
git add product.html
git commit -m "feat: product page — URL param population"
```

---

### Task 4: Add localStorage cart + cart sidebar to product.html

**Files:**
- Modify: `product.html`

- [ ] **Step 1: Add cart sidebar CSS inside `<style>`**

```css
    /* ── Cart sidebar (Shopify-style) ── */
    .cart-overlay { position:fixed; inset:0; background:rgba(0,0,0,0.6); backdrop-filter:blur(4px); z-index:1999; display:none; }
    .cart-overlay.active { display:block; }
    .cart-sidebar {
      position:fixed; top:0; right:0; width:420px; height:100vh; background:#fff;
      box-shadow:-4px 0 30px rgba(0,0,0,0.15); z-index:2000;
      display:flex; flex-direction:column;
      transform:translateX(100%); transition:transform 0.25s ease-out; will-change:transform;
    }
    .cart-sidebar.open { transform:translateX(0); }
    @media (max-width:768px) { .cart-sidebar { width:100%; } }
    .cart-header { padding:20px; border-bottom:1px solid #e5e5e5; display:flex; justify-content:space-between; align-items:center; flex-shrink:0; }
    .cart-header h3 { font-family:'Inter',sans-serif; font-size:1rem; font-weight:700; color:#111; text-transform:uppercase; letter-spacing:0.5px; }
    .cart-close-btn { background:none; border:none; color:#111; font-size:1.1rem; cursor:pointer; transition:opacity 0.2s; }
    .cart-close-btn:hover { opacity:0.5; }
    .cart-col-headers { display:flex; justify-content:space-between; padding:10px 20px; border-bottom:1px solid #e5e5e5; flex-shrink:0; }
    .cart-col-label { font-size:0.68rem; font-weight:600; letter-spacing:2px; text-transform:uppercase; color:#999; }
    .cart-items { flex:1; overflow-y:auto; padding:0 20px; }
    .cart-item-row { display:flex; gap:14px; align-items:flex-start; padding:16px 0; border-bottom:2px solid #000; }
    .cart-item-thumb { width:90px; height:90px; border-radius:10px; overflow:hidden; background:#f5f5f5; flex-shrink:0; }
    .cart-item-thumb img { width:100%; height:100%; object-fit:cover; display:block; }
    .cart-item-info { flex:1; display:flex; flex-direction:column; gap:4px; }
    .cart-item-name { font-size:0.9rem; font-weight:700; color:#111; }
    .cart-item-price { font-size:0.85rem; color:#555; }
    .cart-item-controls { display:flex; align-items:center; gap:8px; margin-top:8px; }
    .cart-qty-btn { width:28px; height:28px; border-radius:50%; border:1.5px solid #ddd; background:#fff; color:#111; font-size:0.9rem; font-weight:600; cursor:pointer; display:flex; align-items:center; justify-content:center; transition:border-color 0.2s; }
    .cart-qty-btn:hover { border-color:#111; }
    .cart-qty-num { font-size:0.9rem; font-weight:600; min-width:20px; text-align:center; color:#111; }
    .cart-delete-btn { margin-left:6px; background:none; border:none; color:#bbb; cursor:pointer; font-size:0.95rem; transition:color 0.2s; }
    .cart-delete-btn:hover { color:#e00; }
    .cart-item-total { font-size:0.9rem; font-weight:700; color:#111; white-space:nowrap; flex-shrink:0; padding-top:2px; }
    .cart-footer-bar { padding:20px; border-top:1px solid #e5e5e5; flex-shrink:0; background:#fff; margin-top:-12px; }
    .cart-total-row { display:flex; justify-content:space-between; font-size:1rem; font-weight:700; color:#111; margin-bottom:14px; }
    .cart-checkout-btn {
      width:100%; background:#25d366; border:none; color:#fff;
      border-radius:8px; padding:15px; font-family:'Inter',sans-serif;
      font-size:0.88rem; font-weight:700; letter-spacing:1px; text-transform:uppercase;
      cursor:pointer; display:flex; align-items:center; justify-content:center; gap:10px;
      transition:opacity 0.2s, box-shadow 0.2s;
    }
    .cart-checkout-btn:hover { opacity:0.9; box-shadow:0 6px 20px rgba(37,211,102,0.35); }
    .cart-checkout-btn:disabled { opacity:0.35; cursor:not-allowed; }
    .cart-checkout-btn .fa-whatsapp { color:#fff; font-size:1.1rem; }
```

- [ ] **Step 2: Add cart sidebar HTML before `</body>`**

```html
  <!-- CART -->
  <div class="cart-overlay" id="cartOverlay"></div>
  <div class="cart-sidebar" id="cartSidebar">
    <div class="cart-header">
      <h3>Your Cart <span id="cartHeaderCount"></span></h3>
      <button class="cart-close-btn" id="cartClose"><i class="fas fa-times"></i></button>
    </div>
    <div class="cart-col-headers">
      <span class="cart-col-label">Product</span>
      <span class="cart-col-label">Total</span>
    </div>
    <div class="cart-items" id="cartItems"></div>
    <div class="cart-footer-bar">
      <div class="cart-total-row">
        <span>Total</span>
        <span>Dh <span id="cartTotalAmt">0</span> MAD</span>
      </div>
      <button class="cart-checkout-btn" id="cartCheckout" disabled>
        <i class="fab fa-whatsapp"></i> Order All on WhatsApp
      </button>
    </div>
  </div>
```

- [ ] **Step 3: Add cart JS (localStorage-backed) in `<script>` after the existing script block**

```html
  <script>
    // ── Cart (localStorage) ──
    const CART_KEY = 'cadeauLikCart';
    let cart = JSON.parse(localStorage.getItem(CART_KEY) || '[]');

    function saveCart() { localStorage.setItem(CART_KEY, JSON.stringify(cart)); }

    function openCart() {
      document.getElementById('cartSidebar').classList.add('open');
      document.getElementById('cartOverlay').classList.add('active');
    }
    function closeCart() {
      document.getElementById('cartSidebar').classList.remove('open');
      document.getElementById('cartOverlay').classList.remove('active');
    }

    function updateCartUI() {
      const total = cart.reduce((s, i) => s + i.price * i.qty, 0);
      const count = cart.reduce((s, i) => s + i.qty, 0);
      document.getElementById('prodCartCount').textContent = count;
      document.getElementById('cartHeaderCount').textContent = count ? `(${count})` : '';
      document.getElementById('cartTotalAmt').textContent = total;
      document.getElementById('cartCheckout').disabled = cart.length === 0;
      document.getElementById('cartItems').innerHTML = cart.length ? cart.map((item, i) => `
        <div class="cart-item-row">
          <div class="cart-item-thumb">
            ${item.img ? `<img src="${item.img}" alt="${item.name}">` : '<div style="width:100%;height:100%;display:flex;align-items:center;justify-content:center;font-size:2rem;">🎁</div>'}
          </div>
          <div class="cart-item-info">
            <div class="cart-item-name">${item.name}</div>
            <div class="cart-item-price">${item.price} DH</div>
            <div class="cart-item-controls">
              <button class="cart-qty-btn" onclick="changeQty(${i},-1)">−</button>
              <span class="cart-qty-num">${item.qty}</span>
              <button class="cart-qty-btn" onclick="changeQty(${i},1)">+</button>
              <button class="cart-delete-btn" onclick="removeItem(${i})"><i class="fas fa-trash-can"></i></button>
            </div>
          </div>
          <div class="cart-item-total">${item.price * item.qty} DH</div>
        </div>
      `).join('') : '<div style="text-align:center;color:#aaa;padding:60px 0;font-size:0.9rem;">Your cart is empty</div>';
    }

    function changeQty(i, delta) { cart[i].qty += delta; if (cart[i].qty <= 0) cart.splice(i, 1); saveCart(); updateCartUI(); }
    function removeItem(i) { cart.splice(i, 1); saveCart(); updateCartUI(); }

    document.getElementById('cartClose').addEventListener('click', closeCart);
    document.getElementById('cartOverlay').addEventListener('click', closeCart);
    document.getElementById('prodCartToggle').addEventListener('click', openCart);

    document.getElementById('cartCheckout').addEventListener('click', () => {
      if (!cart.length) return;
      const msg = cart.map(i => `• ${i.name} x${i.qty} — ${i.price * i.qty} DH`).join('\n');
      const total = cart.reduce((s, i) => s + i.price * i.qty, 0);
      window.open(`https://wa.me/212783552622?text=${encodeURIComponent(`Hello, I'd like to order:\n\n${msg}\n\nTotal: ${total} DH`)}`, '_blank');
    });

    // ── Add to cart from product page ──
    document.getElementById('prodAddBtn').addEventListener('click', () => {
      const existing = cart.find(i => i.name === name);
      if (existing) { existing.qty += qty; }
      else { cart.push({ name, badge: tierLabel, price: parseInt(price), qty, img: image }); }
      saveCart();
      updateCartUI();
      openCart();
    });

    updateCartUI();
  </script>
```

- [ ] **Step 4: Verify cart works**
Open product page with params → click "Buy Gift" → cart sidebar slides in showing the product with thumbnail → click + / − → click trash → total updates. Refresh page → cart count badge still shows correct count.

- [ ] **Step 5: Commit**
```bash
git add product.html
git commit -m "feat: product page — cart sidebar with localStorage sync"
```

---

### Task 5: Add "You May Also Like" section

**Files:**
- Modify: `product.html`

- [ ] **Step 1: Add CSS inside `<style>`**

```css
    /* ── You May Also Like ── */
    .ymal-section { padding:80px 5%; background:#111; }
    .ymal-header { text-align:center; margin-bottom:48px; }
    .ymal-title { font-family:var(--font-heading); font-size:clamp(1.8rem,3vw,2.5rem); color:var(--text-main); }
    .ymal-title em { color:var(--gold); font-style:italic; }

    .ymal-wrapper { position:relative; }
    .ymal-track {
      display:flex; gap:24px; overflow-x:auto; scroll-snap-type:x mandatory;
      scrollbar-width:none; padding-bottom:8px;
    }
    .ymal-track::-webkit-scrollbar { display:none; }

    .ymal-card {
      flex:0 0 260px; scroll-snap-align:start;
      background:#1a1a1a; border-radius:14px; overflow:hidden;
      border:1px solid var(--glass-border); cursor:pointer;
      transition:transform 0.25s, box-shadow 0.25s, border-color 0.25s;
    }
    .ymal-card:hover { transform:translateY(-4px); box-shadow:0 12px 36px rgba(0,0,0,0.5); border-color:rgba(212,175,55,0.4); }
    .ymal-card-img { width:100%; height:220px; background:#111; overflow:hidden; display:flex; align-items:center; justify-content:center; }
    .ymal-card-img img { width:100%; height:100%; object-fit:cover; filter:grayscale(65%); transition:filter 0.4s, transform 0.4s; }
    .ymal-card:hover .ymal-card-img img { filter:grayscale(0%); transform:scale(1.06); }
    .ymal-card-body { padding:16px; display:flex; flex-direction:column; gap:8px; background:linear-gradient(180deg,#111,#0d0d0d); border-top:1px solid rgba(212,175,55,0.18); }
    .ymal-card-badge { font-size:0.6rem; font-weight:700; letter-spacing:2px; text-transform:uppercase; padding:3px 10px; border-radius:50px; width:fit-content; }
    .ymal-card-badge.standard { background:rgba(255,255,255,0.06); color:rgba(255,255,255,0.55); border:1px solid rgba(255,255,255,0.1); }
    .ymal-card-badge.premium { background:linear-gradient(135deg,rgba(124,58,237,0.3),rgba(139,92,246,0.15)); color:#c4b5fd; border:1px solid rgba(124,58,237,0.5); }
    .ymal-card-name { font-size:0.9rem; font-weight:600; color:var(--text-main); }
    .ymal-card-price { font-family:var(--font-heading); font-size:1.2rem; font-weight:700; color:#fff; }
    .ymal-view-btn {
      display:flex; align-items:center; justify-content:center; gap:6px;
      background:#fff; border:none; color:#000; border-radius:8px; padding:10px;
      font-family:'Inter',sans-serif; font-size:0.75rem; font-weight:700;
      letter-spacing:1px; text-transform:uppercase; cursor:pointer;
      transition:opacity 0.2s; margin-top:4px; text-decoration:none;
    }
    .ymal-view-btn:hover { opacity:0.85; }

    /* Arrow buttons (desktop only) */
    .ymal-arrow {
      position:absolute; top:50%; transform:translateY(-50%);
      background:rgba(0,0,0,0.7); border:1px solid var(--glass-border);
      color:var(--gold); width:40px; height:40px; border-radius:50%;
      display:flex; align-items:center; justify-content:center;
      cursor:pointer; z-index:2; transition:background 0.2s;
    }
    .ymal-arrow:hover { background:rgba(212,175,55,0.15); }
    .ymal-arrow-left { left:-20px; }
    .ymal-arrow-right { right:-20px; }
    @media (max-width:768px) { .ymal-arrow { display:none; } }
    @media (min-width:769px) { .ymal-track { overflow-x:hidden; } }
```

- [ ] **Step 2: Add "You May Also Like" HTML after `</section>` (product detail section)**

```html
  <!-- YOU MAY ALSO LIKE -->
  <section class="ymal-section">
    <div class="ymal-header">
      <h2 class="ymal-title">You May Also <em>Like</em></h2>
    </div>
    <div class="ymal-wrapper">
      <button class="ymal-arrow ymal-arrow-left" id="ymalLeft"><i class="fas fa-chevron-left"></i></button>
      <div class="ymal-track" id="ymalTrack"></div>
      <button class="ymal-arrow ymal-arrow-right" id="ymalRight"><i class="fas fa-chevron-right"></i></button>
    </div>
  </section>
```

- [ ] **Step 3: Add "You May Also Like" JS — add inside first `<script>` block, after the price population**

```js
    // ── You May Also Like ──
    const ALL_PRODUCTS = [
      { name:'Father — Standard', tier:'standard', price:200, image:'assets/father-standard.png', relation:'father', gender:'him' },
      { name:'Father — Premium',  tier:'premium',  price:400, image:'assets/father-premium.png',  relation:'father', gender:'him' },
      { name:'Husband — Standard',tier:'standard', price:200, image:'assets/husband-standard.png',relation:'husband',gender:'him' },
      { name:'Husband — Premium', tier:'premium',  price:400, image:'assets/husband-premium.png', relation:'husband',gender:'him' },
      { name:'Friend — Standard', tier:'standard', price:200, image:'assets/friend-standard.png', relation:'friend', gender:'him' },
      { name:'Friend — Premium',  tier:'premium',  price:400, image:'assets/friend-premium.png',  relation:'friend', gender:'him' },
      { name:'Mother — Standard', tier:'standard', price:200, image:'assets/father-standard.png', relation:'mother', gender:'her' },
      { name:'Mother — Premium',  tier:'premium',  price:400, image:'assets/father-premium.png',  relation:'mother', gender:'her' },
      { name:'Wife — Standard',   tier:'standard', price:200, image:'assets/husband-standard.png',relation:'wife',   gender:'her' },
      { name:'Wife — Premium',    tier:'premium',  price:400, image:'assets/husband-premium.png', relation:'wife',   gender:'her' },
      { name:'Friend — Standard (Her)', tier:'standard', price:200, image:'assets/friend-standard.png', relation:'friend', gender:'her' },
      { name:'Friend — Premium (Her)',  tier:'premium',  price:400, image:'assets/friend-premium.png',  relation:'friend', gender:'her' },
    ];

    const related = ALL_PRODUCTS.filter(p => p.gender === gender && p.name !== name);
    const ymalTrack = document.getElementById('ymalTrack');
    ymalTrack.innerHTML = related.map(p => {
      const url = `product.html?name=${encodeURIComponent(p.name)}&tier=${p.tier}&price=${p.price}&image=${encodeURIComponent(p.image)}&relation=${p.relation}&gender=${p.gender}`;
      return `
        <div class="ymal-card">
          <div class="ymal-card-img"><img src="${p.image}" alt="${p.name}" onerror="this.parentElement.innerHTML='<div style=font-size:3rem;color:#d4af37>🎁</div>'"></div>
          <div class="ymal-card-body">
            <span class="ymal-card-badge ${p.tier}">${p.tier.charAt(0).toUpperCase()+p.tier.slice(1)}</span>
            <div class="ymal-card-name">${p.name}</div>
            <div class="ymal-card-price">${p.price} <span style="font-size:0.7rem;color:var(--text-muted)">DH</span></div>
            <a class="ymal-view-btn" href="${url}"><i class="fas fa-eye"></i> View Details</a>
          </div>
        </div>`;
    }).join('');

    // Arrow nav (desktop)
    const track = document.getElementById('ymalTrack');
    document.getElementById('ymalLeft').addEventListener('click', () => { track.scrollBy({ left: -284, behavior:'smooth' }); });
    document.getElementById('ymalRight').addEventListener('click', () => { track.scrollBy({ left: 284, behavior:'smooth' }); });
```

- [ ] **Step 4: Verify**
Open product page with `gender=him&relation=father` — should show 4 cards (Husband Standard, Husband Premium, Friend Standard, Friend Premium). Click "View Details" on one → redirects to product.html with that product's data populated.

- [ ] **Step 5: Commit**
```bash
git add product.html
git commit -m "feat: product page — You May Also Like section"
```

---

### Task 6: Add footer to product.html

**Files:**
- Modify: `product.html`
- Reference: `index.html` footer (lines ~1540–1580)

- [ ] **Step 1: Add footer CSS inside `<style>`**

```css
    /* ── Footer (matches index.html) ── */
    footer { background:#080808; padding:60px 6% 30px; }
    .footer-lux-inner { display:grid; grid-template-columns:2fr 1fr 1fr 1fr; gap:40px; margin-bottom:40px; }
    @media (max-width:768px) { .footer-lux-inner { grid-template-columns:1fr 1fr; } }
    .footer-brand-name { font-family:var(--font-heading); font-size:1.4rem; color:var(--text-main); letter-spacing:2px; margin-bottom:10px; }
    .footer-brand-tagline { font-size:0.82rem; color:var(--text-muted); line-height:1.7; max-width:240px; }
    .footer-socials { display:flex; gap:12px; margin-top:20px; }
    .footer-social-icon { width:36px; height:36px; background:var(--glass-bg); border:1px solid var(--glass-border); border-radius:50%; display:flex; align-items:center; justify-content:center; color:var(--text-muted); font-size:0.85rem; transition:border-color 0.2s, color 0.2s; }
    .footer-social-icon:hover { border-color:var(--gold); color:var(--gold); }
    .footer-col-title { font-size:0.68rem; letter-spacing:3px; text-transform:uppercase; color:var(--gold); margin-bottom:16px; }
    .footer-links { list-style:none; display:flex; flex-direction:column; gap:10px; }
    .footer-links a { font-size:0.85rem; color:var(--text-muted); transition:color 0.2s; }
    .footer-links a:hover { color:var(--text-main); }
    .footer-location { font-size:0.85rem; color:var(--text-muted); }
    .gold-line { height:1px; background:linear-gradient(90deg,transparent,var(--gold),transparent); margin:0 0 24px; }
    .footer-bottom { display:flex; justify-content:space-between; font-size:0.78rem; color:var(--text-muted); flex-wrap:wrap; gap:8px; }
```

- [ ] **Step 2: Add footer HTML after the ymal section, before `</body>`**

```html
  <!-- FOOTER -->
  <footer>
    <div class="footer-lux-inner">
      <div class="footer-col">
        <div class="footer-brand-name">CADEAU LIK</div>
        <div class="footer-brand-tagline">Premium gift boxes crafted for every occasion. Luxury delivered to your door.</div>
        <div class="footer-socials">
          <a href="https://www.instagram.com/cadeau.lik/" class="footer-social-icon" target="_blank"><i class="fab fa-instagram"></i></a>
          <a href="https://wa.me/212783552622" class="footer-social-icon" target="_blank"><i class="fab fa-whatsapp"></i></a>
        </div>
      </div>
      <div class="footer-col">
        <div class="footer-col-title">Navigate</div>
        <ul class="footer-links">
          <li><a href="index.html">Home</a></li>
          <li><a href="index.html#gifts">Gifts</a></li>
          <li><a href="index.html#about">About</a></li>
          <li><a href="index.html#faq">FAQ</a></li>
        </ul>
      </div>
      <div class="footer-col">
        <div class="footer-col-title">Contact</div>
        <ul class="footer-links">
          <li><a href="https://wa.me/212783552622" target="_blank">WhatsApp</a></li>
          <li><a href="https://www.instagram.com/cadeau.lik/" target="_blank">Instagram</a></li>
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
```

- [ ] **Step 3: Verify**
Footer should display at the bottom matching the index.html footer style.

- [ ] **Step 4: Commit**
```bash
git add product.html
git commit -m "feat: product page — footer"
```

---

### Task 7: Update index.html — redirect gift card clicks to product.html + localStorage cart badge

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace `addToCartFromTier` with `goToProduct` in JS**

Find and replace the `addToCartFromTier` function in index.html:

```js
    function goToProduct(btn) {
      const card = btn.closest('.tier-card');
      const name  = card.dataset.name;
      const price = card.dataset.price;
      const tier  = parseInt(price) === 400 ? 'premium' : 'standard';
      const img   = card.querySelector('.tier-card-img img');
      const image = img ? img.getAttribute('src') : '';
      // Derive relation and gender from name
      const nameLower = name.toLowerCase();
      let relation = 'friend';
      if (nameLower.includes('father')) relation = 'father';
      else if (nameLower.includes('husband')) relation = 'husband';
      else if (nameLower.includes('mother')) relation = 'mother';
      else if (nameLower.includes('wife')) relation = 'wife';
      const gender = (nameLower.includes('mother') || nameLower.includes('wife')) ? 'her' : 'him';
      const url = `product.html?name=${encodeURIComponent(name)}&tier=${tier}&price=${price}&image=${encodeURIComponent(image)}&relation=${relation}&gender=${gender}`;
      window.location.href = url;
    }
```

- [ ] **Step 2: Replace all `onclick="addToCartFromTier(this)"` with `onclick="goToProduct(this)"`**

Run this replacement across all 12 tier card buttons in index.html.

- [ ] **Step 3: Update cart badge on index.html to read from localStorage on load**

Find the `updateCart` function in index.html. At the top of the script section, add cart hydration from localStorage:

```js
    const CART_KEY = 'cadeauLikCart';
    let cart = JSON.parse(localStorage.getItem(CART_KEY) || '[]');
```

And update `updateCart` to also call `localStorage.setItem(CART_KEY, JSON.stringify(cart))` when the cart changes. Find these lines:
```js
    function updateCart() {
```
Add at the start of the function body:
```js
      localStorage.setItem(CART_KEY, JSON.stringify(cart));
```

- [ ] **Step 4: Verify full flow**
1. Open index.html → click "Buy Gift" on Father Standard → redirects to product.html with correct params
2. On product.html → click "Buy Gift" → cart opens, shows Father Standard with thumbnail
3. Go back to index.html → cart badge shows 1 item

- [ ] **Step 5: Commit**
```bash
git add index.html
git commit -m "feat: index — redirect gift cards to product page, sync cart via localStorage"
```

---

## Done ✓

The full flow: gift card click → product.html with full details → add to cart → localStorage sync → back to index with badge updated.
