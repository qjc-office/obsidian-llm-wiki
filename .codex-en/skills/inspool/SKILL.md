---
name: inspool
description: Write a high-value answer or intermediate conclusion back into the wiki, usually under inspool-wiki-en/wiki/synthesis.
---

# inspool

Use this skill to turn a high-value answer into a durable wiki page instead of leaving it in chat history.

## Typical Use Cases

- a comparison across multiple sources
- an intermediate research conclusion
- a Q&A summary worth keeping long-term

## Steps

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/wiki/index.md`.
3. Decide which directory fits the content best, usually `inspool-wiki-en/wiki/synthesis/`.
4. Create or update the target page.
5. Add wiki links to related pages, and prefer linking key claims to local source pages.
6. Add or update `supports`, `contradicts`, and `related_*` fields in frontmatter.
7. Update `inspool-wiki-en/wiki/index.md`.
8. Append a `query` or `refactor` log entry to `inspool-wiki-en/wiki/log.md`.

## Writing Requirements

- distinguish clearly between evidence, synthesized conclusions, and open questions
- if the current material is weakly supported, do not present it as a final conclusion
- avoid dumping many external URLs into the body; prefer routing evidence through local `sources` pages
