# Data regions: Global vs EU

Every Bambuser workspace is provisioned in **exactly one region — Global or EU** — and the two regions are **separate environments** (separate data, separate hosts). This is the single most common cause of "player won't load", "show not found", and "data not appearing" issues, because the wrong-region embed loads but silently points at an environment where the workspace doesn't exist.

> **Treat the hosts below as a snapshot.** They were correct when written; embed hosts can change. Confirm against the product's live setup/embed doc page (`…initial-setup.md` / `…bam-playlist-integration.md` / `…bam-chat-integration.md`) before shipping. SKILL.md → "Fetching the documentation" explains how.

## Step 1 — Determine the region (don't guess)

In priority order:

1. **Existing embed snippet** — look at the host already in use (see the patterns below).
2. **Workspace / login (BamHub) domain** — `lcx.bambuser.com` = **Global**; `lcx-eu.bambuser.com` = **EU**. This is the most reliable signal when there's no snippet yet.
3. **Onboarding info** from Bambuser.
4. If none of the above is available, **ask the customer** which region their workspace is in before emitting any URLs.

## Step 2 — Apply the region (it is NOT encoded uniformly)

The encoding differs per product — do **not** assume a single `-eu` rule everywhere:

| Product / surface | Global | EU | Encoding |
|---|---|---|---|
| **Live Shopping** (player) | `lcx-embed.bambuser.com/default/embed.js` | `lcx-embed-eu.bambuser.com/default/embed.js` | **subdomain** swap |
| **Shoppable Video** (`<bam-playlist>`) | `lcx-embed.bambuser.com/default/embed.js` | `lcx-embed-eu.bambuser.com/default/embed.js` | **subdomain** swap |
| **Video Consultation** (Calls Widget) | `one-to-one.bambuser.com/embed.js` | `one-to-one.bambuser.com/eu/embed.js` | **`/eu/` path** segment |
| **Mobile SDKs** (iOS/Android/RN) | region enum value **`US`** (note: not "Global") | enum value **`EU`** | region **enum** in SDK init (type name is platform-specific) |
| **Chat** ("Hero") | `cdn.chat.bambuser.com/loader.js` | *(no documented EU host variant)* | environment via **Application ID** |
| **Live Shopping Channel widget** | `lcx-widgets.bambuser.com/embed.js` | *(no documented EU variant)* | — |

Notes that bite people:

- **Live/Shoppable Video swap the subdomain** (`lcx-embed` ↔ `lcx-embed-eu`). **Video Consultation swaps a path** (`/embed.js` ↔ `/eu/embed.js`) on the *same* host. Mixing these up produces a silently-wrong embed.
- **Mobile uses an enum**, and the non-EU value is literally `US`, not "Global". Don't pass a host. The enum's type name differs by platform — Android `OrganizationServer.US` / `.EU`; iOS `BambuserVideoPlayer(server: .US)` / `.EU` — so fetch the platform's setup doc for the exact name.
- **Chat and the Channel widget have no documented EU host** — region for Chat is selected by which Application ID you install, not by the loader URL. If a customer "in the EU" asks for an EU Chat loader URL, that's a misunderstanding to correct.
- REST APIs (e.g. `liveshopping-api.bambuser.com`) did not surface a documented EU host variant — verify in the analytics/API doc page if data residency is a concern.

## Step 3 — Keep everything in one region

Within a single integration, **every** Bambuser asset (embed script, widgets, API calls, mobile init) must point at the **same** region as the workspace. A common bug is a correct player embed paired with a default-region API call (or vice-versa) — the player works but stats/products don't.

## Data residency

EU exists primarily for **data residency / privacy** requirements (EU customer data stays in the EU environment). If a customer cites GDPR/data-residency reasons, that's a strong signal they are (or should be) on EU — but **confirm the actual workspace region** rather than inferring from geography alone; a workspace's region is a provisioning decision, not auto-derived from where shoppers are.
