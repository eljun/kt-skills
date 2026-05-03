# kt-skills

Pattern-enforcement skills for `kt-monorepo` projects — Next.js 16 + Sanity v5 + Tailwind 4 + shadcn/ui sites that follow the page-builder convention.

These skills are designed to be invoked from Claude Code, Codex CLI, or any other Agent Skills-compatible runtime.

## Skills

| Skill | Purpose |
|-------|---------|
| `bootstrap-project` | Scaffold a new project from `kt-monorepo-template`, inject design tokens, wire GitHub/Vercel/Sanity, and produce an initial commit. |

Future companions (planned, not yet implemented):

- `add-page` — add a new `page` document and route in an existing project
- `add-section` — scaffold a new section schema + component pair following the page-builder convention
- `add-component` — scaffold a UI primitive in `components/ui/` matching shadcn conventions
- `migrate-tokens` — migrate raw Tailwind classes to design tokens after a token update

## Install

This repository follows the same install mechanism as `eljun/workflow-skills` (Vercel `npx skills`).

Install all kt-skills globally for Claude Code:

```bash
npx skills add eljun/kt-skills -g -a claude-code
```

Install for a different agent:

```bash
npx skills add eljun/kt-skills -g -a codex
npx skills add eljun/kt-skills -g -a gemini-cli
```

Install a single skill:

```bash
npx skills add eljun/kt-skills --skill bootstrap-project -g -a claude-code
```

> **Note:** This repo is currently private. To make it installable via `npx skills` for other machines or collaborators, change the GitHub visibility to public.

## Companion repositories

| Repo | Purpose |
|------|---------|
| `eljun/kt-monorepo-template` | The canonical boilerplate cloned by `bootstrap-project` (TODO: not yet created) |
| `eljun/workflow-skills` | The development workflow chain (`task` → `implement` → ... → `release`) |

## Canonical layout

```
kt-skills/
├── .claude-plugin/
│   └── marketplace.json          # plugin manifest for npx skills + Claude plugin discovery
├── plugins/
│   └── kt/
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
