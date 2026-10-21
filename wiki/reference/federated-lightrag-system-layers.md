---
type: Reference
title: Federated LightRAG System Layers
description: >-
  The nine layers of the federated platform — frontend, API gateway, access
  control, workspace manager, connectors, schema extraction, CDC, modified
  LightRAG core and storage — with the reason each exists.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-21T06:10:36.347Z'
---
## Summary

The federated platform is a stack of nine layers wrapped around an unmodified retrieval engine. Each layer answers one of the enterprise gaps catalogued in [What LightRAG Lacks: Gaps and Solutions](what-lightrag-lacks-gaps-and-solutions.md); the before/after picture is in LightRAG vs Federated LightRAG.

## Layer map

| Layer | What it does | Why it is needed |
| --- | --- | --- |
| Frontend | Dashboard, Schema Explorer, KG Visualizer, Admin Panel | Users need a UI to manage workspaces, register data sources, explore schemas and query the KG visually |
| API Gateway | REST + WebSocket + GraphQL | REST for CRUD, WebSocket for streaming query responses, GraphQL for flexible schema-exploration queries — see Federated LightRAG REST API |
| Access Control | AuthN (OIDC/SAML) + RBAC + policy evaluation | Enterprise SSO; fine-grained permissions per workspace, per data source, per document type — see [Access Control Model](access-control-model.md) |
| Workspace Manager | Workspace CRUD, membership, data source registry | Extends LightRAG's native `workspace` field with user membership and source tracking — see Workspace Manager Service |
| Connector Service | Abstract interface + concrete connectors | Decouples source specifics from ingestion; each connector knows how to connect, extract schema and stream content — see [Base Connector Interface](base-connector-interface.md) |
| Schema Extraction | Extracts and normalizes information schemas | Users must discover what data exists before querying; schema nodes are indexed into the KG for schema-aware retrieval — see [Schema Extraction Per Connector](schema-extraction-per-connector.md) |
| CDC / Sync Service | Debezium (PostgreSQL WAL, MySQL binlog), MongoDB Change Streams, file watchers | Keeps the graph current without manual re-ingestion — see [CDC and Real-Time Sync Architecture](cdc-and-real-time-sync-architecture.md) |
| LightRAG Core (Modified) | Same dual-level retrieval + source tagging + ACL filtering | Proven retrieval quality; adds provenance metadata (`source_id`, `workspace_id`) and filters results at query time — see [Modified LightRAG Core](modified-lightrag-core.md) |
| Storage Layer | PostgreSQL (pgvector + RLS), Neo4j, MongoDB, Redis | PostgreSQL for unified storage plus row-level security; Neo4j for graph-heavy workloads; Redis for caching; MongoDB as an all-in-one option |

## Ingestion flow across the layers

When a user registers a data source to a workspace (`POST /workspaces/{id}/sources`), the API gateway asks access control to check that the caller is an admin on the workspace; the Workspace Manager registers the source and returns a `source_id`; the Connector Service tests the connection; the Schema Extractor reads `INFORMATION_SCHEMA`/introspection, parses and normalizes it, and stores the schema metadata; a CDC listener is registered (Debezium or Change Streams); then `extract_content()` streams `ExtractedContent` into `insert(workspace_id, content, source_id)`, which chunks, extracts entities and builds the KG. From then on, change events flow through `incremental_update(workspace_id, changes)`.

**Why this order:** the initial sync establishes a baseline KG, CDC keeps it current without full re-ingestion, and schema extraction happens first so schema nodes exist in the graph before content entities reference them.

## Delivery

The layers are built in the order described in Implementation Phases.
