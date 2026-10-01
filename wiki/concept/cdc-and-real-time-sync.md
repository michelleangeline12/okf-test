---
type: Concept
title: CDC and Real-Time Sync
description: >-
  The change-data-capture pipeline that watches source databases and files and
  applies incremental knowledge-graph updates.
generated:
  by: Pin Test Agent/1
  at: '2026-10-01T06:42:51.512Z'
---
# CDC and Real-Time Sync

**What it is:** a change data capture pipeline that monitors source databases and files for changes, then updates the knowledge graph incrementally so answers never come from a stale graph.

## Pipeline stages

| Stage | Role |
| --- | --- |
| PostgreSQL WAL / MySQL binlog via **Debezium Engine (embedded)** | Captures relational change streams |
| **MongoDB Change Stream Listener** | Captures document-database changes |
| **File Watcher (Watchdog)** | Captures filesystem changes |
| **Change Normalizer** | Converts heterogeneous events into the common `ChangeEvent` shape |
| **Change Deduplicator** | Collapses repeated changes to the same entity |
| **Change Event Bus (Redis Streams)** | Decouples change detection from KG update |
| **Schema Change Detector** | Flags `schema_change` events for re-extraction |
| **Workspace Router** | Routes each change to the owning workspace |
| **Incremental KG Updater** | Applies inserts, updates, deletes, and schema changes to the graph |

## Two deliberate design choices

- **Debezium embedded, not Kafka Connect.** Running Debezium inside the Python process avoids the operational overhead of a separate Kafka/Connect cluster. At enterprise scale it can be swapped for a standalone Debezium Server.
- **A change event bus.** The bus decouples detection from updating, and deduplication happens *before* LightRAG sees the change — avoiding unnecessary LLM calls for entity summarization.

## Change semantics

`ChangeEvent` carries `source_id`, `workspace_id`, `change_type` (`insert` | `update` | `delete` | `schema_change`), `resource_name`, `primary_key`, `before`, `after`, and `timestamp`. Handling is defined in `incremental_update` — see [Modified LightRAG Core](modified-lightrag-core.md):

- **INSERT** — extract entities, add to the KG.
- **UPDATE** — re-extract affected entities, merge into the KG.
- **DELETE** — remove entities no longer present.
- **SCHEMA_CHANGE** — re-extract schema, update schema nodes ([Schema Extraction Per Connector](schema-extraction-per-connector.md)).

This is only practical because LightRAG supports O(n) incremental updates ([How LightRAG Works Today](how-lightrag-works-today.md)). Pipeline status and the recent change log are exposed at `/cdc/status` and `/cdc/changelog` ([REST API Reference](rest-api-reference.md)); the trigger side is in [Data Ingestion Flow](../playbook/data-ingestion-flow.md).
