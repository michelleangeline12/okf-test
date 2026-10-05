---
type: Reference
title: Pluggable Storage Backends
description: >-
  LightRAG's four pluggable storage layers and their backends, plus the storage
  stack chosen for the federated deployment.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-05T02:12:00.369Z'
---
LightRAG separates persistence into four independently pluggable layers. The federated platform selects from these per workload rather than mandating one backend.

## The four layers

| Storage layer | Purpose | Available backends |
| --- | --- | --- |
| KV Store | LLM response cache, config | JSON, PostgreSQL, Redis, MongoDB, OpenSearch |
| Vector Store | Entity/relation embeddings | NanoVectorDB, PostgreSQL (pgvector), Milvus, FAISS, Qdrant, MongoDB, OpenSearch |
| Graph Store | Knowledge-graph nodes/edges | NetworkX (in-memory), Neo4j, PostgreSQL, MongoDB, Memgraph, OpenSearch |
| Doc Status Store | Document processing status | JSON, PostgreSQL, Redis, MongoDB, OpenSearch |

Notes: a Chroma `VectorDBStorage` exists but is commented out; Apache AGE is also available for PostgreSQL graph storage.

## Chosen federated stack

- **PostgreSQL** — pgvector plus row-level security; unified storage with defense-in-depth ACL
- **Neo4j** — graph-heavy workloads
- **MongoDB** — document status store / all-in-one option
- **Redis** — cache, KV, and the CDC change event bus

All backends are namespaced by the LightRAG `workspace` value, so multiple workspaces share infrastructure without data leakage. Storage-layer ACL enforcement (PostgreSQL RLS, Neo4j label-based ACL, MongoDB field-level redaction) is one of the three enforcement points in [RBAC and Source-Level Access Control](rbac-and-source-level-access-control.md).

Related: [Federated System Architecture](federated-system-architecture.md), [LightRAG Foundations](../concept/lightrag-foundations.md).
