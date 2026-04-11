# Raw Material Notes

This directory stores raw source material that has not been rewritten by the LLM, plus a small amount of workflow metadata.

## Rules

- `unprocessed/`: newly imported items waiting to be handled
- `processed/`: items that completed ingest and approval
- `assets/`: images, attachments, screenshots, and other static source assets
- `index.md`: processing-state index

## Recommended Usage

- place web clippings into `unprocessed/` first
- if article images matter, localize them into `assets/`
- preserve original titles, URLs, dates, and other source context when possible
- after ingest, items should first move into `pending_review`
- the agent should move items to `processed/` only after explicit user approval

## Important Note

This directory is the factual source layer. Unless the user explicitly asks for it, agents should not rewrite the body content here.

Allowed workflow updates:

- add or update `processing_status` in frontmatter
- write `reviewed_at`
- write `processed_at`
- write `processed_into`
- optionally write `ingest_group`
- move files between `unprocessed/` and `processed/`
- update `index.md`

## Batch Ingest Recommendation

If several raw notes should be ingested together, use either of these approaches:

- place them in the same subfolder under `unprocessed/`
- assign them the same `ingest_group`

During parameter-free ingest, the agent should prioritize treating them as one batch.
