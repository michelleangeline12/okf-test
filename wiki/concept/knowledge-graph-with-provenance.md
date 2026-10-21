---
type: Concept
title: Knowledge Graph with Provenance
description: >-
  The workspace graph — data sources, schema nodes and entities — where every
  entity, relation and chunk carries workspace_id, source_id, source_type and
  extraction_timestamp.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-21T06:10:36.347Z'
---
## Summary

The knowledge graph is enriched with **schema nodes** and **provenance metadata** so that every entity traces back to the source it came from. Provenance is the single mechanism that makes access control, source deletion, audit trails and incremental updates possible at the same time; without it the other layers of [Federated LightRAG System Layers](../reference/federated-lightrag-system-layers.md) have nothing to operate on.

## Graph shape

One graph per LightRAG **workspace**. Inside it:

- a **Workspace** node (the LightRAG workspace) at the root;
- **Data Source** nodes hanging off it — e.g. `PostgreSQL prod_db`, `S3 bucket uploads`, `CSV files`;
- a **Schema Node** per source, holding that source's structure — `tables, columns, FKs` for a relational source, `bucket structure` for object storage, `columns, dtypes` for a spreadsheet (produced by [Schema Extraction Per Connector](../reference/schema-extraction-per-connector.md));
- **Entity** nodes (`Entity A` with `source: pg_db`, `Entity B` with `source: csv_1`) and typed **Relation** edges between them (e.g. `works_for`).

## Provenance tags

Every entity, relation and chunk is tagged with:

| Tag | Meaning |
| --- | --- |
| `workspace_id` | Which workspace's graph the fact belongs to |
| `source_id` | Which registered data source contributed it |
| `source_type` | Which connector type that source is (postgresql, csv, pdf, rest_api, …) |
| `extraction_timestamp` | When it was extracted |

This extends LightRAG's existing node properties (`entity_name`, `entity_type`, `description`, `source_id`, `file_path`, `created_at`, `weight`) rather than replacing them.

## What provenance enables

1. **ACL filtering at query time** — results are filtered by `source_id` (and document type) after retrieval, so the graph need not be fragmented per user. See [Access Control Model](../reference/access-control-model.md).
2. **Source deletion** — disconnecting a source removes exactly the entities, relations and chunks it contributed (`delete_source_data`).
3. **Audit trails** — trace which source contributed which fact.
4. **Incremental CDC updates** — only entities from changed sources are re-processed, instead of rebuilding the graph. See [CDC and Real-Time Sync Architecture](../reference/cdc-and-real-time-sync-architecture.md).

## Indexing-time enforcement

Tagging happens at **indexing time**, inside `insert_with_provenance()` on the [Modified LightRAG Core](../reference/modified-lightrag-core.md). That is deliberate: provenance written at write time is trustworthy, whereas provenance reconstructed at read time is not. In the comparison table of LightRAG vs Federated LightRAG, entity provenance moves from "not tracked" to `source_id`, `workspace_id` per entity.
