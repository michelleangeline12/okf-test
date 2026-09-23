---
type: Playbook
title: Data Ingestion Flow
description: >-
  The end-to-end sequence when a data source is registered to a workspace:
  permission check, connection test, schema extraction, initial content sync,
  and CDC listener setup.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Data Ingestion Flow

**What happens:** when a user registers a data source to a workspace, the system connects to it, extracts its schema, performs an initial content sync, and starts listening for real-time changes.

## Sequence

1. **User** calls `POST /workspaces/{id}/sources` with `{type: "postgresql", config: {...}}`.
2. **API Gateway → Access Control**: check permission (admin on the workspace) → *Allowed*.
3. **Workspace Manager**: register the data source and return a `source_id`.
4. **Connector Service**: `connect(source_id, config)` and test the connection → *Connected*.
5. **Schema Extractor**: `extract_schema(source_id)` via `INFORMATION_SCHEMA` / introspection → raw schema → parse and normalize → store schema metadata → return an `InformationSchema` object.
6. **CDC Manager**: `register_cdc_listener(source_id)` — set up Debezium / Change Streams.
7. **Connector Service**: `extract_content(source_id)` → a stream of `ExtractedContent`.
8. **LightRAG Core**: `insert(workspace_id, content, source_id)` → chunk, extract entities, build the knowledge graph → store entities, relations, and vectors.
9. **Real-time CDC (ongoing)**: change events (insert/update/delete) flow to `incremental_update(workspace_id, changes)`, which updates the knowledge graph incrementally.

## Why this order

- The **initial sync** establishes a baseline knowledge graph; **CDC** keeps it current without requiring full re-ingestion.
- **Schema extraction happens first** so that schema nodes exist in the graph before content entities reference them — see [Schema Extraction Per Connector](schema-extraction-per-connector.md) and [Knowledge Graph with Provenance](knowledge-graph-with-provenance.md).
- The permission check precedes any connection attempt, per [Access Control Model](access-control-model.md).

The connector methods invoked here are defined in [Base Connector Interface](base-connector-interface.md); the sync mechanics are in [CDC and Real-Time Sync](cdc-real-time-sync.md). Endpoints are listed in [Federated LightRAG REST API](federated-lightrag-rest-api.md).
