# Nuxt + Cloudflare App Scaffold Skill

An agent skill that scaffolds a new, production-oriented full-stack app on the **latest version of Nuxt, deployed to Cloudflare Workers via Wrangler** — from natural language descriptions like "build me a blog on Cloudflare".


## What It Is and What It Does

- **Opinionated, production-ready scaffold.** Nuxt (latest major, `nuxi init` pulls it automatically) + NuxtHub (`@nuxthub/core`) as the binding/runtime layer for Cloudflare **D1 / KV / Blob**, with deployment handled by **Wrangler** — not a minimal init, but only the modules that apply to the app.
- **UI kit is a choice, not an assumption.** **Nuxt UI** (Tailwind-based, default) or **Antdv Next** (Ant Design's Vue 3 system), each wired through its official Nuxt module. Exactly one, never both.
- **Opt-in, not cargo-culted.** Nuxt Content (blog/CMS/markdown) and i18n (`@nuxtjs/i18n`) are only included when the request actually calls for them; a DB-less app gets no `server/db/`, no `d1_databases` block, no stray Drizzle deps.
- **Cloudflare-native deployment wiring.** `wrangler.jsonc` with D1/KV/Blob bindings, local dev emulation via Miniflare, a named `preview` environment isolated from production data, and guidance for Cloudflare Workers Builds' per-branch preview URLs.
- **SEO, testing, and linting by default.** `@nuxtjs/seo` meta-module (sitemap, robots, OG, schema.org), Vitest + `@nuxt/test-utils`, `@nuxt/eslint`, and a final checklist before handing the project back.
- **Knows its boundaries.** It owns the *scaffolding* decisions end to end; deep per-module usage (which component to reach for, theming, advanced content queries) is delegated to the separate `nuxt-ui`, `antdv-next`, and `nuxt-content` skills *if they happen to be installed* — otherwise the skill's own inline guidance plus official docs ([ui.nuxt.com](https://ui.nuxt.com), [antdv-next.com](https://www.antdv-next.com), [content.nuxt.com](https://content.nuxt.com)) cover the same ground.

## Installation

### Option A: `npx skills` (any supported agent)

The [skills CLI](https://github.com/vercel-labs/skills) installs this repo's skill into your agent's skill directory:

```bash
# GitHub shorthand (owner/repo)
npx skills add https://github.com/Tech-Voyage-Dev/scaffold-nuxt-cloudflare-app-skill

# Or target a specific agent explicitly
npx skills add https://github.com/Tech-Voyage-Dev/scaffold-nuxt-cloudflare-app-skill --agent claude-code
```

Use it once without installing by piping the generated prompt into a supported agent:

```bash
npx skills use https://github.com/Tech-Voyage-Dev/scaffold-nuxt-cloudflare-app-skill | claude
```

### Option B: Manual install for Claude Code

Clone or download this repo, then copy the skill folder into your project's `.claude/skills/` directory:

```bash
git clone https://github.com/Tech-Voyage-Dev/scaffold-nuxt-cloudflare-app-skill.git
cp -r scaffold-nuxt-cloudflare-app-skill .claude/skills/scaffold-nuxt-cloudflare-app
```

For a personal install that works across all your projects, copy it to `~/.claude/skills/scaffold-nuxt-cloudflare-app` instead.

## Usage

Ask your coding agent to scaffold a new app:

> "Scaffold a new Nuxt app deployed to Cloudflare Workers — Nuxt UI, a blog section, and a D1 database"

The skill takes over from there. It starts by clarifying only what genuinely can't be inferred from the request — app name/domain, **which UI kit** (Nuxt UI vs Antdv Next), **whether a database is needed**, and whether Content/i18n are in scope — then:

1. Scaffolds the base project with `pnpm dlx nuxi@latest init`, pins pnpm, and sets native-build allowlists for Cloudflare's build environment.
2. Installs modules from a decision table (never blindly): UI kit, `@nuxt/image`, `@nuxtjs/seo`, `@nuxthub/core`, `@vueuse/nuxt`, eslint, and Vitest — plus Drizzle/D1, Content, i18n, analytics only when selected.
3. Lays out the exact `app/` + `server/` + `shared/` directory structure and wires up `nuxt.config.ts` with the UI kit's root wrapper (`UApp` for Nuxt UI).
4. Configures `wrangler.jsonc` with Cloudflare bindings, local dev emulation, and a production-isolated `preview` environment, with deploy commands:

   ```bash
   CLOUDFLARE_ENV=preview npx nuxi build && npx wrangler deploy --env preview   # preview
   npx nuxi build && npx wrangler deploy                                        # production
   ```

5. Sets up testing/linting config and runs through a final checklist before handing back a working, deployable starting point.

Other prompt ideas:

> "Create a Nuxt 4 app for a marketing site — no database, i18n-ready, Antdv Next as the UI kit"

> "Scaffold a full-stack dashboard on Cloudflare with D1 + Drizzle and Nuxt UI"

## File Structure

```
scaffold-nuxt-cloudflare-app-skill/
  SKILL.md                        # Scaffolding methodology + full workflow
  references/
    directory-structure.md        # Annotated app/ + server/ + shared/ layout
    nuxt.config.example.ts        # nuxt.config.ts with all module wiring
    wrangler.example.jsonc        # Cloudflare bindings + preview environment
    package.example.json          # Dependency set per module selection
    content.config.example.ts     # @nuxt/content v3 collection config (opt-in)
    nuxthub-deployment.md         # NuxtHub runtime + Wrangler deploy reference
```
