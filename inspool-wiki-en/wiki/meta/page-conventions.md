# Page Conventions

This page summarizes the recommended writing style for pages under `inspool-wiki-en/wiki/`.

## General Requirements

- write in English by default unless the user explicitly requests another language
- keep titles clear
- keep paragraphs short
- prefer `[[wiki links]]`
- attach source-page references to important conclusions when possible
- mark uncertain material explicitly
- prefer linking key evidence to local `sources` pages instead of external URLs

## Graph-Friendly Writing

To build a cleaner relationship graph in Obsidian, maintain both:

1. explicit wiki links in the body
2. structured relationship fields in frontmatter

The body helps with reading flow and graph display; frontmatter helps with Dataview queries and automation.

## User-Managed Classification

Users may create subfolders manually under:

- `wiki/concepts/`
- `wiki/entities/`
- `wiki/synthesis/`

These subfolders are an organizational layer, not the knowledge relationship itself.

Recommendations:

- keep page relationships primarily in `[[wiki links]]` and frontmatter fields
- do not write a subfolder path into the prose as if it were a knowledge fact
- prefer Dataview queries that target the top-level type directory instead of a fixed nested subfolder
- when pages move for classification reasons, update the index page first instead of rewriting many body paths

## Recommended Frontmatter

If you want to use Dataview later, add YAML frontmatter gradually, for example:

```yaml
---
type: source
status: active
topic: ai-agents
updated: 2026-04-11
---
```

If you want a richer graph, adopt the fuller frontmatter earlier.

### Example Frontmatter for Source Pages

```yaml
---
type: source
status: active
topic: ai-agents
updated: 2026-04-11
raw_note: "[[inspool-wiki-en/raw/unprocessed/Some Raw Note]]"
external_url: https://example.com/article
related_entities:
  - "[[entities/Some Entity]]"
related_concepts:
  - "[[concepts/Inspool Wiki]]"
related_sources: []
---
```

### Example Frontmatter for Entity / Concept / Synthesis Pages

```yaml
---
type: concept
status: active
topic: ai-agents
updated: 2026-04-11
supports:
  - "[[sources/Some Source]]"
contradicts: []
related_entities:
  - "[[entities/Some Entity]]"
related_concepts:
  - "[[concepts/Agent Workflow]]"
related_sources:
  - "[[sources/Another Source]]"
related_synthesis: []
---
```

`supports` and `contradicts` are the two most important fields and should be maintained first.

Use a vault-resolvable wiki link for `raw_note`, ideally with the full path and always pointing to the current real location of the raw file, for example:

`"[[inspool-wiki-en/raw/unprocessed/Some Raw Note]]"`

Additional rules:

- during ingest, `raw_note` usually points to `raw/unprocessed/`
- after approval and movement, it must be updated to `raw/processed/`
- if the body contains a "raw material" link, update it together with `raw_note`

## Suggested Template for Source Pages

```md
---
type: source
status: active
topic:
updated:
raw_note:
external_url:
related_entities: []
related_concepts: []
related_sources: []
---

# Title

## Source Information

## Core Summary

## Key Points

Prefer adding anchors to key judgments, for example:

- An important judgment. See [[sources/This Page Title#Key Points]]

## Evidence Excerpts

## Extractable Entities

## Extractable Concepts

## Connections to the Existing Wiki

## Open Questions

## Related Sources
```

## Suggested Template for Entity Pages

```md
---
type: entity
status: active
topic:
updated:
supports: []
contradicts: []
related_entities: []
related_concepts: []
related_sources: []
related_synthesis: []
---

# Entity Name

## One-Line Definition

## Background

## Key Attributes

## Supporting Evidence

- [[sources/Some Source#Key Points]]

## Conflicts and Disagreements

## Related Concepts

## Related Sources

## Open Questions
```

## Suggested Template for Concept Pages

```md
---
type: concept
status: active
topic:
updated:
supports: []
contradicts: []
related_entities: []
related_concepts: []
related_sources: []
related_synthesis: []
---

# Concept Name

## Definition

## Core Mechanism

## Difference From Neighboring Concepts

## Supporting Evidence

## Limits or Counterexamples

## Related Entities

## Related Sources
```

## Suggested Template for Synthesis Pages

```md
---
type: synthesis
status: active
topic:
updated:
supports: []
contradicts: []
related_entities: []
related_concepts: []
related_sources: []
related_synthesis: []
---

# Topic Name

## Topic Description

## Summary of Conclusions

## Supporting Evidence

## Conflicts and Disagreements

## Current Judgment

## Open Questions

## Next Suggestions

## Related Sources
```

## Citation Rules

- prefer linking key conclusions to local source pages such as `[[sources/Some Source]]`
- if you can point to a specific section, prefer anchors such as `[[sources/Some Source#Key Points]]`
- keep external URLs mainly inside the "Source Information" section of source pages
- do not fabricate `supports` when evidence is weak
- if the user reclassifies page folders, prefer relying on Obsidian-updated wiki links instead of manually hard-coding old paths

## Dataview Examples

### Find Concept Pages Without `supports`

```dataview
TABLE file.link AS Page
FROM "inspool-wiki-en/wiki/concepts"
WHERE !supports OR length(supports) = 0
```

This query automatically covers all subfolders under `concepts/`, which fits user-managed classification.

### Find Synthesis Pages With Conflicting Sources

```dataview
TABLE file.link AS Page, contradicts AS ConflictingSources
FROM "inspool-wiki-en/wiki/synthesis"
WHERE contradicts AND length(contradicts) > 0
```
