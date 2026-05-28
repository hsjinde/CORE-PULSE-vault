# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

**CORE PULSE** is an Obsidian vault — a personal knowledge base made of Markdown files. It is the working directory for the **Claudian** plugin (v2.0.18), which embeds Claude Code directly inside Obsidian. All AI sessions operate with the vault root as their working directory.

CORE PULSE is structured as an **LLM Wiki** — a personal knowledge base that I (Claudian) incrementally build and maintain. Raw source documents live in `raw/`; the synthesized wiki lives in `wiki/`. I write the wiki; you curate the sources and ask the questions.

See the **LLM Wiki Schema** section below for the full structure, page types, and workflows.

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

---

## LLM Wiki Schema

This section defines how CORE PULSE is structured and how Claudian operates on it. Every session should read this before making any wiki edits.

### Folder Layout

```
CORE PULSE-vault/
├── raw/                   # Source documents — READ ONLY. Never modify.
│   ├── assets/            # Images downloaded from web-clipped articles
│   └── *.md               # Web clips, notes, transcripts, article drafts
├── wiki/                  # LLM-maintained wiki — Claudian writes everything here
│   ├── index.md           # Catalog — read first on every query
│   ├── log.md             # Append-only chronological record
│   ├── overview.md        # Evolving top-level synthesis
│   ├── sources/           # One summary page per raw source
│   ├── entities/          # People, orgs, places, products
│   ├── concepts/          # Ideas, frameworks, mental models
│   ├── topics/            # Broader subject areas
│   └── analyses/          # Preserved query outputs, comparisons, syntheses
└── CLAUDE.md              # This file
```

### Page Types & Frontmatter

Every wiki page must have YAML frontmatter with at minimum `type` and `updated`.

**Source summary** (`wiki/sources/<slug>.md`):
```yaml
---
type: source-summary
title: "Full title of the original source"
source_file: raw/filename.md   # relative path to the raw file
date_ingested: YYYY-MM-DD
tags: [tag1, tag2]
updated: YYYY-MM-DD
---
```
Content: 3–5 paragraph summary of the source, key claims, notable quotes (blockquoted), and a `## Connections` section listing wiki pages updated due to this source.

**Entity page** (`wiki/entities/<slug>.md`):
```yaml
---
type: entity
name: "Display Name"
entity_type: person | organization | place | product | other
updated: YYYY-MM-DD
source_count: N
---
```
Content: Who/what it is, why it matters to this KB, recurring appearances across sources, open questions about it.

**Concept page** (`wiki/concepts/<slug>.md`):
```yaml
---
type: concept
name: "Concept Name"
updated: YYYY-MM-DD
source_count: N
---
```
Content: Definition, how this KB uses/interprets it, how it appears across sources, related concepts (wikilinked), open questions.

**Topic page** (`wiki/topics/<slug>.md`):
```yaml
---
type: topic
name: "Topic Name"
updated: YYYY-MM-DD
---
```
Content: Overview of the topic as reflected in this KB, links to relevant entities and concepts, list of sources covering it, emerging thesis.

**Analysis/output page** (`wiki/analyses/<slug>.md`):
```yaml
---
type: analysis
question: "The question this answers"
date: YYYY-MM-DD
sources_used: [wiki/sources/a, wiki/sources/b]
---
```
Content: The answer/output itself. Preserved because it's valuable enough to file back into the wiki.

### Naming Conventions

- File slugs: lowercase, hyphens, no spaces. Example: `atomic-habits.md`, `james-clear.md`
- Dates: ISO 8601 (`YYYY-MM-DD`) everywhere
- Entity/concept names in cross-references always use their wiki page's display name, not the slug

### Workflows

#### Ingest workflow

When the user drops a new source into `raw/` and asks to process it:

1. **Read** the source file in full
2. **Discuss** key takeaways with the user if they want (skip if user says batch/auto)
3. **Create** `wiki/sources/<slug>.md` — full summary, key claims, notable quotes
4. **Update** `wiki/overview.md` — revise the synthesis if this source meaningfully changes the picture
5. **Update or create** entity pages (`wiki/entities/`) for any named entities that recur or matter
6. **Update or create** concept pages (`wiki/concepts/`) for any frameworks or ideas introduced
7. **Update or create** topic pages (`wiki/topics/`) as needed
8. **Update** `wiki/index.md` — add the new source summary and any new pages; increment `source_count` and `page_count`
9. **Append** to `wiki/log.md`: `## [YYYY-MM-DD] ingest | Source Title`

A single ingest typically touches 5–15 wiki pages. Prioritize depth over speed.

#### Query workflow

When the user asks a question:

1. **Read** `wiki/index.md` to find relevant pages
2. **Read** the relevant pages (source summaries, entities, concepts, topic pages)
3. **Synthesize** an answer with citations (`[[page]]` wikilinks)
4. **Decide** if the answer is worth preserving: if yes, create `wiki/analyses/<slug>.md` and add it to the index
5. **Append** to `wiki/log.md`: `## [YYYY-MM-DD] query | Brief description of question`

#### Lint workflow

When the user asks to health-check or lint the wiki:

1. Read the full index
2. Scan all wiki pages for: contradictions, stale claims, orphan pages (no inbound links), concepts mentioned but lacking their own page, missing cross-references
3. Report findings as a prioritized list
4. Fix issues the user approves
5. Append to `wiki/log.md`: `## [YYYY-MM-DD] lint | Brief summary of findings`

### Image Handling

Raw sources may reference external images (URLs) or embed local ones. When processing a source:
- Local images already in `raw/assets/` can be read directly
- External image URLs: download to `raw/assets/<descriptive-name>.png` and update the source file's link to `![[raw/assets/descriptive-name.png]]`
- When summarizing a source, note any important images in the summary page with `![[raw/assets/...]]` embeds

### Index Maintenance Rules

`wiki/index.md` is the LLM's map. Keep it honest:
- Every wiki page must appear in the index exactly once
- One-liners must be genuinely informative (not just "page about X")
- `page_count` and `source_count` in frontmatter must be accurate after every ingest
- Sections grow in order: sources → entities → concepts → topics → analyses

### What the User Does vs What Claudian Does

| User | Claudian |
|------|----------|
| Drops files into `raw/` | Reads raw files, extracts knowledge |
| Asks questions | Searches wiki, synthesizes answers |
| Directs emphasis ("focus on X") | Follows direction, updates wiki accordingly |
| Reviews wiki pages in Obsidian | Writes and maintains all wiki pages |
| Runs lint when wiki feels stale | Finds and fixes issues |
| Reads raw sources if curious | Never modifies `raw/` files |
