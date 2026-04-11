---
name: lint_wiki
description: Check structure, evidence chains, relationship fields, and raw-state consistency for inspool-wiki-en/wiki, and fix low-risk issues.
---

# lint_wiki

Use this skill to run a structural and content-health audit across the entire wiki.

## Steps

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/wiki/index.md`.
3. Scan all page types under `inspool-wiki-en/wiki/`.
4. Identify issues such as:
   - orphan pages
   - strong claims without source support
   - outdated conclusions invalidated by newer material
   - high-frequency concepts or entities that still lack their own pages
   - duplicate pages or inconsistent naming
   - clearly underlinked pages
   - knowledge pages that rely directly on external URLs instead of local source pages
   - missing `supports`, `contradicts`, or `related_*` fields on core pages
   - pages where relationships exist only in frontmatter but not in body links
   - source pages missing `raw_note` or `external_url`
   - stale `raw/unprocessed/` strings left behind after approval
   - broken assumptions after user reclassification under `concepts / entities / synthesis`
   - mismatches between `raw/index.md` and the actual contents of `raw/unprocessed/` and `raw/processed/`
   - raw material left in `pending_review` for too long
5. Do not flag path strings that appear only as examples inside `wiki/meta/`; prioritize active leftovers in real source and knowledge pages.
6. Produce a prioritized issue list.
7. Fix low-risk structural issues directly when appropriate.
8. Append the lint result to `inspool-wiki-en/wiki/log.md`.

## Output Structure

- findings
- auto-fixed items
- recommended next steps
