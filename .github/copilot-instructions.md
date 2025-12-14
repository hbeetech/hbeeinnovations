## Purpose
This repo is a small static multipage website for HBEE Innovations (HTML/CSS/JS). There is no build system — pages are plain HTML that load assets from `css/`, `js/` and `lib/`. The site is intended to be served as static files (GitHub Pages or any static host).

## Big picture
- Frontend-only static site. Primary entry pages: `index.html`, `store.html`, and other content pages in the repo root.
- Shared assets: `css/` (Bootstrap + custom), `js/` (small shared behaviors in `js/main.js`), and `lib/` (third-party libs such as owlcarousel, lightbox, wowjs).
- `store.html` implements a simple client-side store: it fetches `/products.json` when present and falls back to an inline `PRODUCTS_FALLBACK`. Cart is persisted to localStorage (key `hbeei_cart_v1`). Payment integration points are placeholders (see `paymentLink` and `/create-checkout-session`).

## What to change where (hotspots)
- To add or update products: edit `/products.json` (preferred) or change the fallback list inside `store.html`.
- To customize cart behavior / checkout flow: edit the inline script near the bottom of `store.html`. Key functions:
  - `loadProducts()` — loads `/products.json` or the fallback.
  - `addToCart(productId, variantKey, qty)` — mutates the `cart` array and saves to `localStorage`.
  - `saveCart()` / `updateCartUI()` — keep UI and storage in sync.
  - Checkout endpoints: `checkoutLinkBtn` builds a query for `/create-checkout-session` (placeholder). Replace with your backend endpoint.

## Data flow examples
- Products: browser -> fetch('/products.json') -> renderProducts(list). Fallback: `PRODUCTS_FALLBACK` declared inline.
- Cart: UI actions -> `cart` (in-memory) -> `localStorage` (key `hbeei_cart_v1`) -> email checkout uses `mailto:admin@hbeei.com` (pre-filled body).

## Project-specific conventions
- No bundler: keep edits to plain HTML/JS/CSS files and preserve relative paths (e.g., `css/bootstrap.min.css`, `lib/...`).
- Inline scripts: store logic lives inside `store.html` (not in `js/main.js`). When adding features, prefer extracting large JS blocks to `js/` only if you also update references in the HTML.
- Minimal server assumptions: code uses absolute path `/products.json` and relative asset paths. When testing locally, run a static server instead of opening files directly in some browsers (CORS/Fetch restrictions).

## Integration points & external IDs
- Google Tag Manager: GTM-5CCGD9G2 is included in `index.html` and `store.html`.
- Email checkout: uses `mailto:admin@hbeei.com` in `store.html` — preserve or update the address if changing business contact.
- Payment links: `paymentLink` for each product is a placeholder (e.g., `https://buy.stripe.com/test_*`). Replace with your real payment provider integration and server-side session creation at `/create-checkout-session`.

## Small examples to copy/paste
- Read products with fallback (from `store.html`):
  - Look at `loadProducts()` which tries `fetch('/products.json')` and falls back to `PRODUCTS_FALLBACK.slice()` on error.
- Preserve cart key when renaming: `const CART_KEY = 'hbeei_cart_v1'`.

## Developer workflow shortcuts
- There is no build step. To preview changes use a static server (for example `python -m http.server 8000` from the repo root) so fetch() works as expected.

## What not to change lightly
- Do not change relative asset paths in HTML unless you update all pages. Many assets assume the site root is the repo root.
- Avoid deleting the inline fallback in `store.html` until `products.json` is present and tested.

If anything above is unclear or you want example edits (e.g., extract store JS into `js/store.js`, add a backend route for `/create-checkout-session`, or add tests), tell me which part to expand and I'll update this file.
