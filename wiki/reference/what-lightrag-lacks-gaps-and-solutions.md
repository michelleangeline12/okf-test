---
type: Reference
title: 'What LightRAG Lacks: Gaps and Solutions'
description: >-
  The five enterprise gaps in vanilla LightRAG — file-only ingestion, no access
  control, no schema awareness, no real-time sync, no workspace management — and
  the layer that answers each.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-19T13:37:53.588Z'
---
## Summary

The federated platform exists because vanilla LightRAG, for all its retrieval quality, is a single-user document-indexing framework. Five gaps drive the design; each is answered by a new layer described in [Federated LightRAG System Layers](federated-lightrag-system-layers.md).

## Gap table

| Gap | Why it matters | Solution |
| --- | --- | --- |
| Only accepts file uploads | Enterprise data lives in databases, APIs and cloud storage | **Data Source Connector Layer** — [Base Connector Interface](base-connector-interface.md) |
| No access control | Enterprises require role-based, source-level permissions | **RBAC with source-level filtering** — [Access Control Model](access-control-model.md) |
| No schema awareness | Users must understand what data exists before querying | **Information Schema Extraction** — [Schema Extraction Per Connector](schema-extraction-per-connector.md) |
| No real-time sync | Data changes constantly; a stale knowledge graph gives wrong answers | **CDC pipeline (Debezium)** — [CDC and Real-Time Sync Architecture](cdc-and-real-time-sync-architecture.md) |
| No multi-user workspace management | Teams need isolated workspaces with shared governance | **Workspace Manager** — [Workspace Manager Service](workspace-manager-service.md) |

## Why the gaps compound

Each gap assumes the others away. Connectors without provenance cannot be filtered; access control without provenance has nothing to filter on; CDC without incremental updates would require full rebuilds. The connective tissue is provenance metadata on every entity, relation and chunk — see [Knowledge Graph with Provenance](knowledge-graph-with-provenance.md) — which simultaneously enables query-time ACL filtering, source deletion, audit trails and incremental CDC updates.

The net effect of closing all five is summarized in [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md).
