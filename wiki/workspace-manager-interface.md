---
type: Reference
title: Workspace Manager Interface
description: >-
  The WorkspaceManager contract that turns LightRAG's storage namespace into a
  first-class entity with members, registered sources, and settings.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Workspace Manager Interface

**What it is:** the component that extends LightRAG's native `workspace` field with user membership, data source registration, and per-workspace settings.

**Why it is needed:** LightRAG only uses `workspace` as a storage namespace. The platform needs workspace as a first-class entity with members, registered data sources, and governance policies — closing the "no multi-user workspace management" gap from [LightRAG Gaps and Solutions](lightrag-gaps-and-solutions.md).

## Types

`WorkspaceSettings` defaults:

| Setting | Default |
| --- | --- |
| `llm_model` | `gpt-4o` |
| `embedding_model` | `text-embedding-3-large` |
| `chunk_size` | `1200` |
| `chunk_overlap` | `100` |
| `default_retrieval_mode` | `mix` |
| `enable_schema_aware_retrieval` | `True` |
| `enable_citations` | `True` |

`Workspace`: `id`, `name`, `owner_id`, `organization_id`, `description`, `settings`, `created_at`, `updated_at`.

`DataSourceRegistration`: `id`, `workspace_id`, `connector_type`, `connector_config` (**encrypted at rest**), `display_name`, `description`, `sync_mode`, `sync_interval_seconds`, `last_synced_at`, `schema_snapshot`, `status` (`connected` | `error` | `syncing` | `disconnected`), `error_message`.

## Methods

| Method | Purpose |
| --- | --- |
| `create_workspace(name, owner_id, org_id, settings) -> Workspace` | Provision a workspace |
| `register_data_source(workspace_id, connector_type, config, display_name, sync_mode) -> DataSourceRegistration` | Attach a source |
| `remove_data_source(source_id) -> bool` | Detach a source |
| `trigger_sync(source_id) -> str` | Start a manual sync |
| `get_workspace_sources(workspace_id) -> List[DataSourceRegistration]` | List sources |
| `add_user_to_workspace(user_id, workspace_id, role) -> bool` | Grant membership with a role |
| `remove_user_from_workspace(user_id, workspace_id) -> bool` | Revoke membership |

Roles come from [Access Control Model](access-control-model.md); registration triggers the sequence in [Data Ingestion Flow](data-ingestion-flow.md). Chunk defaults match those in [LightRAG Fundamentals](lightrag-fundamentals.md).
