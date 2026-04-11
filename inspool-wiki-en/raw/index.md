# Raw Index

This page tracks the processing queue and completed records under `inspool-wiki-en/raw/`.

This is a workflow view, not a content index. For content navigation, see `inspool-wiki-en/wiki/index.md`.

Note: the default processing unit is not always a single file. It is an ingest unit, which can be one file or a clearly related group of files.

## Queue Overview

- Unprocessed: 0
- Pending review: 0
- Processed: 0
- Latest completed: none

## Unprocessed Items

- None

## Pending Review

- None

## Processed Items

- None

## Maintenance Rules

- new material goes into `unprocessed/`
- each ingest run processes one ingest unit by default
- successful ingest moves items to `pending_review`
- items move to `processed/` only after user approval
- if the raw body is unchanged and only workflow metadata is updated, it still counts as part of the source-material layer
