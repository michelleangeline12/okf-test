---
okf_version: '0.2'
---
# Knowledge Index

## Concept

[Browse all Concept pages →](wiki/concept/index.md)

-   [CDC and Real-Time Sync](wiki/concept/cdc-and-real-time-sync.md) - The change-data-capture pipeline that watches source databases and files and applies incremental knowledge-graph updates.
-   [Federated LightRAG Overview](wiki/concept/federated-lightrag-overview.md) - An enterprise-grade RAG platform built on LightRAG that federates heterogeneous data sources into one governed knowledge graph per workspace.
-   [How LightRAG Works Today](wiki/concept/how-lightrag-works-today.md) - The base LightRAG framework — core pipeline, knowledge-graph construction, dual-level retrieval, pluggable storage, workspaces, and incremental updates.

## Guide

[Browse all Guide pages →](wiki/guide/index.md)

-   [Knowledge Source Index](wiki/guide/knowledge-source-index.md) - Map of the TEST OKF 1 knowledge base: which source documents exist and which wiki pages were authored from them.

## Playbook

[Browse all Playbook pages →](wiki/playbook/index.md)

-   [Data Ingestion Flow](wiki/playbook/data-ingestion-flow.md) - Step-by-step sequence when a data source is registered to a workspace: permission check, connection test, schema extraction, initial content sync, then CDC listening.
-   [Query Flow with Access Control](wiki/playbook/query-flow-with-access-control.md) - How a workspace query is resolved: dual-level retrieval, post-retrieval ACL filtering by source and document type, then generation with citations from accessible sources only.

## Reference

[Browse all Reference pages →](wiki/reference/index.md)

-   [Federated Architecture Layers](wiki/reference/federated-architecture-layers.md) - The seven layers of the Federated LightRAG system — from frontend to storage — and the rationale for each.
-   [LightRAG Gaps and What We Fill](wiki/reference/lightrag-gaps-and-what-we-fill.md) - The five capability gaps in vanilla LightRAG and the federated component that addresses each one.
