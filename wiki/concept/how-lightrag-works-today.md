---
type: Concept
title: How LightRAG Works Today
description: >-
  The base LightRAG framework — core pipeline, knowledge-graph construction,
  dual-level retrieval, pluggable storage, workspaces, and incremental updates.
generated:
  by: Pin Test Agent/1
  at: '2026-10-01T06:42:51.512Z'
---
# How LightRAG Works Today

LightRAG is a graph-enhanced RAG framework (arXiv:2410.05779, EMNLP 2025). Understanding it matters because Federated LightRAG extends it rather than replacing it.

## Core pipeline

`Document → Chunking (default 1200 tokens, 100 overlap) → LLM entity & relation extraction → Knowledge Graph (NetworkX / Neo4j / PostgreSQL) → Dual-level retrieval → Context fusion → LLM response`, with entity extraction performed on the user query at read time.

## Knowledge graph construction

Documents are split into chunks; an LLM extracts entities (Person, Organization, Location, Event, Concept, …) and typed relationships with keywords and descriptions from each chunk.

**Entity merging:** when two chunks produce entities with the same normalized name, LightRAG merges them — descriptions are concatenated with a `<SEP>` separator, and if the fragment count exceeds `FORCE_LLM_SUMMARY_ON_MERGE` (default **8**), an LLM map-reduce summarization condenses them.

Entity node properties:

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
| Low-level (local) | Specific entities + direct relationships | Vector search on entity embeddings + 1–2 hop graph traversal | "Who is the CEO of Tesla?" |
| High-level (global) | Communities, themes, topics | Community detection on the KG, subgraph retrieval | "How does EV adoption affect urban infrastructure?" |
| Mix (default) | Both levels combined | Both + cross-encoder reranking | General-purpose queries |

Six query modes are supported: `naive`, `local`, `global`, `hybrid`, `mix`, `bypass` (default: `mix`).

## Pluggable storage and workspaces

LightRAG has four independently pluggable storage layers — see [Pluggable Storage Backends](pluggable-storage-backends.md). Workspace isolation is native: a `workspace` field (defaulting to the `WORKSPACE` env var) is used by every backend as a namespace/prefix, so workspaces coexist on shared storage without leakage. Federated LightRAG extends this native concept with RBAC and federated sources ([Workspace Manager Interface](workspace-manager-interface.md)).

## Incremental updates

LightRAG supports **O(n) incremental updates** instead of O(N) full rebuilds: new entities are matched by normalized name against the existing graph, merged if found, or created as new nodes. This is what makes real-time CDC ingestion feasible ([CDC and Real-Time Sync](cdc-and-real-time-sync.md)).

What the framework does *not* provide is covered in [LightRAG Gaps and What We Fill](../reference/lightrag-gaps-and-what-we-fill.md).
