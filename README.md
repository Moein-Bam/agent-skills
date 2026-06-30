# Bambuser Agent Skills

Expert‑level [Agent Skill](https://agentskills.io) that teaches your AI coding agent how to integrate [Bambuser](https://bambuser.com) video‑commerce products — **Live Shopping, Shoppable Video, Video Consultation, Chat, and the App Framework *(Beta)*** — into any website, app, or storefront.

It works with **Claude Code, Cursor, OpenAI Codex, Gemini CLI, GitHub Copilot, Amp**, and other tools that support the open Agent Skills standard, plus a Markdown fallback for rules‑based tools.

## Why this skill

Bambuser's docs change. Rather than freezing a copy that goes stale, this skill is deliberately **thin and durable**: it carries the concepts that are shared across products (the ready‑callback pattern, the cart/product‑data model, Global vs EU regions) and the non‑obvious "gotchas" that are hard to learn from a few pages — then **fetches the latest specifics from the official docs at [bambuser.com/docs](https://bambuser.com/docs) at runtime** (the `llms.txt` index + per‑page `.md`). So answers stay current as the docs evolve.

## Install

> **One‑time, for claude.ai users:** allow the `bambuser.com` domain at [claude.ai/settings/capabilities](https://claude.ai/settings/capabilities), or the skill can't fetch the docs it relies on. (Claude Code, Cursor, Codex, etc. allow web fetches by default.)

### Recommended — the cross‑agent installer

The [`skills` CLI](https://agentskills.io) installs into whichever agent you use:

```bash
# Into the current project (committed, shared with your team):
npx skills add bambuser/agent-skills

# Or globally, for all your projects:
npx skills add -g bambuser/agent-skills
```

### Manual — copy the skill folder

No CLI needed. Clone, then copy `skills/bambuser-integration` into your agent's skills directory. **Global** (works for all your projects):

```bash
git clone https://github.com/bambuser/agent-skills
mkdir -p ~/.claude/skills ~/.agents/skills
cp -r agent-skills/skills/bambuser-integration ~/.claude/skills/   # Claude Code (reads ONLY .claude/skills)
cp -r agent-skills/skills/bambuser-integration ~/.agents/skills/   # Cursor / Codex / Gemini / Copilot / Amp
```

**Per‑project** — run these *from your own project's root* (not from inside the clone):

```bash
mkdir -p .claude/skills .agents/skills
cp -r /path/to/agent-skills/skills/bambuser-integration .claude/skills/   # Claude Code
cp -r /path/to/agent-skills/skills/bambuser-integration .agents/skills/   # everyone else
```

### Script — install into every agent at once

`install.sh` puts the skill in the right per‑agent directories: it **symlinks** the Claude Code dir (so `git pull` refreshes it) and **copies** the neutral `.agents/skills` dir (Codex doesn't load a symlinked one).

```bash
git clone https://github.com/bambuser/agent-skills

# Global — simplest; works across all your projects:
agent-skills/install.sh --global

# Per‑project — installs into ./.claude/skills and ./.agents/skills of the CURRENT directory,
# so run it from your project root:
cd /path/to/your/project && /path/to/agent-skills/install.sh
```

**Verify it worked:** `npx skills list` (CLI), or `ls .claude/skills/bambuser-integration/SKILL.md` — then restart your agent / start a new session. If you previously installed an earlier Bambuser skill under a different name (e.g. `bambuser-core`), remove it so the triggers don't collide.

### Rules‑based tools (Windsurf, Cline, Continue)

These don't support skills, but they read Markdown context. Copy this repo's [`AGENTS.md`](AGENTS.md) to your project root — it names the products and regions and points the agent at the skill and the live docs. Windsurf reads root `AGENTS.md` directly; for **Cline** (`.clinerules/`) and **Continue** (`.continue/rules/`) drop it in (or point their rules config at) that tool's rules folder.

## Use it

Just ask your agent in natural language — the skill triggers on Bambuser topics:

```text
"Embed a Bambuser live shopping show on our PDP and wire up add‑to‑cart."
"Set up the <bam-playlist> shoppable video component with our product feed."
"Add the Bambuser video consultation calls widget and co‑browsing."
"Which Bambuser embed host do we use for an EU workspace?"
"Integrate Bambuser chat on Shopify with purchase tracking."
```

The skill identifies the product, applies the shared concepts, and fetches the exact, current detail from the docs before writing code.

> **If doc fetches fail on claude.ai**, allow the `bambuser.com` domain at [claude.ai/settings/capabilities](https://claude.ai/settings/capabilities) — the skill relies on fetching the live docs. (Claude Code, Cursor, Codex and the other CLIs allow web fetches by default, though enterprise network policies may still block them.)

## What's inside

```
skills/bambuser-integration/
├── SKILL.md                        # the durable concepts + the doc‑fetching mechanism
└── references/
    ├── products.md                 # the five products in depth + gotchas
    ├── regions.md                  # Global vs EU (per‑product encoding + hosts)
    ├── cart-and-product-data.md    # shared product object, factory chain, callbacks
    └── doc-index.md                # validated entry‑point pages + the llms-*.txt bundles
```

## Contributing

Found a gap or an outdated concept? See [CONTRIBUTING.md](CONTRIBUTING.md) — open an issue or PR. Note: the skill should stay **concept‑level**; anything that belongs in the product docs goes to [bambuser.com/docs](https://bambuser.com/docs), not here.

## License

[Apache-2.0](LICENSE) © Bambuser
