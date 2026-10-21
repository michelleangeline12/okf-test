---
type: Reference
title: Access Control Model
description: >-
  Multi-layer RBAC — workspace roles plus per-source and per-document-type
  access levels — with enforcement at query time, indexing time and the storage
  layer.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-21T06:10:36.347Z'
---
## Summary

Multi-layer RBAC governs who can access which workspaces, data sources and document types. It answers LightRAG's "no access control" gap — see [What LightRAG Lacks: Gaps and Solutions](what-lightrag-lacks-gaps-and-solutions.md) — and is enforced at three independent points.

## Model

`Organization` → `User` —*has_role_in*→ `Workspace` (the LightRAG workspace) —*has_access_to*→ `Data Source A / B`; workspaces and sources are `governed_by` **Access Policies** (per-source, per-document-type).

- **Workspace roles:** `owner`, `admin`, `editor`, `viewer`
- **Source access levels:** `full`, `read`, `schema_only`, `denied`
- **Document-type access:** a boolean map, e.g. `database_rows`, `pdf_chunks`

### Access levels

| Level | Can see schema | Can query data | Can modify |
| --- | --- | --- | --- |
| `full` | Yes | Yes | Yes |
| `read` | Yes | Yes | No |
| `schema_only` | Yes (metadata only) | No | No |
| `denied` | No | No | No |

**Why source-level access:** different teams own different databases. The HR team should query HR data but not Finance data, even within the same workspace — achievable without duplicating the KG.

**Why `schema_only` exists:** sometimes a user needs to know that a table called `employees` exists with columns `name`, `salary`, `department`, but must not see actual employee records. This enables data-catalog use cases without exposing sensitive data.

## Service interface

```python
class AccessControlService:
    async def get_accessible_sources(user_id, workspace_id) -> List[str]: ...
    async def get_accessible_doc_types(user_id, workspace_id) -> List[str]: ...
    async def check_source_access(user_id, source_id, required_level) -> bool: ...
    async def get_user_permissions(user_id, workspace_id) -> UserPermission: ...
    async def grant_source_access(admin_user_id, target_user_id, source_id, level) -> bool: ...
    async def revoke_source_access(admin_user_id, target_user_id, source_id) -> bool: ...
```

`UserPermission` carries `user_id`, `workspace_id`, `role`, `source_access` (source_id → level) and `doc_type_access` (doc_type → bool).

## Query flow with access control

`POST /workspaces/{id}/query {query, mode: "mix"}` → RBAC engine returns `get_accessible_sources()` (e.g. `[source_1, source_3]`) and `get_accessible_doc_types()` (e.g. `["database_rows", "pdf_chunks"]`) → LightRAG runs dual-level retrieval (entity extraction from the query, entity vector search + 1–2 hop traversal, plus community detection + subgraph retrieval) → the **ACL filter** removes entities from blocked sources and blocked doc types → the LLM generator produces a response with **citations only from accessible sources**.

**Why filtering happens post-retrieval:** filtering at query time rather than at storage time preserves the KG's structural integrity — the graph is complete, but users only see slices they are authorized for. This avoids graph fragmentation and still allows admin-level queries across all sources.

## Enforcement points

| Point | What happens | Why |
| --- | --- | --- |
| **Query time** | Retrieved KG entities are filtered by accessible sources and doc types before LLM generation | Most critical
