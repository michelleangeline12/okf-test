---
type: Reference
title: Federated Architecture Layers
description: >-
  The seven layers of the Federated LightRAG system — from frontend to storage —
  and the rationale for each.
generated:
  by: Pin Test Agent/1
  at: '2026-10-01T06:42:51.512Z'
---
# Federated Architecture Layers

The system is a seven-layer stack. Each layer exists to solve a problem the layer below it cannot.

| Layer | What it does | Why it is needed |
| --- | --- | --- |
| **Frontend** | Dashboard, Schema Explorer, KG Visualizer, Admin Panel | Users need a UI to manage workspaces, register data sources, explore schemas, and query the KG visually |
| **API Gateway** | REST + WebSocket + GraphQL | REST for CRUD, WebSocket for streaming query responses, GraphQL for flexible schema-exploration queries |
| **Access Control** | AuthN (OIDC/SAML) + RBAC + policy evaluation | Enterprise SSO; fine-grained permissions per workspace, per data source, per document type |
| **Workspace Manager** | Workspace CRUD, membership, data source registry | Extends LightRAG's native `workspace` field with user membership and data source tracking |
| **Connector Service** | Abstract interface + concrete connectors | Decouples source specifics from ingestion; each connector knows how to connect, extract schema, and stream content |
| **Schema Extraction** | Extracts and normalizes information schemas | Users discover what data exists before querying; schema nodes are indexed into the KG for schema-aware retrieval |
| **CDC / Sync Service** | Debezium (PostgreSQL WAL, MySQL binlog), MongoDB Change Streams, file watchers | Keeps the KG current without manual re-ingestion — critical where source data changes constantly |
| **LightRAG Core (Modified)** | Same dual-level retrieval + source tagging + ACL filtering | Proven retrieval quality, plus provenance metadata (`source_id`, `workspace_id`) and query-time filtering |
| **Storage Layer** | PostgreSQL (pgvector + RLS), Neo4j, MongoDB, Redis | PostgreSQL for unified storage + row-level security; Neo4j for graph-heavy workloads; Redis for caching; MongoDB as an all-in-one option |

## Notable internals

- LightRAG Core contains the ingestion pipeline with source tagging, the knowledge-graph engine, dual-level retrieval, an **ACL-aware result filter**, and the LLM response generator.
- The CDC layer contains a Debezium engine, CDC manager, sync scheduler, and change-log store.
- The connector service exposes one `BaseConnector` interface implemented by PostgreSQL, MySQL, MongoDB, S3/Cloud, File Upload, and REST API connectors.

Details: [Data Ingestion Flow](../playbook/data-ingestion-flow.md), [Query Flow with Access Control](../playbook/query-flow-with-access-control.md), [Pluggable Storage Backends](pluggable-storage-backends.md), and the interface pages [Base Connector Interface](base-connector-interface.md), [Access Control Service Interface](access-control-service-interface.md), [Workspace Manager Interface](workspace-manager-interface.md), [Modified LightRAG Core](modified-lightrag-core.md). Overall context: [Federated LightRAG Overview](../concept/federated-lightrag-overview.md).
