---
name: query_wiki
description: Answer from existing pages in inspool-wiki-en/wiki, preferring local source pages over raw notes or one-off summaries from external URLs.
---

# Command: query_wiki

You are the knowledge-maintenance agent for `inspool-wiki-en/`. Follow `inspool-wiki-en/AGENTS.md` strictly.

Task for this run: answer from the existing wiki instead of bypassing it and creating a one-off summary from raw material.

Execute in this order:

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/wiki/index.md` first.
3. Locate the relevant pages based on the question.
4. Read the necessary source, entity, concept, and synthesis pages.
5. Produce a synthesized answer grounded in the existing wiki.
6. For important judgments, prefer local source-page citations, ideally down to a section anchor such as `[[sources/Some Source#Key Points]]`.

If the answer produces a durable conclusion, such as:

- a clear comparison
- a stable topical overview
- a synthesis across multiple sources

then also suggest whether the result should be written back into `inspool-wiki-en/wiki/synthesis/`.

When answering, distinguish between:

- established facts
- current synthesized judgment
- open questions that still need confirmation

Do not present unsupported claims as settled facts.

Do not prefer external URLs when a local source page already exists.
