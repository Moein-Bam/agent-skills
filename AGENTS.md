# Bambuser — agent context

> This file is the **fallback** for AI tools that don't support the Agent Skills standard (e.g. Windsurf, Cline, Continue). Tools that do (Claude Code, Cursor, Codex, Gemini CLI, Copilot, Amp) should use the full skill at [`skills/bambuser-integration/SKILL.md`](skills/bambuser-integration/SKILL.md) instead — it has more depth. Copy this file to your project root to give a rules‑based tool Bambuser context.

You help integrate **Bambuser** video‑commerce products. There are **five**:

| Product | What it does | Embed | Key object |
|---|---|---|---|
| **Live Shopping** (One‑to‑Many) | Live/recorded shopping shows | `initBambuserLiveShopping()` + `onBambuserLiveShoppingReady` | `player` |
| **Shoppable Video** | Short on‑demand videos with product cards | `<bam-playlist>` web component | `player` |
| **Video Consultation** (One‑to‑One) | 1:1 shopper↔agent video calls | `new BambuserOneToOneEmbed()` + `onBambuserOneToOneReady` | `oneToOneEmbed` |
| **Chat** ("Hero") | Text chat to in‑store experts | `HeroWebPluginSettings` + `hero()` | `hero` |
| **App Framework** *(Beta)* | Build custom apps/tools (e.g. VTO) | App manifest + sandbox | Screen/Dialog/Tool/VTO APIs |

Core rules:

1. **Regions.** Every workspace is **Global or EU** and they're separate environments. Don't guess — determine it from the existing embed, the login domain (`lcx.bambuser.com` = Global, `lcx-eu.bambuser.com` = EU), or **ask**. Region encoding differs per product: Live/Shoppable Video swap a **subdomain** (`lcx-embed` ↔ `lcx-embed-eu`); Video Consultation swaps an **`/eu/` path**; mobile SDKs use a **`US`/`EU` enum**; Chat selects environment via Application ID.
2. **Ready callbacks.** Define the `onBambuser…Ready` callback **before** the embed script loads (cached scripts run synchronously). Chat is the exception (command queue, no callback).
3. **Cart/product data.** Live, Shoppable Video, and Video Consultation share one model: hydrate product data, then handle add/update/checkout via the store's own endpoints, reporting back through a callback (`callback(true)` / `callback({ success:false, reason:'out-of-stock' })`). Chat is different — it uses a Product Feed + `hero("track", …)`.
4. **Fetch the latest docs — don't rely on memory.** For any exact attribute, event field, config key, host, or endpoint, fetch from `https://bambuser.com/docs`: read the index `https://bambuser.com/docs/llms.txt`, find the matching page, then fetch that page with **`.md`** appended (e.g. `https://bambuser.com/docs/live/cart-integration.md`). Treat the docs as the source of truth. (On claude.ai, allow `bambuser.com` at `claude.ai/settings/capabilities`.)
5. Never self‑host Bambuser scripts; always load from the official host. Set `currency`/`locale` when cart is involved. Pair the embed script with the init/event‑handler code.
6. **Ask, don't invent.** Recover workspace/store‑specific values from the user (or their existing embed/code) rather than guessing: data region, `orgId`/`org-id`, show/video/playlist or Chat Application IDs, and whether client‑side cart/product APIs (`getProduct`/`addToCart`/…) already exist or should be built.

For full depth, read [`skills/bambuser-integration/SKILL.md`](skills/bambuser-integration/SKILL.md) and its `references/`.
