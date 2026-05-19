# kt-skills

Pattern-enforcement skills for `kt-monorepo` projects — Next.js 16 + Sanity v5 + Tailwind 4 + shadcn/ui sites that follow the page-builder convention.

These skills are designed to be invoked from Claude Code, Codex CLI, or any other Agent Skills-compatible runtime.

## Quick start

1. **Install once per machine:**
   ```bash
   npx skills add eljun/kt-skills -g -a claude-code
   ```
2. **Check prerequisites** (one-time): Node ≥ 20, `gh auth status` returns logged in, `vercel whoami` returns a username, and a Sanity project created at <https://sanity.io/manage> (the skill never creates Sanity projects automatically).
3. **Start a new project.** Open Claude Code in any directory and either:
   - Invoke the slash command: `/bootstrap-project`
   - Or describe the goal in natural language: *"Bootstrap a new project from the kt template"*, *"Start a new client site using kt-monorepo-template"*.

The skill stops and confirms before each irreversible cloud action: GitHub repo creation, Vercel project link, and `git push`. Local actions (clone, npm install, initial commit) run without confirmation.

### Inputs the skill will ask for

Have these answers ready before you start — the skill walks through them in order:

| # | Input | Example / default |
|---|-------|-------------------|
| 1 | Project slug (kebab-case) | `acme-wellness-spa` |
| 2 | Project display name | `Acme Wellness Spa` |
| 3 | Project description (one line) | `Boutique wellness studio in Brooklyn` |
| 4 | Project directory | `~/Code/Personal/{slug}` |
| 5 | Design tokens CSS file, or `skip` | `~/Brand/acme/tokens.css` |
| 6 | GitHub visibility | `private` (default) or `public` |
| 7 | Sanity project ID + dataset | from <https://sanity.io/manage> |
| 8 | Vercel project name | defaults to slug |

If you need an item later than expected, say `pause` — the skill will not invent inputs on your behalf.

## Skills

| Skill | Purpose |
|-------|---------|
| `bootstrap-project` | Scaffold a new project from `kt-monorepo-template`, inject design tokens, wire GitHub/Vercel/Sanity, and produce an initial commit. |

Future companions (planned, not yet implemented):

- `add-page` — add a new `page` document and route in an existing project
- `add-section` — scaffold a new section schema + component pair following the page-builder convention
- `add-component` — scaffold a UI primitive in `components/ui/` matching shadcn conventions
- `migrate-tokens` — migrate raw Tailwind classes to design tokens after a token update

## Install variants

The default install in [Quick start](#quick-start) covers most cases. Use these for other agents or partial installs.

This repository follows the same install mechanism as `eljun/workflow-skills` (Vercel `npx skills`).

Install for a different agent:

```bash
npx skills add eljun/kt-skills -g -a codex
npx skills add eljun/kt-skills -g -a gemini-cli
```

Install a single skill:

```bash
npx skills add eljun/kt-skills --skill bootstrap-project -g -a claude-code
```

## Companion repositories

| Repo | Purpose |
|------|---------|
| [`eljun/kt-monorepo-template`](https://github.com/eljun/kt-monorepo-template) | The canonical boilerplate cloned by `bootstrap-project` |
| [`eljun/workflow-skills`](https://github.com/eljun/workflow-skills) | The development workflow chain (`task` → `implement` → ... → `release`) |

## Canonical layout

```
kt-skills/
├── .claude-plugin/
│   └── marketplace.json          # plugin manifest for npx skills + Claude plugin discovery
├── plugins/
│   └── kt/
│       ├── commands/
│       │   └── bootstrap-project.md   # slash command shim that invokes the skill
│       └── skills/
│           └── bootstrap-project/
│               └── SKILL.md
├── adapters/
│   └── claude/
│       └── README.md             # Claude Code-specific install + invocation notes
├── AGENTS.md
└── README.md
```

The portable skill source is `plugins/kt/skills/{skill-name}/SKILL.md`. Adapters explain runtime-specific install paths and invocation styles.

## Conventions enforced

Skills in this repo enforce the conventions documented in `kt-monorepo-template/CLAUDE.md`:

- **Stack:** Next.js 16 (App Router), Sanity v5, Tailwind 4, shadcn/ui, TypeScript, npm
- **Architecture:** page-builder pattern (Pattern A) — every page is a `page` document with a `slug` and `sections` array
- **Tokens:** mandatory use of design tokens from `app/globals.css`, never raw Tailwind color/spacing
- **File naming:** PascalCase for components, kebab-case for `components/ui/` primitives, camelCase for Sanity schemas
- **Brand voice:** restrained, sensory, present-tense; no emoji, no exclamation marks, no generic spa marketing language

When a skill scaffolds new code, it should match these conventions exactly.
