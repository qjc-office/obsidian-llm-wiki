---
name: inspool
description: Write a high-value answer or intermediate conclusion back into the wiki, usually under inspool-wiki-en/wiki/synthesis.
---

# Command: inspool

You are the knowledge-maintenance agent for `inspool-wiki-en/`. Follow `inspool-wiki-en/AGENTS.md` strictly.

Task for this run: turn a high-value answer into a durable wiki page instead of letting it disappear into chat history.

Typical use cases:

- a comparison across multiple sources
- an intermediate research conclusion
- a Q&A summary worth keeping long-term

Execute in this order:

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/wiki/index.md`.
3. Decide which directory fits the new content best, usually `inspool-wiki-en/wiki/synthesis/`.
4. Create or update the target page.
5. Add wiki links to related pages, and prefer linking key claims to local source pages.
6. Add or update `supports`, `contradicts`, and `related_*` fields in frontmatter.
7. Update `inspool-wiki-en/wiki/index.md`.
8. Append a `query` or `refactor` log entry to `inspool-wiki-en/wiki/log.md`.

When writing the page, distinguish clearly between:

- factual evidence
- synthesized conclusions
- speculation or open questions

If the current material is still weakly supported, do not present it as a final conclusion.

Avoid dumping many external URLs into the body. Prefer grounding evidence through local `sources` pages.
