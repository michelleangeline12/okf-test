---
type: Concept
title: Federated LightRAG System Layers
description: >-
  The eight-layer architecture of the federated platform and the rationale for
  each layer, from frontend down to the storage layer.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Federated LightRAG System Layers

The platform is organized as a stack of layers, each justified by a specific enterprise need. Together they wrap the [LightRAG Fundamentals](lightrag-fundamentals.md) core.

| Layer | What it does | Why it is needed |
| --- | --- | --- |
| **Frontend** | Dashboard, Schema Explorer, KG Visualizer, Admin Panel | Users need a UI to manage workspaces, register data sources, explore schemas, and query the knowledge graph visually |
| **API Gateway** | REST + WebSocket + GraphQL | REST for CRUD, WebSocket for streaming query responses, GraphQL for flexible schema-exploration queries |
| **Access Control** | AuthN (OIDC/SAML) + RBAC + policy evaluation | Enterprise SSO integration; fine-grained permissions per workspace, per data source, per document type |
| **Workspace Manager** | Workspace CRUD, membership, data source registry | Extends LightRAG's native `workspace` field with user membership and data source tracking |
| **Connector Service** | Abstract interface + concrete connectors | Decouples data source specifics from the ingestion pipeline; each connector knows how to connect, extract schema, and stream content |
| **Schema Extraction** | Extracts and normalizes information schemas | Users must discover what data exists before querying; schema nodes are indexed into the KG for schema-aware retrieval |
| **CDC / Sync Service** | Debezium (PostgreSQL WAL, MySQL binlog), MongoDB Change Streams, file watchers | Keeps the knowledge graph current without manual re-ingestion |
| **LightRAG Core (Modified)** | Same dual-level retrieval plus source tagging and ACL filtering | Proven retrieval quality, with provenance metadata (`source_id`, `workspace_id`) and query-time filtering added |
| **Storage Layer** | PostgreSQL (pgvector + RLS), Neo4j, MongoDB, Redis | PostgreSQL for unified storage plus row-level security; Neo4j for graph-heavy workloads; Redis for caching; MongoDB as an all-in-one option |

## Related flows

- Registering a source and keeping it fresh: [Data Ingestion Flow](data-ingestion-flow.md)
- Answering a question under permissions: [ACL-Aware Query Flow](acl-aware-query-flow.md)
- The interface contracts between layers: [Base Connector Interface](base-connector-interface.md), [Access Control Service Interface](access-control-service-interface.md), [Workspace Manager Interface](workspace-manager-interface.md), [Modified LightRAG Core Interface](modified-lightrag-core-interface.md)
