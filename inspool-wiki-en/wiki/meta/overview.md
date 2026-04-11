# Workspace Overview

## Goal

Turn raw material within a bounded topic area into a structured, traceable, and continuously maintainable English wiki.

## Current Stage

This workspace is still in the initialization stage. The folders and rules are ready, but the first real source has not yet been ingested.

The workflow is already defined:

- new material enters `raw/unprocessed/`
- the agent processes it incrementally
- successful ingest moves it to `pending_review`
- user approval moves it into `raw/processed/`
- status tracking lives in `raw/index.md`

## Recommended Way To Start

Start with a narrow topic. Do not try to cover the whole vault at once.

Suggested order:

1. define a topic boundary
2. import the first source into `raw/unprocessed/`
3. generate the corresponding source page
4. extract entities and concepts
5. update the index and the log
6. iterate continuously

## Current TODO

- choose the first experimental topic
- add the first source
- run the first ingest flow
