# AGENTS.md — kt-skills coordination

This file is read by every agent runtime that consumes the skills in this repository (Claude Code, Codex CLI, Gemini CLI, etc.). It explains shared conventions and points to the canonical source.

## Purpose

`kt-skills` is a collection of pattern-enforcement skills for `kt-monorepo` projects: bootstrapping new projects from `kt-monorepo-template`, scaffolding new pages/sections/components per the page-builder convention, and migrating between design token versions.

For human-facing rules — code conventions, design tokens, brief discipline, and brand voice — read `kt-monorepo-template/CLAUDE.md`. AGENTS.md does not duplicate those; it points to them.

## Canonical skill source

```
plugins/kt/skills/{skill-name}/SKILL.md
```

Each `SKILL.md` is portable and agent-neutral. Runtime-specific notes (install path, invocation style, reasoning tier mapping) live under `adapters/{runtime}/README.md`.

## Conventions every skill must respect

When generating or modifying code in target projects, skills must follow:

- **Stack:** Next.js 16 (App Router), Sanity v5, Tailwind 4, shadcn/ui, TypeScript, npm
- **Architecture:** page-builder pattern (Pattern A) — pages are `page` documents with a `sections` array; no per-page singleton schemas except `siteSettings`
- **Design tokens:** always use tokens from `app/globals.css` (e.g. `bg-washi`, `text-ink-700`); never raw Tailwind color/spacing/shadow classes; never inline hex values
- **File naming:**
  - `components/*.tsx` and `components/sections/*.tsx` → PascalCase
  - `components/ui/*.tsx` → kebab-case (preserves shadcn convention)
  - `lib/**/*.ts(x)` → kebab-case
  - `sanity/**/*.ts` → camelCase matching the schema name
- **Brand voice:** restrained, sensory, present-tense; no emoji, no exclamation marks, no generic spa marketing language

These conventions are documented in detail in `kt-monorepo-template/CLAUDE.md`. The template is the canonical source — if the template changes, update both this file and any affected skills.

## Provider assumptions

Skills assume these CLIs are installed and authenticated on the target machine:

- `gh` (GitHub CLI) — authenticated as the org owner
- `vercel` (Vercel CLI) — logged in
- `git` — system git, configured with `user.email` and `user.name`
- `npx` — comes with Node ≥ 20

Sanity is invoked via `npx sanity` to avoid requiring a global install.

## Stop-and-confirm gates

Skills must stop and ask the user before any irreversible cloud action:

- Creating GitHub repositories
- Linking Vercel projects (creates project if missing)
- Creating Sanity projects (skills should never auto-create — ask the user to create manually and provide the ID)
- Pushing to remote repositories

Local actions (file generation, `npm install`, `git init`, local commits) do not require confirmation.

## Handoff

Each skill ends with a structured handoff message describing what was created and the recommended next step. The handoff is mandatory — it's the user's signal that the skill is done.
