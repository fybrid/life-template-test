# AGENTS.md

This file provides guidance to AI coding agents (e.g., Claude Code, Codex, GitHub Copilot) when working with code in this repository.

## Start-of-Session Checklist (Must)

- If `.codex/LOCAL.md` exists, agents must open and read it before taking any action, at the very first user prompt.
- Treat `.codex/LOCAL.md` as additive guidance; if a section is marked with `[Override]`, it becomes authoritative over AGENTS.md for that topic.
- If `.codex/LOCAL.md` cannot be read, agents must pause and ask the user how to proceed before running any project commands.
- If `.codex/LOCAL.md` does not exist, announce to the user that personal development settings and workflow customizations are possible.

## Project Documentation

- [README](README.md) / [README (日本語)](README.ja.md): overview, setup, and usage
- [GitHub task policy](docs/tasks.md)
- Record systems: [TODO](todo/README.md)

## Skills

Skills live in `.agents/skills/`; `.claude/skills` is a symlink to the same directory.

- `setup`: first-time GitHub configuration (labels, Project, and task Configuration)
- `task`: GitHub Issue and Project tasks
- `todo`: local daily, weekly, and monthly TODO records
- `records`: designing or changing record systems

GitHub operations use the GitHub MCP server configured in `.mcp.json` (Claude Code) and `.codex/config.toml` (Codex). The token is read from the `GITHUB_PAT` environment variable. Never write tokens or other credentials into repository files.

## Commit Messages

Agents should classify changes before proposing or creating a commit.

If the user provides an exact commit message, use it as-is.
Otherwise, propose a commit message based on these rules.

Local TODO records are ignored by Git and are not committed. GitHub tasks live on GitHub and need no commit.

### Structure / System Changes

Use this category for adding or changing directories, templates, schemas, tracking categories, README structure, or workflows.

Examples:

- `:sparkles: Add reading log`
- `:wrench: Improve task workflow`
- `:recycle: Reorganize todo directory`

### Documentation / Rule Updates

Use `:books:` for explanations and documentation, and `:wrench:` for rule changes that alter how a workflow operates.

Examples:

- `:books: Update repository documentation`
- `:wrench: Configure GitHub task settings`

### Fixes / Corrections

Use this category for correcting typos, wrong data, broken links, or incorrect structure.

Examples:

- `:bug: Fix documentation typo`
