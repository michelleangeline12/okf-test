---
type: Reference
title: Base Connector Interface
description: >-
  The abstract BaseConnector contract — types, dataclasses, and lifecycle
  methods that every data source connector must implement.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Base Connector Interface

**What it is:** an abstract interface that all data source connectors implement. Each connector knows how to connect to a specific type of source, extract its schema, stream content, and listen for changes.

**Why it is needed:** without a common interface, each data source type would require bespoke integration code scattered throughout the system. The `BaseConnector` abstraction lets a new source type (e.g. Elasticsearch, Snowflake) be added by implementing a single class.

## Supported connector types

`ConnectorType`: `postgresql`, `mysql`, `mongodb`, `sqlite`, `sqlserver`, `oracle`, `s3`, `gcs`, `azure_blob`, `csv`, `xlsx`, `pdf`, `docx`, `rest_api`, `graphql`, `kafka`.

`SyncMode`: `manual` · `scheduled` · `cdc`

`ChangeType`: `insert` · `update` · `delete` · `schema_change`

## Core dataclasses

| Dataclass | Key fields |
| --- | --- |
| `ColumnSchema` | `name`, `data_type`, `nullable`, `default_value`, `is_primary_key`, `is_foreign_key`, `foreign_key_ref` (`"table.column"`), `description`, `sample_values` |
| `TableSchema` | `name`, `schema_name`, `columns`, `row_count`, `description` |
| `InformationSchema` | `source_id`, `source_type`, `tables`, `raw_schema`, `extracted_at`, `stats` |
| `Document` | `content`, `metadata`, `source_id`, `resource_name`, `doc_type` (e.g. `database_row`, `pdf_page`, `csv_row`) |
| `ExtractedContent` | `source_id`, `documents`, optional `schema` |
| `ChangeEvent` | `source_id`, `workspace_id`, `change_type`, `resource_name`, `primary_key`, `before`, `after`, `timestamp` |
| `ResourceDescriptor` | `name`, `type` (`table`, `collection`, `file`, `endpoint`), `row_count`, `size_bytes`, `last_modified`, `description` |

## Abstract methods

| Method | Contract |
| --- | --- |
| `connect(config) -> bool` | Establish a connection to the data source |
| `test_connection() -> bool` | Verify the connection is alive |
| `extract_schema() -> InformationSchema` | Extract the full information schema |
| `extract_content(resource_filter=None, since=None) -> AsyncIterator[ExtractedContent]` | Extract content for indexing; `resource_filter` targets specific tables/files/endpoints, `since` enables incremental extraction |
| `list_resources() -> List[ResourceDescriptor]` | List all available tables, files, endpoints |
| `get_changes(since) -> List[ChangeEvent]` | Detect changes since last sync |
| `supports_cdc() -> bool` | Whether real-time CDC is supported |
| `start_cdc_listener(callback, since=None)` | Start listening for real-time changes |
| `stop_cdc_listener()` | Stop listening for changes |
| `disconnect()` | Clean up connection resources |

Concrete schema-extraction strategies per connector are in [Schema Extraction Per Connector](schema-extraction-per-connector.md); the lifecycle these methods drive is [Data Ingestion Flow](data-ingestion-flow.md). Connector delivery order is covered in [Federated LightRAG Implementation Phases](federated-lightrag-implementation-phases.md).
