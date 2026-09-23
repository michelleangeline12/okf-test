---
type: Playbook
title: ACL-Aware Query Flow
description: >-
  How a workspace query retrieves context from the knowledge graph, filters out
  unauthorized sources and document types, and generates a cited answer.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# ACL-Aware Query Flow

**What happens:** when a user queries a workspace, the system retrieves relevant context from the knowledge graph, filters out anything the user cannot access, and generates a response.

## Sequence

1. **User** calls `POST /workspaces/{id}/query` with `{query: "...", mode: "mix"}`.
2. **RBAC Engine** resolves the caller's scope: `get_accessible_sources(user_id, workspace_id)` → e.g. `[source_1, source_3]`, and `get_accessible_doc_types(user_id, workspace_id)` → e.g. `["database_rows", "pdf_chunks"]`.
3. **LightRAG Core** runs `query(query, mode="mix", source_filter, doc_type_filter)` using [dual-level retrieval](lightrag-fundamentals.md):
   - *Low-level*: extract entities from the query, then entity vector search plus 1–2 hop graph traversal.
   - *High-level*: community detection plus subgraph retrieval.
4. **ACL Filter** removes entities from blocked sources and from blocked document types, producing the filtered context.
5. **LLM Generator** produces the response from the filtered context.
6. **User** receives the answer plus citations drawn only from accessible sources.

## Why ACL filtering happens post-retrieval

Filtering at query time rather than at storage time preserves the knowledge graph's structural integrity: the graph is complete, but users only see slices they are authorized for. This avoids graph fragmentation and still enables admin-level queries across all sources.

Query-time filtering is one of three enforcement points described in [Access Control Model](access-control-model.md); the provenance tags it relies on are described in [Knowledge Graph with Provenance](knowledge-graph-with-provenance.md). The implementation contract is in [Modified LightRAG Core Interface](modified-lightrag-core-interface.md).
