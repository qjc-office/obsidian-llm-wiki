---
name: approve_ingest
description: After user approval, archive a pending_review ingest unit into inspool-wiki-en/raw/processed and repair raw references in the wiki.
---

# approve_ingest

Use this skill after the user confirms that a `pending_review` ingest unit is ready to be archived into `inspool-wiki-en/raw/processed/`.

## Steps

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/raw/index.md` and `inspool-wiki-en/wiki/log.md`.
3. Scan `inspool-wiki-en/raw/unprocessed/`.
4. Find ingest units marked `pending_review`.
5. If the user specified a batch, process that batch. Otherwise, prioritize the most recent `pending_review` batch.
6. Verify that the related wiki pages and `processed_into` fields exist.
7. Record the old and target path for each raw file in the ingest unit.
8. Update workflow fields for each raw file, for example:
   - `processing_status: processed`
   - `reviewed_at: <today>`
   - `processed_at: <today>`
9. Move the raw files into `inspool-wiki-en/raw/processed/`.
10. Repair old raw references in source pages and other wiki pages, including:
    - `raw_note` in frontmatter
    - the "raw material" link under source information
    - any hard-coded `raw/unprocessed/...` path strings
11. Run a localized self-check and confirm that `inspool-wiki-en/wiki/` no longer contains those stale paths.
12. Update `inspool-wiki-en/raw/index.md`.
13. Append an `approve` log entry to `inspool-wiki-en/wiki/log.md`.

## Constraints

- run this only after explicit user confirmation
- do not substantially rewrite wiki prose during approval
- path repair is required when raw files move
- if the self-check fails, list the stale paths explicitly instead of pretending the archive succeeded
- if the `pending_review` batch does not match current wiki records, report the mismatch first

## Output

- state which ingest unit you are archiving
- list which raw files moved into `processed/`
- state clearly whether the stale-path check passed
- explain the reason if archiving did not complete
