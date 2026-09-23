---
type: Concept
title: Knowledge Graph with Provenance
description: >-
  Enriching the knowledge graph with schema nodes and provenance metadata so
  every entity, relation, and chunk traces back to its source.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Knowledge Graph with Provenance

**What it is:** the knowledge graph is enriched with schema nodes and provenance metadata so that every entity traces back to its source.

## Graph shape

A **Workspace** node (a LightRAG workspace) links to multiple **Data Source** nodes — for example `PostgreSQL prod_db`, `S3 bucket uploads`, and `CSV files`. Each data source links to a **Schema Node** describing its structure (`tables, columns, FKs` for the database; `bucket structure` for object storage; `columns, dtypes` for CSV). Content **Entities** (e.g. Entity A with `source: pg_db`, Entity B with `source: csv_1`) hang off their source and are joined by typed relations such as `works_for`.

## Provenance metadata

Every entity, relation, and chunk is tagged with:

- `workspace_id`
- `source_id`
- `source_type`
- `extraction_timestamp`

## Why it matters

| Capability | Enabled by |
| --- | --- |
| ACL filtering at query time | Filter by `source_id` — see [ACL-Aware Query Flow](acl-aware-query-flow.md) |
| Source deletion | Remove all entities contributed by a disconnected source |
| Audit trails | Trace which source contributed which fact |
| Incremental CDC updates | Update only entities originating from changed sources — see [CDC and Real-Time Sync](cdc-real-time-sync.md) |

Provenance is a deliberate departure from vanilla LightRAG, which does not track entity provenance — see [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md). The insertion API is `insert_with_provenance()` in [Modified LightRAG Core Interface](modified-lightrag-core-interface.md).
