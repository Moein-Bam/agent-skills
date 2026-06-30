# Shared cart & product-data model

Product-agnostic concepts shared by **Live Shopping**, **Shoppable Video**, and **Video Consultation**. (Chat is excluded — it uses Shopper Events + a Product Feed, not this model; see [`products.md`](products.md).)

The **exact, current payload fields** for product hydration and cart events live in the cart docs — **fetch them when you write real code** (`live/cart-integration.md`, `shoppable-video/cart-integration.md`, `video-consultation/cart-integration.md`, `video-consultation/provide-product-data.md`, `video-consultation/providing-search-data.md`; base `https://bambuser.com/docs/`). This file is the durable model, not a payload reference.

## The two jobs

1. **Hydrate product data** so the player/widget shows accurate names, prices, variants, images, and stock. The player asks for data (a "provide product data" event) and you respond by mapping your store's product into Bambuser's product object via the factory chain.
2. **Handle cart actions** — add, update/remove, checkout — by calling the store's own cart endpoints and reporting the result back through a callback.

**Dependency:** in Live/Shoppable Video, cart actions only function once product data is hydrated (no hydration ⇒ disable cart UI). In Video Consultation the cart handlers work independently of hydration.

## Event names by product

| Action | Live Shopping / Shoppable Video | Video Consultation |
|--------|---------------------------------|--------------------|
| Product data request | `player.EVENT.PROVIDE_PRODUCT_DATA` | `'provide-product-data'` |
| Add to cart | `player.EVENT.ADD_TO_CART` | `'should-add-item-to-cart'` |
| Update / remove cart item | `player.EVENT.UPDATE_ITEM_IN_CART` | `'should-update-item-in-cart'` |
| Checkout | `player.EVENT.CHECKOUT` | `'goto-checkout'` |
| Sync cart after checkout return | `player.EVENT.SYNC_CART_STATE` | — |
| Product search (agent) | — | `'provide-search-data'` |

## Product object (shape your store data maps into)

Conceptually a product has identity + description, then **variations** (e.g. colors), each with **sizes** carrying price and stock:

```
product
  name, brandName, sku, description, defaultVariationIndex
  variations[]            // dimension 1 (e.g. color)
    name, sku, imageUrls[], attributes { colorName, colorHexCode }
    sizes[]               // dimension 2 (e.g. size)
      name, sku, inStock, price { current, original, currency }
```

**Max 2 variation dimensions** (typically Color + Size). Flatten anything beyond that.

## Factory / builder chain

Hydration uses a fluent builder passed into `updateProduct`. The entry point differs per product (`player.updateProduct(...)` for Live/Shoppable Video, `oneToOneEmbed.updateProduct(...)` for Video Consultation) but the idea is the same: a `productFactory` → `.product((p) => …)` → `.variations` → `.sizes` → `.price` chain. Live/Shoppable Video also offer `.inheritFromPlaceholder()` to update only a few fields (e.g. stock) while keeping scraped values for the rest. Video Consultation's chain additionally supports top-level `.currency()`/`.locale()`, `.attributes(...)`, and `.relatedProducts(...)`.

**Why a "placeholder" exists:** Bambuser **auto-scrapes** product data from your PDP into a placeholder first (see the per-product `data-scraping.md`). Your hydration **overrides** the scraped values; `.inheritFromPlaceholder()` keeps the scraped fields you don't override. This is the key to reasoning about "prices/images don't match the dashboard" symptoms — the scrape and your hydration disagree.

Because the exact method names and optional fields evolve, **fetch the cart doc for the product you're integrating** and follow its current factory example rather than reproducing one from memory.

## Callback convention (uniform across products)

```javascript
callback(true);                                              // success
callback(false);                                             // generic failure (shows error)
callback({ success: false, reason: 'out-of-stock' });        // out-of-stock UI
callback({ success: false, reason: 'custom-error', message: 'Only 2 left' });
```

Wishlist callbacks add reasons like `'login-required'` and `'more-info-required'`.

## Product Feed alternative

Instead of handling the product-data event, a dashboard **Product Feed** (XML or CSV, via URL or SFTP, synced periodically) can supply product data. It's shared across Live, Shoppable Video, and Video Consultation (one feed per workspace). You still implement the **cart action** handlers. Chat uses its own CSV Product Feed for recommendations.

## Two classic cart footguns (fetch the troubleshooting page)

These are wired-up-but-misbehaving symptoms that are hard to self-diagnose — when you hit one, fetch the page rather than guessing:

- **Clicking a product minimizes the player / opens the PDP in a new tab** instead of opening the in-player cart → the product is being treated as a link; cart integration isn't fully taking over. See `live/troubleshooting/product-acts-as-link.md`.
- **Only one "combined variations" dropdown shows** instead of separate Color + Size → the 2-dimension variation model isn't mapped correctly. See `live/troubleshooting/combined-variations-dropdown.md` (and the Shoppable Video equivalent).

## Wiring to a specific store's endpoints (when asked to write real code)

When the request includes a website URL and asks for working integration code, the cart handlers need the store's own Add / Update / Remove / Cart-status (and, for Video Consultation, Search) endpoints.

**First, ask** whether the customer already has client-side functions for these (`getProduct`, `getCart`, `addToCart`, `updateCart`, `removeFromCart`, and `search`) and where they live — reuse them if so. Only if they don't exist, and the user wants you to build them, implement them by discovering the underlying endpoints in this order:

1. **Search the existing codebase first** — reuse the store's own cart/product calls if present (most reliable). Grep for `cart`, `basket`, `addToCart`, `/products/`, `checkout`, GraphQL operation names.
2. **Observe live traffic** with browser tooling if available: open a PDP, add to cart, change quantity, remove, open the cart — capture each request's URL, method, headers, body, and response. Only inspect sites the user asked you to integrate; never place real orders.
3. **Fall back to platform conventions** (Shopify `/cart/add.js`, `/cart/change.js`, `/cart.js`, `/products/{handle}.js`; WooCommerce Store API; Magento REST; BigCommerce; SFCC) and verify read-only calls.

Wrap the result in a small `storeApi` (`getProduct`, `getCart`, `addToCart`, `updateCart`, `removeFromCart`, and `search` for Video Consultation), then call it from the product's event handlers. The Shopify path is well-trodden — fetch the per-product `shopify-guide.md`.
