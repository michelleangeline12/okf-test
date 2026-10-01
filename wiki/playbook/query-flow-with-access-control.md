---
type: Playbook
title: Query Flow with Access Control
description: >-
  How a workspace query is resolved: dual-level retrieval, post-retrieval ACL
  filtering by source and document type, then generation with citations from
  accessible sources only.
generated:
  by: Pin Test Agent/1
  at: '2026-10-01T06:42:51.512Z'
---
# Query Flow with Access Control

**What happens:** when a user queries a workspace, the system retrieves relevant context from the knowledge graph, removes anything the user cannot access, and only then generates a response.

## Sequence

1. User calls `POST /workspaces/{id}/query` with `{query: "...", mode: "mix"}`.
2. API Gateway asks the RBAC engine for `get_accessible_sources(user_id, workspace_id)` → e.g. `[source_1, source_3]`, and `get_accessible_doc_types(user_id, workspace_id)` → e.g. `["database_rows", "pdf_chunks"]` ([Access Control Service Interface](access-control-service-interface.md)).
3. LightRAG Core runs `query(query, mode="mix", source_filter, doc_type_filter)`:
   - **Low-level retrieval** — extract entities from the query, entity vector search + 1–2 hop graph traversal.
   - **High-level retrieval** — community detection + subgraph retrieval.
4. Raw results (spanning *all* sources) pass through the ACL filter, which removes entities from blocked sources and from blocked document types, yielding filtered context.
5. The LLM generator produces the answer from the filtered context, with **citations only from accessible sources**.

## Why ACL filtering happens post-retrieval

Filtering at query time rather than at storage time preserves the graph's structural integrity: the KG is complete, but users only see slices they are authorized for. This avoids graph fragmentation and still allows admin-level queries across all sources.

## Enforcement is layered, not single-point

Query-time filtering is the most critical point but not the only one — indexing-time tagging and storage-layer controls (PostgreSQL RLS, Neo4j label-based ACL, MongoDB field-level redaction) provide defense in depth. See [Access Control Model](access-control-model.md) → *Enforcement points*.

Retrieval levels and the six query modes are described in [How LightRAG Works Today](../concept/how-lightrag-works-today.md); the modified core method is `query_with_acl` in [Modified LightRAG Core](modified-lightrag-core.md).
