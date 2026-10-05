---
type: Concept
title: Dual-Level Retrieval
description: >-
  LightRAG's low-level (entity-centric) and high-level (community-centric)
  retrieval strategies, and the six query modes that combine them.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-05T02:30:09.090Z'
---
LightRAG retrieves context at two levels. The rationale: simple vector search finds isolated facts, graph traversal finds relationships, and both together give comprehensive answers.

| Level | What it retrieves | How | Best for |
| --- | --- | --- | --- |
| Low-Level (local) | Specific entities + their direct relationships | Vector search on entity embeddings + 1–2 hop graph traversal | “Who is the CEO of Tesla?” |
| High-Level (global) | Communities, themes, topics | Community detection on the KG, subgraph retrieval | “How does EV adoption affect urban infrastructure?” |
| Mix (default) | Both levels combined | Both + cross-encoder reranking | General-purpose queries |

Six query modes are supported: `naive`, `local`, `global`, `hybrid`, `mix`, `bypass` — default `mix`. The mode is passed per query (e.g. `POST /workspaces/{id}/query {query, mode: "mix"}`) and listable via the API — see [REST API Surface](rest-api-surface.md).

## In the federated system

Retrieval itself is unchanged; the federation layer wraps it. A query first resolves the caller's accessible sources and document types, runs dual-level retrieval, then drops any retrieved entity from a blocked source or doc type before the LLM ever sees the context. This is described in [Access-Controlled Query Flow](access-controlled-query-flow.md), and the source/doc-type tags it filters on come from [Knowledge Graph Provenance](knowledge-graph-provenance.md).

Related: [LightRAG Foundations](lightrag-foundations.md), [Modified LightRAG Core](modified-lightrag-core.md).
