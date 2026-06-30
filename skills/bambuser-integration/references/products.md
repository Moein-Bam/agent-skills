# The five products

Durable, concept-level knowledge per product — what it is, when it's the right fit, the key objects you'll touch, and the **non-obvious things** that are easy to get wrong. **Exact embed snippets, attribute lists, event payloads, and config keys are not here** — fetch them from the product's doc pages (see [`doc-index.md`](doc-index.md) and SKILL.md → "Fetching the documentation").

---

## Live Shopping (One-to-Many)

**What it is.** One-to-many live (and recorded/VOD) video shows with interactive shopping, embedded on your site. Branded "One-to-Many" internally.

**When it's the fit.** Scheduled or always-on live broadcasts, hosted shows, event-driven selling with many viewers per host.

**Key objects & surfaces.** - `player` — the embedded show player; configured and event-driven via `player.on(player.EVENT.*)`, `player.configure(...)`, `player.updateProduct(...)`. - **Channel widget** — a no-code grid of featured / upcoming / ended shows (`onBambuserWidgetReady`). - **Floating Action Button (FAB)** — a floating entry point to shows. - **Inline player** and **Miniplayer** (a persistent overlay that survives navigation).

**Non-obvious relevances.** - Live Shopping and Shoppable Video **share the same player event API and cart model** — code and mental model transfer between them. - The Miniplayer's "survive navigation" behavior depends on how your site routes; SPAs need manual navigation handling. Check the miniplayer compatibility doc. - Shows can be surfaced on product pages via deeplinks; this is a distinct integration from the main embed. - Native mobile and WebView integrations are documented **under Live Shopping**, not as a separate product (see Mobile SDKs below).

---

## Shoppable Video

**What it is.** On-demand, short-form (<5 min) videos with product cards, embedded as a **standard HTML web component**, `<bam-playlist>`.

**When it's the fit.** Always-on shoppable video on PDPs, landing pages, or galleries; no live event needed.

**Key objects & surfaces.** - `<bam-playlist>` web component — configured via **HTML attributes** (`org-id`, `video-ids`, `autoplay`, `mode`, `player-fit`, `focus-mode`, `query`, CSS variables for styling). - Shares the `player` event API with Live Shopping for cart/product handling. - A full-screen **Video PDP** mode.

**Non-obvious relevances.** - Configuration is **attribute-driven** (declarative HTML), unlike Live Shopping's imperative `player.configure(...)` — but the cart/event handling is the same `player.EVENT.*` API. - Videos are assigned to playlists in several ways (curated, by SKU, by tag, static); the distribution method you pick changes how `<bam-playlist>` is configured. - It's a web component, so **site-speed/Lighthouse** characteristics matter; there's a dedicated optimization doc. - Has its own mobile SDKs (iOS/Android) under the Shoppable Video docs.

---

## Video Consultation (One-to-One)

**What it is.** One-to-one (and one-to-few) video calls between a shopper and a store agent, via the **Calls Widget**. Branded "One-to-One" internally; the global object is `oneToOneEmbed`.

**When it's the fit.** Clienteling, high-consideration purchases, booked or on-demand expert calls.

**Key objects & surfaces.** - `oneToOneEmbed` (from `new BambuserOneToOneEmbed()`, exposed via `onBambuserOneToOneReady`). - **Calls Widget**, **Overlay Widget**, and a **floating player / Miniplayer**. - **Queue** (on-demand) and **booking-system** integrations (e.g. Appointedd, Booxi). - **Co-browsing** so the agent and shopper can browse together.

**Non-obvious relevances.** - Cart/product handling uses the **same conceptual model** as Live/Shoppable Video but with **string event names** (`'provide-product-data'`, `'should-add-item-to-cart'`, …) instead of `player.EVENT.*` constants — and the cart handlers work **independently of** product hydration. - It additionally has **`'provide-search-data'`** so the agent can search the catalog live — a surface the other products don't have. - Booking, queue status, "surf while in queue", presenting customer data to the agent, custom booking emails, and commission tracking are all separate integration topics — scope them explicitly rather than assuming "video consultation" is one switch.

---

## Chat (a.k.a. "Hero")

**What it is.** A text-chat widget connecting shoppers to in-store experts. Internally branded "Hero"; the global object and command function are both `hero`.

**When it's the fit.** Asynchronous/synchronous text assistance, expert Q&A, conversational commerce without video.

**Key objects & surfaces.** - `window.HeroWebPluginSettings` (configuration) + the `hero("method", …)` command queue. - **Display rules** controlling where the launcher appears. - A **Product Feed** (CSV) powering recommendations, and Shopper Events for attribution.

**Non-obvious relevances.** - Chat is the **odd one out**: it does **not** use the shared player/cart model. No `PROVIDE_PRODUCT_DATA`, no factory chain. Products come from a **Product Feed**, and you integrate via tracking + display rules. - Its tracking is a **separate API** (`hero("track", …)`), and **Product View + Purchase events are required for Chat to function** (not just for analytics) — omitting them breaks attribution and recommendations. - No "ready callback" — the `hero()` command queue is safe to call immediately, so ordering relative to the loader script is not a concern (unlike the other products). - Has specific **cookie requirements**; check the cookie doc when consent tooling is involved.

---

## App Framework *(Beta)*

**What it is.** A platform for building **custom apps** that extend Bambuser surfaces — e.g. a virtual try-on (VTO) experience, custom screens, dialogs, and side-panel tools inside the player and the agent view. Not an embed; you build and publish an app against a manifest.

**When it's the fit.** Custom interactive experiences beyond what the standard players offer; VTO; bespoke agent tooling.

**Key objects & surfaces.** - An **app manifest** + sandbox template. - **Screen API** (full-area views), **Dialog API** (modal panels), **Tool API** (side-panel views in the player), and **Calls/VTO** APIs.

**Non-obvious relevances.** - It's **Beta** — flag this to customers; APIs and availability may change, so leaning on the live docs matters even more here. - It's a **build-and-publish** model (architecture, manifest, design library, publish flow), not a copy-paste snippet. Treat requests here as app development, not page embedding. - VTO has multiple provider patterns (custom provider, session tool, data via placeholder) — pick the one matching the customer's VTO vendor.

---

## Cross-cutting sub-topics (not separate products)

- **Mobile SDKs** — native iOS / Android / React Native players, plus WebView integration. Documented under **Live Shopping** (and Shoppable Video has its own mobile SDKs). Region is chosen with a `US`/`EU` **enum**, not a host swap (see [`regions.md`](regions.md)).
- **REST APIs** — per product (e.g. Live Shopping Stats / Channels API at `liveshopping-api.bambuser.com`). Referenced from the relevant product doc pages; fetch those for endpoints and scopes.
- **SSO** — SAML (Okta, Azure AD, custom IdP) and OIDC for dashboard login, documented per product.

When a request touches one of these, start from the parent product in the routing list, then fetch the specific sub-topic page from `llms.txt`.
