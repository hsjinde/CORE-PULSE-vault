# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

**CORE PULSE** is an Obsidian vault — a personal knowledge base made of Markdown files. It is the working directory for the **Claudian** plugin (v2.0.18), which embeds Claude Code directly inside Obsidian. All AI sessions operate with the vault root as their working directory.

The vault is currently in its initial state — no notes exist yet. As notes are added, this file should be updated to describe the emerging structure.

## Vault Configuration

| Setting | Value |
|---|---|
| Theme | AnuPpuccin |
| Default model | claude-sonnet-4-6 |
| Permission mode | yolo (all edits accepted automatically) |
| AI API base URL | `https://cli.19980803.xyz/v1` (custom proxy) |

**Active community plugins:**
- `realclaudian` — Claudian v2.0.18: the AI agent interface itself
- `obsidian-git` — auto-commits on file change every 3 minutes with message `vault backup: {{date}}`

**Active core plugins of note:** Daily Notes, Templates, Canvas, Backlinks, Properties, Bases, Sync, File Recovery.

## Git & Version Control

The vault is a git repository (branch: `main`). Obsidian Git auto-commits any changed file every **3 minutes** with the message `vault backup: {{date}}`. Manual commits and pushes are done through the Obsidian Git plugin UI or via CLI from the vault root.

`.gitignore` excludes:
- `.obsidian/workspace.json` and `.obsidian/workspace-mobile.json` (per-device UI state)
- `.claudian/` (Claudian session data — API keys, conversation history)

## Path Conventions

All file operations use **relative paths from the vault root** (`D:\CORE PULSE-vault`). Never use absolute paths when creating or editing vault notes.

- ✓ `notes/my-note.md`
- ✗ `D:\CORE PULSE-vault\notes\my-note.md`

## Claudian Session State

Claudian stores its runtime data in `.claudian/` (gitignored):
- `claudian-settings.json` — plugin UI settings, environment variables, API credentials
- `sessions/` — per-conversation metadata (`.meta.json` files)

Do not read or modify `claudian-settings.json` unless the user explicitly asks — it contains API tokens.

## Obsidian Conventions to Follow

- **Frontmatter**: Use YAML frontmatter for metadata. Respect existing fields; do not remove them.
- **Internal links**: Use `[[note-name]]` wiki-link syntax. Avoid breaking existing links when renaming files.
- **Tags**: Use `#tag-name` inline or `tags:` in frontmatter — be consistent with whichever style the note already uses.
- **Daily Notes**: If the user uses daily notes, they follow the Obsidian Daily Notes core plugin format.
- **Dataview blocks**: Do not modify ` ```dataview ``` ` query blocks unless explicitly asked.
- **Canvas files**: `.canvas` files are JSON — treat them carefully; do not open as plain Markdown.
