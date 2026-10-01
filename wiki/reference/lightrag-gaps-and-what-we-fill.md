---
type: Reference
title: LightRAG Gaps and What We Fill
description: >-
  The five capability gaps in vanilla LightRAG and the federated component that
  addresses each one.
generated:
  by: Pin Test Agent/1
  at: '2026-10-01T06:42:51.512Z'
---
# LightRAG Gaps and What We Fill

LightRAG ships a strong retrieval core but assumes a single-user, file-upload workflow. The architecture plan identifies five gaps and names a specific new component for each.

| Gap | Why it matters | Solution component |
| --- | --- | --- |
| Only accepts file uploads | Enterprise data lives in databases, APIs, and cloud storage | **Data Source Connector Layer** — [Base Connector Interface](base-connector-interface.md) |
| No access control | Enterprises require role-based, source-level permissions | **RBAC with source-level filtering** — [Access Control Model](access-control-model.md) |
| No schema awareness | Users must understand what data exists before querying it | **Information Schema Extraction** — [Schema Extraction Per Connector](schema-extraction-per-connector.md) |
| No real-time sync | Data changes constantly; a stale knowledge graph gives wrong answers | **CDC pipeline (Debezium)** — [CDC and Real-Time Sync](../concept/cdc-and-real-time-sync.md) |
| No multi-user workspace management | Teams need isolated workspaces with shared governance | **Workspace Manager** — [Workspace Manager Interface](workspace-manager-interface.md) |

## Reading the gaps against the base framework

Each gap maps to a limitation of the framework described in [How LightRAG Works Today](../concept/how-lightrag-works-today.md): ingestion is file-only, storage isolation is a namespace rather than a permission model, the graph stores document-derived entities but no schema metadata, and inserts are manual. The resulting before/after view is in [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md), and the components are sequenced for delivery in [Implementation Phases](implementation-phases.md).
