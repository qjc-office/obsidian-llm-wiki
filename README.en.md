English | [中文](./README.md)

# Inspool Wiki for Obsidian

This project is directly inspired by [Andrej Karpathy's original "LLM Wiki" note](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). The core idea is simple and powerful: instead of asking an LLM to repeatedly re-read raw documents and assemble one-off answers, let the LLM maintain a living wiki that sits between you and the source material.

Inspool Wiki is not a literal copy of that idea file. It turns the idea into a practical, Obsidian-friendly workflow with explicit structure, agent rules, review gates, and a clearer ingest lifecycle.

If you need the Chinese edition, see [README.md](./README.md).

## What Problem This Solves

Most people still use LLMs for document work in a "re-understand everything every time" mode:

- throw a pile of documents at the model
- retrieve something at question time
- generate a one-off answer
- lose the structure after the conversation ends

This project takes a different approach:

- raw material enters `raw/`
- the LLM continuously compiles that material into `wiki/`
- new material updates the existing knowledge structure instead of starting over
- high-value answers can be written back into the wiki

The result is a long-lived Obsidian wiki that gets steadily refined over time.

## What This Project Adds

Compared with the original "LLM Wiki" idea, this project keeps the three-layer model of raw sources, wiki pages, and schema-like conventions, while adding:

- an Obsidian-oriented folder layout and wiki-link style
- [AGENTS.md](./inspool-wiki-en/AGENTS.md) for Codex and [CLAUDE.md](./inspool-wiki-en/CLAUDE.md) for Claude
- reusable `commands/` and `skills/` stored in `.claude-en/` and `.codex-en/`
- an explicit `unprocessed -> pending_review -> processed` state machine
- a review-first archive flow, so raw material moves only after user approval
- support for small related batches as one ingest unit
- compatibility with user-managed subfolders under `concepts / entities / synthesis`
- a `lint_wiki` check for stale paths, weak claims, and missing relationship fields

## Directory Layout

```text
.claude-en/
├─ commands/              # Claude Code commands (English)
└─ skills/                # Obsidian official skills (shared)
.codex-en/
└─ skills/
   ├─ {core}/             # Core skills (5)
   └─ {obsidian}/         # Obsidian official skills (shared, 5)
inspool-wiki-en/
├─ AGENTS.md
├─ CLAUDE.md
├─ raw/
│  ├─ unprocessed/
│  ├─ processed/
│  ├─ assets/
│  ├─ index.md
│  └─ README.md
└─ wiki/
   ├─ sources/
   ├─ entities/
   ├─ concepts/
   ├─ synthesis/
   ├─ meta/
   ├─ index.md
   └─ log.md
```

Directory roles:

- `.claude-en/commands/`: Claude Code command templates.
- `.codex-en/skills/`: Codex core skills + Obsidian official skills.
- `.claude-en/skills/`: Obsidian official skills (shared).
- `raw/`: source-material layer; the body is read-only by default, except for minimal workflow metadata.
- `wiki/`: structured knowledge layer; source pages, entity pages, concept pages, and synthesis pages live here.
- `AGENTS.md` / `CLAUDE.md`: rule entry points for agents.

## Recommended Capture Flow

The preferred capture method is **Obsidian Web Clipper**.

Recommended setup:

1. Install the Obsidian Web Clipper browser extension.
2. Set the save folder to `inspool-wiki-en/raw/unprocessed/`.

Optional recommendations:

- if article images matter, download them into `raw/assets/`
- preserve original titles, URLs, and dates whenever possible
- avoid manually rewriting the body of raw notes

## Standard Workflow

### 1. Capture

Put new source material into `raw/unprocessed/`.

Typical inputs include:

- web clippings
- articles or book excerpts
- meeting notes
- PDF transcripts
- other Markdown documents worth keeping long-term

### 2. Ingest

Run `ingest_raw`.

It will:

- select the next ingest unit
- create or update source pages
- update related entity, concept, and synthesis pages
- update `wiki/index.md`
- append to `wiki/log.md`
- update `raw/index.md`
- mark the current raw batch as `pending_review`

### 3. Review

Review the ingest result.

Focus on:

- page naming
- whether links are valid
- whether conclusions are overstated
- whether key concepts or entities were missed
- whether evidence is sufficient

### 4. Approve

If the ingest result looks correct, run `approve_ingest`.

It will:

- change the relevant raw files from `pending_review` to `processed`
- move them into `raw/processed/`
- repair raw-path references in wiki pages
- update `raw/index.md`
- append to `wiki/log.md`

Key constraint:

**raw files move only after explicit user approval.**

### 5. Query

For daily Q&A, run `query_wiki` and answer from the existing wiki first instead of bypassing it and going straight back to raw material.

### 6. Inspool

If a response is especially valuable, run `inspool` to write it back into the wiki, usually under `wiki/synthesis/`.

### 7. Lint

Run `lint_wiki` periodically to check:

- orphan pages
- stale path references
- strong claims without source support
- missing relationship fields
- broken assumptions after user-driven page reclassification
- mismatches between `raw/index.md` and the actual directory state

## Working Rules

To keep the project maintainable over time:

- treat `raw/` as the factual source layer and avoid rewriting the body casually
- treat `wiki/` as the synthesis layer that can evolve over time
- prefer linking key claims to local `sources` pages rather than external URLs
- mark uncertain material as "to be confirmed" or an open question
- record conflicts explicitly instead of silently overwriting old conclusions
- allow users to create subfolders under `concepts / entities / synthesis`
- prefer Obsidian wiki links over hard-coded deep paths

## Ingest Units

The default processing unit is not always "one file". It is an ingest unit.

An ingest unit can be:

- one standalone source
- a small set of clearly related sources

Priority for grouping:

1. files inside the same subfolder under `raw/unprocessed/`
2. files with the same `ingest_group`
3. files the user explicitly wants processed together
4. otherwise, treat each file separately

Recommended batch size: 2 to 5 files.

## Graph Model

This project aims to build a browsable knowledge graph inside Obsidian.

Recommended layers:

- `raw/`: raw source nodes
- `wiki/sources/`: evidence nodes
- `wiki/entities/`: entity nodes
- `wiki/concepts/`: concept nodes
- `wiki/synthesis/`: synthesis or judgment nodes

Key principles:

- external URLs are not the primary graph edges
- important relationships should connect local Markdown pages
- `sources` acts as the evidence anchor layer
- `supports / contradicts / related_*` capture structured relationships in frontmatter
- body-level wiki links support reading flow and graph visualization

## Commands and Skills

Codex skills:

- [ingest_raw](./.codex-en/skills/ingest_raw/SKILL.md)
- [approve_ingest](./.codex-en/skills/approve_ingest/SKILL.md)
- [query_wiki](./.codex-en/skills/query_wiki/SKILL.md)
- [inspool](./.codex-en/skills/inspool/SKILL.md)
- [lint_wiki](./.codex-en/skills/lint_wiki/SKILL.md)

Claude Code commands:

- [ingest_raw](./.claude-en/commands/ingest_raw.md)
- [approve_ingest](./.claude-en/commands/approve_ingest.md)
- [query_wiki](./.claude-en/commands/query_wiki.md)
- [inspool](./.claude-en/commands/inspool.md)
- [lint_wiki](./.claude-en/commands/lint_wiki.md)

Shared Obsidian skills (provided by the official Obsidian project):

- [defuddle](./.codex-en/skills/defuddle/SKILL.md)
- [json-canvas](./.codex-en/skills/json-canvas/SKILL.md)
- [obsidian-bases](./.codex-en/skills/obsidian-bases/SKILL.md)
- [obsidian-cli](./.codex-en/skills/obsidian-cli/SKILL.md)
- [obsidian-markdown](./.codex-en/skills/obsidian-markdown/SKILL.md)

## Codex and Claude

- Codex users should start from [AGENTS.md](./inspool-wiki-en/AGENTS.md) with skills under `.codex-en/skills/`.
- Claude users should start from [CLAUDE.md](./inspool-wiki-en/CLAUDE.md) with commands under `.claude-en/commands/`.

## Quick Start

1. Copy `inspool-wiki-en/` into your Obsidian vault.
2. **Rename `.claude-en/` to `.claude/` and `.codex-en/` to `.codex/`** so that the commands and skills take effect in your project directory.
3. Configure Obsidian Web Clipper to save into `inspool-wiki-en/raw/unprocessed/`.
4. Point your agent to the rule file:
   - Codex: `inspool-wiki-en/AGENTS.md`
   - Claude: `inspool-wiki-en/CLAUDE.md`
5. Clip your first article.
6. Run `ingest_raw`.
7. Review it, then run `approve_ingest`.
8. Keep the wiki alive with `query_wiki`, `inspool`, and `lint_wiki`.

## Recommended Obsidian Plugins

| Plugin | Purpose |
|--------|---------|
| **Claudian** | Call Claude directly inside Obsidian; use this project's commands to run ingest, query, and other workflows from the editor |
| **Templater** | Template engine for quickly creating pages that follow this project's conventions (source, entity, concept, synthesis pages) |
| **Dataview** | Dynamic query views based on frontmatter fields; useful for browsing `supports`, `contradicts`, and other relationships |
| **Outliner** | Enhanced list and outline experience; fits the hierarchical structure and folding navigation of wiki pages |
| **Another Quick Switcher** | Improved quick switcher with fuzzy search and wiki-link jumping for faster navigation across wiki pages |
| **File Explorer Note Count** | Shows note counts per folder in the file explorer; gives a quick view of how `sources/`, `entities/`, and other directories are growing |
| **Custom Attachment Location** | Custom attachment save path; configure it to save images into `raw/assets/` consistently |

## Good Next Extensions

- introduce stricter graph relationship checks
- add domain-specific page templates
- further optimize prompts for Claude and Codex separately
