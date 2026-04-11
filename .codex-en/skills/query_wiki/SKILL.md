---
name: query_wiki
description: Answer from existing pages in inspool-wiki-en/wiki, preferring local source pages over raw notes or one-off summaries from external URLs.
---

# query_wiki

Use this skill to answer from the existing wiki instead of bypassing it and creating a one-off summary from raw material.

## Steps

1. Read `inspool-wiki-en/AGENTS.md`.
2. Review `inspool-wiki-en/wiki/index.md` first.
3. Locate the relevant pages based on the question.
4. Read the necessary source, entity, concept, and synthesis pages.
5. Produce a synthesized answer grounded in the existing wiki.
6. For important judgments, prefer local source-page citations, ideally with section anchors such as `[[sources/Some Source#Key Points]]`.

## When To Suggest Writing Back

If the answer produces a durable conclusion, such as:

- a clear comparison
- a stable topical overview
- a synthesis across multiple sources

then suggest writing the result into `inspool-wiki-en/wiki/synthesis/`.

## Answer Requirements

- distinguish between established facts, current synthesis, and open questions
- do not present unsupported material as settled fact
- do not prefer external URLs when a local source page already exists
