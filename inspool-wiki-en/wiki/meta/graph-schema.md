# Graph Schema

This page defines the knowledge-graph model for `inspool-wiki-en/`. The goal is to support all of the following inside Obsidian:

- readable page structure
- a clear graph view
- queryable Dataview relationships
- traceable evidence chains

## Core Principle

Do not treat external URLs as the primary edges in the graph.

The primary edges in the graph should connect local Markdown nodes. Recommended structure:

- `raw/`: raw source nodes
- `wiki/sources/`: evidence nodes
- `wiki/entities/`: entity nodes
- `wiki/concepts/`: concept nodes
- `wiki/synthesis/`: synthesis or judgment nodes

## Why `sources` Is the Evidence Layer

External URLs are useful as provenance records, but they are poor primary edges for an Obsidian graph.

A better pattern is:

1. keep the external URL and the original text in the raw note
2. generate a source summary page under `wiki/sources/`
3. let other knowledge pages connect to that evidence through `[[sources/...]]` or `[[sources/...#some-section]]`

That gives you:

- clickable local evidence nodes
- a more stable graph structure
- better page aggregation

## Recommended Relationship Types

### Evidence Edges

- `supports`: sources that support the key claims on the current page
- `contradicts`: sources that conflict with the key claims on the current page

### Topical Edges

- `related_entities`
- `related_concepts`
- `related_sources`
- `related_synthesis`

Maintain these relationship fields in frontmatter and also reflect them through explicit wiki links in the body.

## Recommended Linking Style

### Page-Level Evidence Links

Useful in frontmatter:

```yaml
supports:
  - "[[sources/Some Source]]"
```

### Section-Level Evidence Links

Useful in the body:

```md
An important judgment. See [[sources/Some Source#Key Points]].
```

Page-level links help with structured queries; section-level links help with manual verification.

## Suggested Page Responsibilities

### Source Pages

Primary responsibilities:

- summarize a single source
- link back to the raw note
- extract entities, concepts, and related sources
- serve as evidence nodes for other pages

### Entity Pages / Concept Pages

Primary responsibilities:

- form the thematic skeleton
- maintain `supports` and `contradicts`
- connect adjacent concepts and entities

### Synthesis Pages

Primary responsibilities:

- capture conclusions across multiple sources
- show supporting and conflicting evidence explicitly
- connect related entities, concepts, and sources

## Minimum Viable Rules

If you do not want to maintain too many fields at first, at least ensure:

1. every synthesis page has `supports`
2. every key concept page has at least one source-page link
3. key conclusions in the body can land on `[[sources/...#some-section]]`

## Anti-Patterns

Avoid these patterns:

- dumping large numbers of bare URLs into knowledge pages
- writing conclusions in the body without any local source links
- storing relationships only in frontmatter with no explicit body links
- splitting every tiny claim into its own page and fragmenting the graph

## Dataview Query Examples

### Find Synthesis Pages With No Supporting Evidence

```dataview
TABLE file.link AS Page
FROM "inspool-wiki-en/wiki/synthesis"
WHERE !supports OR length(supports) = 0
```

### Find Entity Pages With No Related Sources

```dataview
TABLE file.link AS Page
FROM "inspool-wiki-en/wiki/entities"
WHERE !related_sources OR length(related_sources) = 0
```

### Find Pages With Contradicting Sources

```dataview
TABLE file.link AS Page, contradicts AS ConflictingSources
FROM "inspool-wiki-en/wiki"
WHERE contradicts AND length(contradicts) > 0
```
