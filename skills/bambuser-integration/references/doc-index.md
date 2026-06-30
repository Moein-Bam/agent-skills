# Documentation index (entry points)

A **short, validated** map of common starting pages per product, so you can fetch the right Markdown directly. **Every URL is the page slug with `.md` appended**, base `https://bambuser.com/docs/`.

> This is a **convenience snapshot**, not the source of truth. Slugs can change. If a page below 404s — or you need a topic not listed — fetch the site-wide index and pick from it: **`https://bambuser.com/docs/llms.txt`** (every page as a `.md` link + one-line description, grouped by product). The index is always authoritative.

## Whole-product bundles (`llms-<slug>.txt`)

When you need broad context for one product in a single fetch (each is large):

- `https://bambuser.com/docs/llms-live.txt` — Live Shopping *(slug is `live`, not `live-shopping`)*
- `https://bambuser.com/docs/llms-shoppable-video.txt` — Shoppable Video
- `https://bambuser.com/docs/llms-video-consultation.txt` — Video Consultation
- `https://bambuser.com/docs/llms-chat.txt` — Chat
- `https://bambuser.com/docs/llms-app-framework.txt` — App Framework
- `https://bambuser.com/docs/llms-full.txt` — everything (prefer `llms.txt` + targeted `.md` instead)

## Live Shopping (`/docs/live/…`)

| Topic | Page (`.md`) |
|-------|--------------|
| Initial setup (embed a show) | `live/initial-setup.md` |
| Player API reference (Web/Swift/Kotlin) | `live/player-api-reference.md` |
| Cart integration | `live/cart-integration.md` |
| Channel widget | `live/channel.md` |
| Miniplayer | `live/miniplayer.md` |
| Shopper Events tracking | `live/shopper-events-tracking.md` |
| Access analytics data | `live/access-analytics-data.md` |
| Shopify | `live/shopify-guide.md` |
| Mobile SDK — Android | `live/mobile-sdk-integration-android/setup.md` |
| Mobile SDK — iOS | `live/mobile-sdk-integration-ios/setup.md` |
| Mobile SDK — React Native | `live/mobile-sdk-integration-react-native/setup.md` |

## Shoppable Video (`/docs/shoppable-video/…`)

| Topic | Page (`.md`) |
|-------|--------------|
| `<bam-playlist>` integration guide | `shoppable-video/bam-playlist-integration.md` |
| Cart & product-data integration | `shoppable-video/cart-integration.md` |
| Distributing videos to playlists | `shoppable-video/distributing-videos.md` |
| Shopify | `shoppable-video/shopify-guide.md` |

## Video Consultation (`/docs/video-consultation/…`)

| Topic | Page (`.md`) |
|-------|--------------|
| Initial setup (Calls Widget) | `video-consultation/initial-setup.md` |
| Cart integration | `video-consultation/cart-integration.md` |
| Provide product data | `video-consultation/provide-product-data.md` |
| Co-browsing | `video-consultation/cobrowsing.md` |
| Overlay Widget | `video-consultation/overlay-widget.md` |
| Shopify | `video-consultation/shopify-guide.md` |

## Chat (`/docs/chat/…`)

| Topic | Page (`.md`) |
|-------|--------------|
| Integration guide (loader, App IDs, Product Feed) | `chat/bam-chat-integration.md` |
| Manage display (where the launcher appears) | `chat/manage-display.md` |
| Cookie requirements | `chat/cookie-requirements.md` |
| Shopify | `chat/integrate-chat-on-shopify.md` |

## App Framework — *Beta* (`/docs/app-framework/…`)

| Topic | Page (`.md`) |
|-------|--------------|
| Getting started (sandbox template) | `app-framework/getting-started.md` |
| App architecture | `app-framework/learn/app-architecture.md` |
| Screen API | `app-framework/build/api/screen.md` |
| Dialog API | `app-framework/build/api/dialog.md` |
| Tool API | `app-framework/build/api/tool.md` |

---

**REST APIs & SSO** are not listed as standalone sections — they're sub-topics referenced from the product pages above (e.g. Live Shopping stats at `liveshopping-api.bambuser.com`, SSO under each product). Find the exact page via `llms.txt`.
