---
type: Reference
title: Access Control Model
description: >-
  Multi-layer RBAC covering workspace roles, per-source access levels,
  document-type permissions, and the three points where access is enforced.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Access Control Model

**What it is:** multi-layer RBAC governing who can access which workspaces, data sources, and document types. Structurally: an **Organization** contains **Users**, each user `has_role_in` a **Workspace** (a LightRAG workspace), the workspace `governed_by` **Access Policies** (per-source, per-document-type), and users `has_access_to` individual **Data Sources**.

## Workspace roles

`owner` · `admin` · `editor` · `viewer`

## Source access levels

| Level | Can see schema | Can query data | Can modify |
| --- | --- | --- | --- |
| `full` | Yes | Yes | Yes |
| `read` | Yes | Yes | No |
| `schema_only` | Yes (metadata only) | No | No |
| `denied` | No | No | No |

## Design rationale

- **Why source-level access:** in an enterprise, different teams own different databases. The HR team should query HR data but not Finance data, even within the same workspace. Source-level access enables this without duplicating the knowledge graph.
- **Why `schema_only` exists:** sometimes a user needs to know that a table called `employees` exists with columns `name`, `salary`, `department` — but should not see actual employee records. This enables data-catalog use cases without exposing sensitive data.

## Enforcement points

| Point | What happens | Why |
| --- | --- | --- |
| **Query time** | Retrieved KG entities are filtered by accessible sources and doc types before LLM generation | Most critical; prevents any data leakage in responses |
| **Indexing time** | Every entity/relation is tagged with `source_id` and `workspace_id` | Enables filtering; provides provenance for audit |
| **Storage layer** | PostgreSQL RLS policies, Neo4j label-based ACL, MongoDB field-level redaction | Defense in depth; prevents direct-DB-access bypass |

The programmatic surface is [Access Control Service Interface](access-control-service-interface.md); the runtime path is [ACL-Aware Query Flow](acl-aware-query-flow.md); provenance tags come from [Knowledge Graph with Provenance](knowledge-graph-with-provenance.md).
