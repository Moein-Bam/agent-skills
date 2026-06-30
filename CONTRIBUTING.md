# Contributing

Thanks for helping improve the Bambuser agent skill. The bar is simple: **keep the skill thin and durable, and let the live docs carry the detail.**

## The golden rule: don't duplicate the docs

This skill is intentionally small. It captures:

- **Durable concepts** — what each product is, the shared models (ready callbacks, cart/product data, regions), and how products relate.
- **Non‑obvious "relevances"** — gotchas that are hard to learn by skimming a page or two (e.g. that the EU region is encoded differently per product).
- **The fetch mechanism** — how to pull the latest specifics from `bambuser.com/docs` at runtime.

It deliberately does **not** copy embed snippets, attribute lists, event payloads, config keys, or endpoint schemas. Those live in the docs and change over time; freezing them here makes the skill wrong. If you're tempted to paste doc content, instead add a pointer to the right doc page.

## What good changes look like

- A newly discovered gotcha or cross‑product relationship → add a concise note to the relevant `references/*.md`.
- A doc slug in `references/doc-index.md` that 404s → fix it against `https://bambuser.com/docs/llms.txt`, or remove it (the index is a convenience snapshot, not the source of truth).
- A new product, region mechanic, or shared concept → update `SKILL.md` and the references.

## Conventions

- The skill folder name **must** equal the `name:` in `SKILL.md` frontmatter (`bambuser-integration`), be lowercase/hyphenated, and the `description` stays ≤ 1024 characters with the trigger up front.
- Keep `SKILL.md` lean (roughly ≤ 200 lines); push depth into `references/`.
- Maintain the shared agent context **only in `AGENTS.md`**. `CLAUDE.md` imports it via `@AGENTS.md`, so don't duplicate the content into `CLAUDE.md` (different tools read different filenames).
- Verify any doc URL you add actually resolves (`<page>.md` should not 404).
- The product set appears in several places — if you add/remove/rename a product, update `SKILL.md`, `AGENTS.md`, `references/products.md`, `references/doc-index.md`, and `README.md` together.

## How to propose a change

Open an issue describing the gap (include the agent/tool, the prompt, and what was wrong), or send a PR. For requests to cover a product/topic the skill doesn't yet address, an issue is perfect.
