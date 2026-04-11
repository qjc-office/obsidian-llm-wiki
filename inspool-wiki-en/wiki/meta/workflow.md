---
type: meta
status: active
updated: 2026-04-11
related_sources: []
related_synthesis: []
---

# Workflow Overview

## Purpose

This page summarizes the full workflow of `inspool-wiki-en/` and maps each step to the project skill or command file, so the workflow can be repeated without renegotiating it every time.

## End-to-End Flow

1. Put raw material into `inspool-wiki-en/raw/unprocessed/`.
2. If needed, group files into one ingest unit with subfolders or `ingest_group`.
3. Run ingest to compile raw material into the wiki, but do not archive it yet.
4. Review the result.
5. After approval, run approve to move raw files into `raw/processed/` and repair raw references inside the wiki.
6. Use the wiki for daily Q&A instead of going back to raw material for one-off summaries.
7. Write high-value answers back into the wiki.
8. Run lint periodically to inspect structure, evidence chains, workflow state, and graph relationships.

## Ingest Units

The default processing unit is not always a single file. It is an ingest unit.

An ingest unit can be:

- one standalone raw note
- a small group of clearly related raw notes

Priority for deciding the unit:

1. files in the same subfolder under `raw/unprocessed/`
2. files with the same `ingest_group`
3. files the user explicitly wants handled together
4. otherwise, one file at a time

Recommended batch size: 2 to 5 files.

## State Machine

Raw material moves through these states:

- `unprocessed`
- `pending_review`
- `processed`

Notes:

- `ingest` only advances raw material to `pending_review`
- `approve` advances raw material to `processed`
- moving directly from `unprocessed` to `processed` is not allowed without user confirmation

## Core Operations

### 1. ingest_raw

Select the next ingest unit, create or update `sources / entities / concepts / synthesis`, update indexes and logs, and mark raw files as `pending_review`.

### 2. approve_ingest

Locate the latest `pending_review` raw batch, verify that the corresponding wiki pages exist, then move raw files into `raw/processed/`, repair raw-path references in wiki pages, and update indexes and logs.

### 3. query_wiki

Read `wiki/index.md` first, answer from the existing wiki pages, and cite local `sources` pages for important judgments.

### 4. inspool

Write a worthwhile answer back into the wiki as a durable page, usually under `synthesis/`, and update the index and log.

### 5. lint_wiki

Check for orphan pages, weak evidence, duplicates, stale paths, missing relationship fields, `pending_review` backlog, and `supports / contradicts / related_*` graph fields.

## Graph Rules

Current graph-layer convention:

- `raw/`: source-material layer
- `wiki/sources/`: evidence nodes
- `wiki/entities/` and `wiki/concepts/`: knowledge skeleton
- `wiki/synthesis/`: higher-level judgments

Prefer citing key conclusions through:

- `[[sources/Some Source]]`
- `[[sources/Some Source#Some Section]]`

External URLs should not serve as the main graph edges.

## User-Managed Classification

Users may create subfolders and move pages under:

- `wiki/concepts/`
- `wiki/entities/`
- `wiki/synthesis/`

This is normal maintenance behavior. Agents must remain compatible with these subfolders and should not treat them as immutable semantic truth.

## Default Language

Unless the user explicitly asks otherwise:

- wiki pages should default to English
- indexes, logs, docs, and command templates should default to English
- raw source material may stay in its original language

## Recommended Operating Sequence

For routine new-source handling, use this order:

1. import raw material into `raw/unprocessed/`
2. run `ingest_raw`
3. review the newly created or updated wiki pages
4. run `approve_ingest` after approval
5. answer future questions through `query_wiki`
6. run `inspool` for high-value answers
7. run `lint_wiki` periodically
