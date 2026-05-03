---
name: bootstrap-project
description: Use this skill when the user wants to bootstrap a new web project from the kt-monorepo-template (Next.js 16 + Sanity v5 + Tailwind 4 + shadcn/ui). Walks through project naming, design token import, GitHub/Vercel/Sanity wiring, and an initial commit. Stops with a runnable scaffold and a final URL report.
---

# bootstrap-project

## Invariants

- Use the canonical stack: Next.js 16 App Router, Sanity v5, Tailwind 4, shadcn/ui, TypeScript, npm. Do not offer alternatives.
- Source code comes from `eljun/kt-monorepo-template` via `git clone --depth 1`. Do not generate boilerplate inline as a fallback if the clone fails.
- Design tokens are an INPUT (file path or paste-in). Do not invent or generate them.
- Stop and confirm before each irreversible cloud action: GitHub repo creation, Vercel project link, git push. Never auto-create Sanity projects.
- Do not run `npm run dev` automatically — leave it for the user.
- Emit a one-line status message before each major step so the user sees progress.

## Prerequisites

Run these checks first. If any fail, stop and report which one.

| Tool | Check | Expected |
|------|-------|----------|
| Node | `node --version` | v20.x or higher |
| npm | `npm --version` | v10.x or higher |
| gh | `gh auth status` | Logged in to github.com |
| vercel | `vercel whoami` | Returns a username |
| Sanity | `npx -y sanity@latest --version` | Returns a version (no global install needed) |

## Inputs (ask the user, in order)

1. **Project slug** — kebab-case, used for directory name and repo names. Example: `acme-wellness-spa`.
2. **Project display name** — used in copy and `package.json`. Example: `Acme Wellness Spa`.
3. **Project directory** — default: `~/Code/Personal/{slug}`.
4. **Design tokens source** — one of:
   - Absolute path to a CSS file containing the `@theme` block to inject into `app/globals.css`
   - `skip` — keep the template's placeholder palette
5. **GitHub visibility** — `private` (default) or `public`.
6. **Sanity project** — one of:
   - `existing` — user provides project ID + dataset name
   - `new` — user creates a project at https://sanity.io/manage in a new tab, then provides the project ID + dataset name (skill never creates Sanity projects automatically)
7. **Vercel project name** — default: same as slug.

## Workflow

1. **Verify prerequisites.** Run all checks. Stop on failure.
2. **Confirm inputs.** Echo answers back as a summary. Wait for explicit "go" before any side effects.
3. **Clone template.** Status: "Cloning kt-monorepo-template..."
   ```bash
   git clone --depth 1 https://github.com/eljun/kt-monorepo-template.git {target_dir}
   rm -rf {target_dir}/.git
   ```
4. **Substitute placeholders.** Status: "Substituting project placeholders..."
   - Find files containing placeholders: `grep -rl '{{PROJECT_' {target_dir}`
   - Replace `{{PROJECT_SLUG}}`, `{{PROJECT_NAME}}`, `{{PROJECT_DESCRIPTION}}` across all matches.
5. **Inject design tokens.** Status: "Injecting design tokens..." (skip if user chose `skip`)
   - Read user's tokens file.
   - Replace the `@theme { ... }` block in `{target_dir}/app/globals.css` with the user's contents. Preserve everything else in `globals.css`.
6. **Wire Sanity config.** Status: "Wiring Sanity config..."
   - Update `sanity/env.ts` (or wherever the template stores the project ID/dataset) with the user-provided values.
   - Write the same values to `.env.local` if the template uses env vars (`NEXT_PUBLIC_SANITY_PROJECT_ID`, `NEXT_PUBLIC_SANITY_DATASET`).
7. **Install dependencies.** Status: "Installing dependencies (this may take a minute)..."
   ```bash
   cd {target_dir} && npm install
   ```
8. **Initial commit.** Status: "Creating initial commit..."
   ```bash
   cd {target_dir} && git init && git add . && git commit -m "feat: initial scaffold from kt-monorepo-template"
   ```
9. **Confirm GitHub repo creation.** Show user: "About to run `gh repo create eljun/{slug} --{visibility} --source . --remote origin`. OK?" Wait for explicit confirmation.
10. **Create GitHub repo.** Status: "Creating GitHub repo..." Run the confirmed command.
11. **Confirm Vercel link.** Show user: "About to run `vercel link` (interactive, may ask scope and project name). OK?" Wait for confirmation.
12. **Link Vercel.** Status: "Linking Vercel project..."
    ```bash
    cd {target_dir} && vercel link
    ```
    The CLI prompts the user for scope and project — do not try to script around it.
13. **Pull Vercel env.**
    ```bash
    cd {target_dir} && vercel env pull .env.local
    ```
    The file may be empty if no env vars are set yet — that is fine.
14. **Confirm push.** Show user: "About to push to GitHub. OK?" Wait for confirmation.
15. **Push to GitHub.** Status: "Pushing to GitHub..."
    ```bash
    cd {target_dir} && git push -u origin main
    ```
16. **Final report.** Print a structured summary with these fields:
    - Local directory
    - GitHub URL: `https://github.com/eljun/{slug}`
    - Vercel project URL (from `vercel link` output)
    - Sanity project ID + dataset
    - Sanity Studio URL: `http://localhost:3000/studio` (once dev runs)
    - Next steps: `cd {target_dir} && npm run dev`

## Status messages (emit before each step)

| Step | Message |
|------|---------|
| 1 | Checking prerequisites... |
| 3 | Cloning kt-monorepo-template... |
| 4 | Substituting project placeholders... |
| 5 | Injecting design tokens... |
| 6 | Wiring Sanity config... |
| 7 | Installing dependencies (this may take a minute)... |
| 8 | Creating initial commit... |
| 10 | Creating GitHub repo... |
| 12 | Linking Vercel project... |
| 15 | Pushing to GitHub... |

## Failure handling

| Failure | Action |
|---------|--------|
| Template clone fails | The `eljun/kt-monorepo-template` repo may not exist or be private. Stop and tell the user — do not generate inline boilerplate as a fallback. |
| Placeholder substitution finds zero matches | Warn but continue — template may have evolved past placeholders. |
| `npm install` fails | Stop. Leave the directory in place. Show the install error. |
| `gh repo create` collision | Stop and ask for an alternative slug. Do not auto-suffix. |
| `vercel link` fails | Skip step 13. Leave a `TODO: vercel link` note in the final report. The project is still functional locally. |
| `git push` fails | Stop. Local repo is intact. Show the push error so the user can debug (auth, branch protection, etc.). |

## Out of scope (v1)

- Stack alternatives (Svelte, Supabase, Material UI, Pages Router, etc.)
- Automatic Sanity project creation
- Automatic dev server startup
- CI/CD workflow configuration
- Custom domain setup on Vercel
- Sanity content seeding or migration

## Handoff

End with the structured report from step 16, plus:

```text
Project bootstrapped: {target_dir}
Status: ready for first dev session
Next: cd {target_dir} && npm run dev
```
