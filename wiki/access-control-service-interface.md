---
type: Reference
title: Access Control Service Interface
description: >-
  The AccessControlService contract for workspace roles, per-source access
  levels, and document-type permissions.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Access Control Service Interface

**What it is:** the service that manages user permissions at the workspace level (role) and data source level (access level).

**Why it is needed:** enterprise knowledge management requires that different teams see different data. A single RBAC flag is insufficient — source-level and document-type-level granularity is required.

## Types

- `Role`: `owner` · `admin` · `editor` · `viewer`
- `SourceAccessLevel`: `full` · `read` · `schema_only` · `denied`
- `UserPermission`: `user_id`, `workspace_id`, `role`, `source_access` (map of `source_id` → access level), `doc_type_access` (map of `doc_type` → allowed)

## Methods

| Method | Purpose |
| --- | --- |
| `get_accessible_sources(user_id, workspace_id) -> List[str]` | Sources the user may query |
| `get_accessible_doc_types(user_id, workspace_id) -> List[str]` | Document types the user may query |
| `check_source_access(user_id, source_id, required_level) -> bool` | Level check against one source |
| `get_user_permissions(user_id, workspace_id) -> UserPermission` | Full permission record |
| `grant_source_access(admin_user_id, target_user_id, source_id, level) -> bool` | Grant access (admin action) |
| `revoke_source_access(admin_user_id, target_user_id, source_id) -> bool` | Revoke access (admin action) |

The semantics behind these types are in [Access Control Model](access-control-model.md); the retrieval-time consumer is [ACL-Aware Query Flow](acl-aware-query-flow.md). Membership management lives in [Workspace Manager Interface](workspace-manager-interface.md), and the HTTP surface in [Federated LightRAG REST API](federated-lightrag-rest-api.md).
