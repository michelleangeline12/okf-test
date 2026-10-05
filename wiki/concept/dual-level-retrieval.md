---
type: Concept
title: Dual-Level Retrieval
description: >-
  LightRAG's local, global and mixed retrieval levels, the six query modes, and
  why both levels are needed together.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-05T02:12:00.369Z'
---
LightRAG retrieves context at two levels because neither alone is sufficient: simple vector search finds isolated facts, while graph traversal finds relationships. Combining them yields comprehensive answers.

## The two levels

| Level | What it retrieves | How | Best for |
| --- | --- | --- | --- |
| Low-level (local) | Specific entities and their direct relationships | Vector search on entity embeddings + 1–2 hop graph traversal | "Who is the CEO of Tesla?" |
| High-level (global) | Communities, themes, topics | Community detection on the KG, subgraph retrieval | "How does EV adoption affect urban infrastructure?" |
| Mix (default) | Both levels combined | Both + cross-encoder reranking | General-purpose queries |

## Query modes

Six modes are supported: `naive`, `local`, `global`, `hybrid`, `mix`, `bypass`. The default is **mix**. The available modes are exposed to clients via `GET /api/v1/workspaces/{id}/query/modes` — see [REST API Reference](rest-api-reference.md).

## Federation changes

The retrieval algorithm itself is unchanged; Federated LightRAG adds source filtering and an ACL filter between retrieval and generation, so the same dual-level quality is preserved while unauthorized content is removed before the LLM sees it. See [Access-Controlled Query Flow](access-controlled-query-flow.md) and [Modified LightRAG Core](modified-lightrag-core.md).

Related: [LightRAG Foundations](lightrag-foundations.md), [Knowledge Graph Provenance](knowledge-graph-provenance.md).
