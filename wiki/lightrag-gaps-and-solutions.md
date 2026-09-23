---
type: Reference
title: LightRAG Gaps and Solutions
description: >-
  The five capabilities vanilla LightRAG lacks for enterprise use, and the
  federated component that fills each gap.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# LightRAG Gaps and Solutions

Vanilla [LightRAG Fundamentals](lightrag-fundamentals.md) is a strong retrieval core but not an enterprise platform. The federated design adds one component per gap.

| Gap | Why it matters | Solution |
| --- | --- | --- |
| Only accepts file uploads | Enterprise data lives in databases, APIs, and cloud storage | [Base Connector Interface](base-connector-interface.md) — the Data Source Connector Layer |
| No access control | Enterprise requires role-based, source-level permissions | RBAC with source-level filtering — see [Access Control Model](access-control-model.md) |
| No schema awareness | Users need to understand what data exists before querying | [Schema Extraction Per Connector](schema-extraction-per-connector.md) — Information Schema Extraction |
| No real-time sync | Data changes constantly; a stale knowledge graph gives wrong answers | [CDC and Real-Time Sync](cdc-real-time-sync.md) (Debezium) |
| No multi-user workspace management | Teams need isolated workspaces with shared governance | Workspace Manager — see [Workspace Manager Interface](workspace-manager-interface.md) |

Each gap maps to a layer in the [Federated LightRAG System Layers](federated-lightrag-system-layers.md), and the aggregate effect is summarized in [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md).
