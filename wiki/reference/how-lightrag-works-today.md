---
type: Reference
title: How LightRAG Works Today
description: >-
  Baseline LightRAG behaviour — chunking, entity extraction, dual-level
  retrieval, pluggable storage, workspace isolation and incremental updates —
  that the federated platform extends.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-19T13:37:53.588Z'
---
## Summary

LightRAG is a graph-enhanced RAG framework (arXiv:2410.05779, EMNLP 2025). Its current behaviour matters because the federated platform **extends it rather than replacing it** — see [Federated LightRAG Architecture Overview](../concept/federated-lightrag-architecture-overview.md).

## Core pipeline

`Document → Chunking (default 1200 tokens, 100 overlap) → LLM entity & relation extraction → Knowledge Graph (NetworkX / Neo4j / PostgreSQL) → Dual-level retrieval → Context fusion → LLM response`. On the query side, entities are extracted from the user query and fed into the same retrieval stage.

## Knowledge graph construction

Documents are split into chunks; an LLM extracts **entities** (Person, Organization, Location, Event, Concept, …) and **relationships** (typed edges with keywords and descriptions) from each chunk. When two chunks produce entities with the same normalized name, LightRAG merges them: descriptions are concatenated with a `<SEP>` separator, and if the fragment count exceeds `FORCE_LLM_SUMMARY_ON_MERGE` (default **8**), an LLM map-reduce summarization condenses them.

Actual graph node properties:

```json
{
  "entity_name": "Tesla Inc",
  "entity_type": "organization",
  "description": "American electric vehicle manufacturer<SEP>Also produces solar panels",
  "source_id": "chunk_abc123<SEP>chunk_def456",
  "file_path": "report.pdf<SEP>overview.docx",
  "created_at": 1712345678,
  "weight": 1.0
}
```

## Dual-level retrieval

Simple vector search finds isolated facts; graph traversal finds relationships. Both together give comprehensive answers.

| Level | What it retrieves | How | Best for |
| --- | --- | --- | --- |
| Low-level (local) | Specific entities + their direct relationships | Vector search on entity embeddings + 1–2 hop graph traversal | "Who is the CEO of Tesla?" |
| High-level (global) | Communities, themes, topics | Community detection on the KG, subgraph retrieval | "How does EV adoption affect urban infrastructure?" |
| Mix (default) | Both levels combined | Both + cross-encoder reranking | General-purpose queries |

Six query modes are supported: `naive`, `local`, `global`, `hybrid`, `mix`, `bypass` (default: `mix`).

## Pluggable storage

Four storage layers, each pluggable:

| Layer | Purpose | Available backends |
| --- | --- | --- |
| KV Store | LLM response cache, config | JSON, PostgreSQL, Redis, MongoDB, OpenSearch |
| Vector Store | Entity/relation embeddings | NanoVectorDB, PostgreSQL (pgvector), Milvus, FAISS, Qdrant, MongoDB, OpenSearch |
| Graph Store | Knowledge graph nodes/edges | NetworkX (in-memory), Neo4j, PostgreSQL, MongoDB, Memgraph, OpenSearch |
| Doc Status Store | Document processing status | JSON, PostgreSQL, Redis, MongoDB, OpenSearch |

`ChromaVectorDBStorage` exists but is commented out; Apache AGE is also available for PostgreSQL graph storage.

## Workspace isolation and incremental updates

LightRAG natively supports workspace isolation via a `workspace` field (default from the `WORKSPACE` env var); every backend uses it as a namespace/prefix, so workspaces coexist on the same backend without leakage. The federated platform extends this native concept with RBAC and federated sources — see [Workspace Manager Service](workspace-manager-service.md).

Incremental updates are **O(n)** versus an O(N) full rebuild: new entities are matched by normalized name against the existing graph, merged if found, or created as new nodes. This is what makes real-time CDC ingestion feasible — see [CDC and Real-Time Sync Architecture](cdc-and-real-time-sync-architecture.md).
