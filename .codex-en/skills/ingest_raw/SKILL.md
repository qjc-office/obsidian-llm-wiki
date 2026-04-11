---
name: ingest_raw
description: Process the next ingest unit in inspool-wiki-en/raw/unprocessed, update the wiki, and mark the raw files as pending_review.
---

# ingest_raw

Use this skill to process the next ingest unit in `inspool-wiki-en/raw/unprocessed/` and integrate it into the wiki.

## Steps

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/raw/index.md`, `inspool-wiki-en/wiki/index.md`, and `inspool-wiki-en/wiki/log.md`.
3. Scan `inspool-wiki-en/raw/unprocessed/`.
4. Select the next ingest unit using this priority:
   - files in the same subfolder
   - files sharing the same `ingest_group`
   - otherwise, a single file
5. If multiple candidates exist, prefer the oldest queued batch; if that is unclear, choose the first lexically.
6. If the selection is a batch, confirm that the files are actually related. If the batch is too large, split it into smaller 2-to-5-file batches.
7. Extract the core information from the source or source batch.
8. Create or update the corresponding summary pages in `inspool-wiki-en/wiki/sources/` and treat them as evidence nodes.
9. Create or update the relevant entity, concept, and synthesis pages.
10. Add the necessary `[[wiki links]]`, and prefer linking important claims to `[[sources/...]]` or `[[sources/...#some-section]]`.
11. Maintain frontmatter relationship fields such as `supports`, `contradicts`, `related_entities`, `related_concepts`, and `related_sources`.
12. Update `inspool-wiki-en/wiki/index.md`.
13. Append an `ingest` log entry to `inspool-wiki-en/wiki/log.md`.
14. Update workflow fields for every raw file in the ingest unit, for example:
    - `processing_status: pending_review`
    - `reviewed_at:`
    - `processed_at:`
    - `processed_into: <corresponding source page path>`
15. Update `inspool-wiki-en/raw/index.md`.
16. Tell the user explicitly that the batch is now in `pending_review`.

## Constraints

- the body content under `raw/` is read-only by default
- attach source-page references to important conclusions whenever possible
- prefer local source pages over external URLs as primary graph edges
- remain compatible with user-created subfolders under `wiki/concepts/`, `wiki/entities/`, and `wiki/synthesis/`
- mark uncertain material as "to be confirmed" or an open question
- record conflicts explicitly when new material contradicts older pages
- do not stop after generating one summary page; maintain the related pages together
- raw files may be marked `pending_review` only after `wiki/index.md`, `wiki/log.md`, and `raw/index.md` are all updated successfully
- process one ingest unit at a time; if it is a batch, keep it small and coherent
- do not move raw files to `processed/` without user approval

## Empty Queue

If `inspool-wiki-en/raw/unprocessed/` is empty:

- say clearly that there is nothing to process
- do not create empty logs or pages
- stop there

## Output

- summarize which ingest unit you selected and which pages you plan to update
- summarize what was added or updated
- state clearly which raw files are now in `pending_review`
- call out insufficient evidence or naming conflicts if you find them
