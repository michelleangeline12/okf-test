---
okf_version: '0.2'
---
# Knowledge Index

## Concept

-   [CDC and Real-Time Sync](wiki/cdc-and-real-time-sync.md) - The change data capture pipeline that watches source databases and files and updates the knowledge graph incrementally.
-   [Federated LightRAG Overview](wiki/federated-lightrag-overview.md) - An enterprise-grade RAG platform built on LightRAG that unifies heterogeneous data sources into per-workspace knowledge graphs with fine-grained access control.
-   [Federated LightRAG System Layers](wiki/federated-lightrag-system-layers.md) - The eight-layer architecture of the federated platform and the rationale for each layer, from frontend down to the storage layer.
-   [Knowledge Graph with Provenance](wiki/knowledge-graph-with-provenance.md) - Enriching the knowledge graph with schema nodes and provenance metadata so every entity, relation, and chunk traces back to its source.

## Playbook

-   [ACL-Aware Query Flow](wiki/acl-aware-query-flow.md) - How a workspace query retrieves context from the knowledge graph, filters out unauthorized sources and document types, and generates a cited answer.
-   [Data Ingestion Flow](wiki/data-ingestion-flow.md) - The end-to-end sequence when a data source is registered to a workspace: permission check, connection test, schema extraction, initial content sync, and CDC listener setup.

## Reference

-   [Access Control Model](wiki/access-control-model.md) - Multi-layer RBAC covering workspace roles, per-source access levels, document-type permissions, and the three points where access is enforced.
-   [Access Control Service Interface](wiki/access-control-service-interface.md) - The AccessControlService contract for workspace roles, per-source access levels, and document-type permissions.
-   [Base Connector Interface](wiki/base-connector-interface.md) - The abstract BaseConnector contract — types, dataclasses, and lifecycle methods that every data source connector must implement.
-   [LightRAG Fundamentals](wiki/lightrag-fundamentals.md) - How vanilla LightRAG works today: core pipeline, knowledge graph construction, dual-level retrieval, pluggable storage, workspace isolation, and incremental updates.
-   [LightRAG Gaps and Solutions](wiki/lightrag-gaps-and-solutions.md) - The five capabilities vanilla LightRAG lacks for enterprise use, and the federated component that fills each gap.
-   [Workspace Manager Interface](wiki/workspace-manager-interface.md) - The WorkspaceManager contract that turns LightRAG's storage namespace into a first-class entity with members, registered sources, and settings.
