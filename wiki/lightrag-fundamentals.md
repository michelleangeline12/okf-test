---
type: Reference
title: LightRAG Fundamentals
description: >-
  How vanilla LightRAG works today: core pipeline, knowledge graph construction,
  dual-level retrieval, pluggable storage, workspace isolation, and incremental
  updates.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# LightRAG Fundamentals

LightRAG is a graph-enhanced RAG framework (arXiv:2410.05779, EMNLP 2025). Understanding it is essential because [Federated LightRAG Overview](federated-lightrag-overview.md) extends it rather than replacing it.

## Core pipeline

`Document → Chunking (default 1200 tokens, 100 overlap) → LLM Entity & Relation Extraction → Knowledge Graph (NetworkX / Neo4j / PostgreSQL) → Dual-Level Retrieval → Context Fusion → LLM Response`, with entity extraction performed on the user query.

## Knowledge graph construction

Documents are split into chunks; an LLM extracts entities (Person, Organization, Location, Event, Concept, etc.) and relationships (typed edges with keywords and descriptions) from each chunk.

**Entity merging:** when two chunks produce entities with the same normalized name, LightRAG merges them — descriptions are concatenated with a `<SEP>` separator, and if the fragment count exceeds `FORCE_LLM_SUMMARY_ON_MERGE` (default: 8), an LLM map-reduce summarization condenses them.

Entity node properties look like:

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
| Low-Level (local) | Specific entities + their direct relationships | Vector search on entity embeddings + 1–2 hop graph traversal | "Who is the CEO of Tesla?" |
| High-Level (global) | Communities, themes, topics | Community detection on the KG, subgraph retrieval | "How does EV adoption affect urban infrastructure?" |
| Mix (default) | Both levels combined | Both + cross-encoder reranking | General-purpose queries |

Six query modes are supported: `naive`, `local`, `global`, `hybrid`, `mix`, `bypass` (default: `mix`).

## Pluggable storage

LightRAG has four pluggable storage layers:

| Storage layer | Purpose | Available backends |
| --- | --- | --- |
| KV Store | LLM response cache, config | JSON, PostgreSQL, Redis, MongoDB, OpenSearch |
| Vector Store | Entity/relation embeddings | NanoVectorDB, PostgreSQL (pgvector), Milvus, FAISS, Qdrant, MongoDB, OpenSearch |
| Graph Store | Knowledge graph nodes/edges | NetworkX (in-memory), Neo4j, PostgreSQL, MongoDB, Memgraph, OpenSearch |
| Doc Status Store | Document processing status | JSON, PostgreSQL, Redis, MongoDB, OpenSearch |

`ChromaVectorDBStorage` exists but is commented out; Apache AGE is also available for PostgreSQL graph storage.

## Workspace isolation

LightRAG natively supports workspace-based isolation via a `workspace` field (defaulting to the `WORKSPACE` environment variable). All storage backends use the workspace value as a namespace/prefix, so different workspaces coexist on the same backend without data leakage. The federation layer extends this native concept with RBAC and federated data sources — see [Access Control Model](access-control-model.md).

## Incremental updates

LightRAG supports O(n) incremental updates versus an O(N) full rebuild: new entities are matched by normalized name against the existing graph, merged if found, or created as new nodes. This is what makes real-time CDC ingestion feasible — see [CDC and Real-Time Sync](cdc-real-time-sync.md).
