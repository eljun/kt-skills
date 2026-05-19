# Claude Code Adapter

## Install

```bash
npx skills add eljun/kt-skills -g -a claude-code
```

This populates `~/.agents/skills/` and is discoverable by Claude Code via the existing symlinks in `~/.claude/skills/`.

To install a single skill:

```bash
npx skills add eljun/kt-skills --skill bootstrap-project -g -a claude-code
```

## Invocation

You can trigger `bootstrap-project` two ways:

1. **Slash command** — `/bootstrap-project` (registered by the plugin at `plugins/kt/commands/bootstrap-project.md`; discoverable via `/help` and tab-completion). Note: this depends on Claude Code's auto-discovery of plugin `commands/` directories and has not yet been exercised end-to-end — if it fails to register, fall back to the description-match invocation below and please file an issue.

2. **Description match** — skills are also matched by their `description` field, so a natural-language prompt works:

   - "Bootstrap a new project."
   - "Start a new client site using the kt template."
   - "I need to spin up a new project for {client name}."

## Reasoning tier

`bootstrap-project` is a `balanced` tier task: many tool calls, each routine, but the order matters. Default Claude Code reasoning is appropriate. Switch to `xhigh` or higher only if a step needs unusual debugging.

## Required permissions

The skill calls Bash for `git`, `gh`, `vercel`, `npm`, `npx`, and file operations. If running with restrictive permission settings, allowlist these commands in advance to avoid prompts mid-bootstrap:

```jsonc
// In ~/.claude/settings.local.json or project .claude/settings.json
{
  "permissions": {
    "allow": [
      "Bash(git:*)",
      "Bash(gh:*)",
      "Bash(vercel:*)",
      "Bash(npm install)",
      "Bash(npm run *)",
      "Bash(npx:*)"
    ]
  }
}
```

## Status messages

Each step in the skill emits a one-line status message before running. These appear inline in the Claude Code transcript — there is no separate status bar API. If you want them to be more prominent, configure a status line script in `~/.claude/settings.json` that surfaces the most recent assistant message.
