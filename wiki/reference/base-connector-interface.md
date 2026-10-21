---
type: Reference
title: Base Connector Interface
description: >-
  The abstract BaseConnector contract — connect, test, extract schema, stream
  content, list resources, detect and listen for changes — that every data
  source connector implements.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-21T06:10:36.347Z'
---
## Summary

`BaseConnector` is the abstract interface every data source connector implements. Each connector knows how to connect to one source type, extract its schema, stream its content and listen for changes. Without a common interface, every source type would need bespoke integration code scattered through the system; with it, a new source type (for example Elasticsearch or Snowflake) is added by implementing a single class. This layer is the answer to LightRAG's file-upload-only ingestion gap — see [What LightRAG Lacks: Gaps and Solutions](what-lightrag-lacks-gaps-and-solutions.md).

## Supported connector types

`ConnectorType`: `postgresql`, `mysql`, `mongodb`, `sqlite`, `sqlserver`, `oracle`, `s3`, `gcs`, `azure_blob`, `csv`, `xlsx`, `pdf`, `docx`, `rest_api`, `graphql`, `kafka`.

`SyncMode`: `manual`, `scheduled`, `cdc`.

`ChangeType`: `insert`, `update`, `delete`, `schema_change`.

## Data model

| Dataclass | Key fields | Purpose |
| --- | --- | --- |
| `ColumnSchema` | `name`, `data_type`, `nullable`, `default_value`, `is_primary_key`, `is_foreign_key`, `foreign_key_ref` (`"table.column"`), `description`, `sample_values` | One column of a table |
| `TableSchema` | `name`, `schema_name`, `columns`, `row_count`, `description` | One table and its columns |
| `InformationSchema` | `source_id`, `source_type`, `tables`, `raw_schema`, `extracted_at`, `stats` | Full normalized schema for a source — see [Schema Extraction Per Connector](schema-extraction-per-connector.md) |
| `Document` | `content`, `metadata`, `source_id`, `resource_name`, `doc_type` (`database_row`, `pdf_page`, `csv_row`, …) | One indexable unit; `doc_type` is what document-type-level ACLs filter on |
| `ExtractedContent` | `source_id`, `documents`, optional `schema` | Payload handed to the ingestion pipeline |
| `ChangeEvent` | `source_id`, `workspace_id`, `change_type`, `resource_name`, `primary_key`, `before`, `after`, `timestamp` | CDC event — see [CDC and Real-Time Sync Architecture](cdc-and-real-time-sync-architecture.md) |
| `ResourceDescriptor` | `name`, `type` (`table`, `collection`, `file`, `endpoint`), `row_count`, `size_bytes`, `last_modified`, `description` | Entry in `list_resources()` |

## Abstract methods

```python
class BaseConnector(ABC):
    connector_type: ConnectorType

    async def connect(self, config: Dict[str, Any]) -> bool: ...
    async def test_connection(self) -> bool: ...
    async def extract_schema(self) -> InformationSchema: ...
    async def extract_content(
        self,
        resource_filter: Optional[List[str]] = None,
        since: Optional[datetime] = None,
    ) -> AsyncIterator[ExtractedContent]: ...
    async def list_resources(self) -> List[ResourceDescriptor]: ...
    async def get_changes(self, since: datetime) -> List[ChangeEvent]: ...
    async def supports_cdc(self) -> bool: ...
    async def start_cdc_listener(self, callback: callable, since: Optional[datetime] = None) -> None: ...
    async def stop_cdc_listener(self) -> None: ...
    async def disconnect(self) -> None: ...
```

`extract_content()` streams asynchronously and supports two narrowing arguments: `resource_filter` targets specific tables, files or endpoints, and `since` requests only data changed since the last sync (incremental extraction). `stop_cdc_listener()` and `disconnect()` are non-abstract cleanup hooks.

## Where the content goes

`ExtractedContent` is inserted through [Modified LightRAG Core](modified-lightrag-core.md), which tags every entity, relation and chunk with its source — see [Knowledge Graph with Provenance](../concept/knowledge-graph-with-provenance.md). Connectors are registered against a workspace via Workspace Manager Service and driven through Federated LightRAG REST API.
