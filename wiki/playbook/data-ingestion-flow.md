---
type: Playbook
title: Data Ingestion Flow
description: >-
  Step-by-step sequence when a data source is registered to a workspace:
  permission check, connection test, schema extraction, initial content sync,
  then CDC listening.
generated:
  by: Pin Test Agent/1
  at: '2026-10-01T06:42:51.512Z'
---
# Data Ingestion Flow

**What happens:** when a user registers a data source to a workspace, the system connects to it, extracts its schema, performs an initial content sync, and starts listening for real-time changes.

## Sequence

### Registration and permission check
1. User calls `POST /workspaces/{id}/sources` with `{type: "postgresql", config: {...}}` (see [REST API Reference](rest-api-reference.md)).
2. Access Control checks permission — the user must be **admin on the workspace** — and returns *Allowed*.
3. Workspace Manager registers the data source and returns a `source_id` ([Workspace Manager Interface](workspace-manager-interface.md)).

### Initial sync
4. Connector Service `connect(source_id, config)` → *Connected*; the connection is verified with `test_connection`.
5. Schema Extractor `extract_schema(source_id)` reads `INFORMATION_SCHEMA` / introspection, parses and normalizes the raw schema, stores schema metadata, and returns an `InformationSchema` object ([Schema Extraction Per Connector](schema-extraction-per-connector.md)).
6. CDC Manager `register_cdc_listener(source_id)` sets up Debezium / Change Streams ([CDC and Real-Time Sync](../concept/cdc-and-real-time-sync.md)).
7. Connector Service `extract_content(source_id)` streams `ExtractedContent`.
8. LightRAG Core `insert(workspace_id, content, source_id)` chunks the content, extracts entities, builds the KG, and stores entities, relations, and vectors — each tagged with provenance ([Knowledge Graph Provenance](knowledge-graph-provenance.md)).

### Real-time CDC
9. Change events (insert / update / delete) flow to `incremental_update(workspace_id, changes)`, which updates the KG incrementally rather than rebuilding it.

## Why this order

- The **initial sync establishes the baseline KG**; CDC keeps it current without full re-ingestion.
- **Schema extraction happens first** so schema nodes exist in the graph *before* content entities reference them.
- Provenance tagging at insert time is what later makes query-time ACL filtering, source deletion, and audit trails possible ([Access Control Model](access-control-model.md)).

Related: [Federated Architecture Layers](../reference/federated-architecture-layers.md) and the connector contract in [Base Connector Interface](base-connector-interface.md).
