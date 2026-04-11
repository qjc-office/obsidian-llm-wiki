---
name: approve_ingest
description: After user approval, archive a pending_review ingest unit into inspool-wiki-en/raw/processed and repair raw references in the wiki.
---

# Command: approve_ingest

You are the knowledge-maintenance agent for `inspool-wiki-en/`. Follow `inspool-wiki-en/AGENTS.md` strictly.

Task for this run: after the user has explicitly confirmed that the ingest result is correct, archive the corresponding `pending_review` ingest unit into `inspool-wiki-en/raw/processed/`.

Execute in this order:

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/raw/index.md` and `inspool-wiki-en/wiki/log.md`.
3. Scan `inspool-wiki-en/raw/unprocessed/`.
4. Find ingest units currently marked `pending_review`.
5. If the user specified a batch, process that batch. Otherwise, prioritize the most recently updated `pending_review` batch.
6. Verify that the corresponding wiki pages and `processed_into` fields exist.
7. Record the old and new path for every raw file in that ingest unit.
8. Update workflow fields for each raw file, for example:
   - `processing_status: processed`
   - `reviewed_at: <today>`
   - `processed_at: <today>`
9. Move those raw files into `inspool-wiki-en/raw/processed/`.
10. Repair old raw references in source pages and other wiki pages, including at minimum:
   - `raw_note` in frontmatter
   - the "raw material" link under source information
   - any other hard-coded `raw/unprocessed/...` path strings
11. Run a localized self-check to confirm that `inspool-wiki-en/wiki/` no longer contains those old paths.
12. Update `inspool-wiki-en/raw/index.md`.
13. Append an `approve` log entry to `inspool-wiki-en/wiki/log.md`.

Constraints:

- run this flow only after explicit user approval
- do not substantially rewrite wiki prose during approval
- you must repair path references broken by raw-file movement
- if the path self-check fails, report the remaining stale paths explicitly instead of silently treating the archive as successful
- if the `pending_review` batch does not match existing wiki records, report the mismatch before taking action

Output requirements:

- state which ingest unit you are archiving
- list which raw files moved into `processed/`
- state clearly whether the stale-path check passed
- if archiving was blocked, explain why
