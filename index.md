---
okf_version: '0.2'
---
# Knowledge Index

## Concept

[Browse all Concept pages →](wiki/concept/index.md)

-   [Federated LightRAG Architecture Overview](wiki/concept/federated-lightrag-architecture-overview.md) - An enterprise-grade RAG platform built on LightRAG that unifies heterogeneous data sources into one governed knowledge graph per workspace, with fine-grained role-based access control.
-   [Federated LightRAG Overview](wiki/concept/federated-lightrag-overview.md) - An enterprise-grade RAG platform built on LightRAG that unifies heterogeneous data sources into one governed knowledge graph per workspace.
-   [Knowledge Graph with Provenance](wiki/concept/knowledge-graph-with-provenance.md) - The workspace graph — data sources, schema nodes and entities — where every entity, relation and chunk carries workspace_id, source_id, source_type and extraction_timestamp.

## Guide

[Browse all Guide pages →](wiki/guide/index.md)

-   [Knowledge Source Guide: OKF Test](wiki/guide/knowledge-source-guide-okf-test.md) - A map of the OKF Test knowledge source — what raw material it contains and which wiki pages were authored from each source file.
-   [OKF Test Knowledge Source](wiki/guide/okf-test-knowledge-source.md) - Map of the TEST-DEMO knowledge source: which raw documents it contains, what each one covers, and which wiki pages were derived from them.

## Reference

[Browse all Reference pages →](wiki/reference/index.md)

-   [Access Control Model](wiki/reference/access-control-model.md) - Multi-layer RBAC — workspace roles plus per-source and per-document-type access levels — with enforcement at query time, indexing time and the storage layer.
-   [Base Connector Interface](wiki/reference/base-connector-interface.md) - The abstract BaseConnector contract — connect, test, extract schema, stream content, list resources, detect and listen for changes — that every data source connector implements.
-   [CDC and Real-Time Sync Architecture](wiki/reference/cdc-and-real-time-sync-architecture.md) - The change-data-capture pipeline — Debezium, MongoDB Change Streams and file watchers feeding a Redis Streams event bus — that keeps the knowledge graph current without re-ingestion.
-   [Federated LightRAG System Layers](wiki/reference/federated-lightrag-system-layers.md) - The nine layers of the federated platform — frontend, API gateway, access control, workspace manager, connectors, schema extraction, CDC, modified LightRAG core and storage — with the reason each exists.
-   [How LightRAG Works Today](wiki/reference/how-lightrag-works-today.md) - Baseline LightRAG behaviour — chunking, entity extraction, dual-level retrieval, pluggable storage, workspace isolation and incremental updates — that the federated platform extends.
-   [Modified LightRAG Core](wiki/reference/modified-lightrag-core.md) - LightRAG's engine with three additions — source provenance tagging, ACL-aware retrieval and CDC-driven incremental updates — exposed as the FederatedLightRAG class.
-   [Schema Extraction Per Connector](wiki/reference/schema-extraction-per-connector.md) - How each connector pulls an information schema — SQL information_schema joins, MongoDB sampling, OpenAPI and GraphQL introspection, pandas profiling for files — and stores it as KG metadata.
-   [What LightRAG Lacks: Gaps and Solutions](wiki/reference/what-lightrag-lacks-gaps-and-solutions.md) - The five enterprise gaps in vanilla LightRAG — file-only ingestion, no access control, no schema awareness, no real-time sync, no workspace management — and the layer that answers each.
