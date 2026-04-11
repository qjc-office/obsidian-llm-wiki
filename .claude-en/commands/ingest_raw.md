---
name: ingest_raw
description: Process the next ingest unit in inspool-wiki-en/raw/unprocessed, update the wiki, and mark the raw files as pending_review.
---

# Command: ingest_raw

You are the knowledge-maintenance agent for `inspool-wiki-en/`. Follow `inspool-wiki-en/AGENTS.md` strictly.

Task for this run: process the next ingest unit in `inspool-wiki-en/raw/unprocessed/` and integrate it into the wiki.

Important: this run performs ingest only. Do not archive raw files yet. They must remain in `pending_review` until the user confirms the result.

Execute in this order:

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/raw/index.md`, `inspool-wiki-en/wiki/index.md`, and `inspool-wiki-en/wiki/log.md`.
3. Scan `inspool-wiki-en/raw/unprocessed/`.
4. Select the next ingest unit using this priority:
   - files in the same subfolder
   - files sharing the same `ingest_group`
   - otherwise, a single file
5. If multiple ingest units are possible, choose the oldest queued batch; if that is unclear, choose the first in lexical order.
6. If the selected unit is a batch, verify that the files are actually related. If the batch is too large, split it into smaller batches of 2 to 5 files.
7. Extract the core information from that source or source batch.
8. Create or update the relevant source summary pages in `inspool-wiki-en/wiki/sources/` and treat them as evidence nodes.
9. Create or update the related entity, concept, and synthesis pages.
10. Add necessary `[[wiki links]]`, and prefer linking key claims to `[[sources/...]]` or `[[sources/...#some-section]]`.
11. Maintain frontmatter relationship fields on core pages such as `supports`, `contradicts`, `related_entities`, `related_concepts`, and `related_sources`.
12. Update `inspool-wiki-en/wiki/index.md`.
13. Append an `ingest` log entry to `inspool-wiki-en/wiki/log.md`.
14. Update workflow fields for every raw file in the ingest unit, for example:
   - `processing_status: pending_review`
   - `reviewed_at:`
   - `processed_at:`
   - `processed_into: <corresponding source page path>`
15. Update `inspool-wiki-en/raw/index.md`.
16. Tell the user explicitly that this batch is now in `pending_review`.

Constraints:

- the body content under `raw/` is read-only by default
- attach source-page references to important conclusions whenever possible
- prefer local source pages over external URLs as primary graph edges
- remain compatible with user-created subfolders under `wiki/concepts/`, `wiki/entities/`, and `wiki/synthesis/`
- mark uncertain material as "to be confirmed" or an open question
- if a new source conflicts with older pages, record the conflict explicitly instead of silently overwriting it
- do not stop after creating a single summary page; maintain the related pages together
- raw files may be marked `pending_review` only after `wiki/index.md`, `wiki/log.md`, and `raw/index.md` are all updated successfully
- process one ingest unit at a time; if it is a batch, keep it small and coherent
- do not move raw files to `processed/` without user approval

If `inspool-wiki-en/raw/unprocessed/` is empty:

- say clearly that there is nothing to process
- do not create empty logs or pages
- stop there

Output requirements:

- summarize which ingest unit you selected and which pages you plan to update
- summarize what was added or updated
- state clearly which raw files are now in `pending_review`
- call out insufficient evidence or naming conflicts if you find them
