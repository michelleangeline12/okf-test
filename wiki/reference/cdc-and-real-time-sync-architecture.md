---
type: Reference
title: CDC and Real-Time Sync Architecture
description: >-
  The change-data-capture pipeline — Debezium, MongoDB Change Streams and file
  watchers feeding a Redis Streams event bus — that keeps the knowledge graph
  current without re-ingestion.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-21T06:10:36.347Z'
---
## Summary

A change data capture pipeline monitors source databases and files for changes and updates the knowledge graph **incrementally**. It answers LightRAG's "no real-time sync" gap: data changes constantly, and a stale knowledge graph gives wrong answers — see [What LightRAG Lacks: Gaps and Solutions](what-lightrag-lacks-gaps-and-solutions.md).

## Pipeline stages

**Sources → CDC layer → event bus → change processing → LightRAG processing**

| Stage | Detail |
| --- | --- |
| Data sources | PostgreSQL **WAL**, MySQL **binlog**, MongoDB **Change Streams**, file system |
| CDC layer | **Debezium Engine (embedded)** for PostgreSQL/MySQL, **MongoDB Change Stream Listener**, **File Watcher (Watchdog)** |
| Change Event Bus | **Redis Streams** — decouples change detection from KG update |
| Change processing | **Change Normalizer** → **Change Deduplicator** → **Workspace Router** |
| Schema handling | **Schema Change Detector** raises `schema_change` events |
| LightRAG processing | **Incremental KG Updater** calls `incremental_update(workspace_id, changes)` on the [Modified LightRAG Core](modified-lightrag-core.md) |

## Change event handling

Each event is a `ChangeEvent` (`source_id`, `workspace_id`, `change_type`, `resource_name`, `primary_key`, `before`, `after`, `timestamp`) defined in [Base Connector Interface](base-connector-interface.md). The updater maps `change_type` to a graph operation:

| ChangeType | Graph effect |
| --- | --- |
| `insert` | Extract entities from the new row/document and add them to the KG |
| `update` | Re-extract the affected entities and merge them into the graph |
| `delete` | Remove entities that are no longer present in the source |
| `schema_change` | Re-extract the schema and update the schema nodes for that source |

## Two design decisions

**Embedded Debezium, not Kafka Connect.** Running Debezium embedded in the Python process avoids the operational overhead of a separate Kafka/Connect cluster. For enterprise scale the design can be swapped to a standalone Debezium Server without changing the rest of the pipeline.

**A change event bus.** Redis Streams decouples change detection from KG update. Multiple changes to the same entity are **deduplicated** before reaching LightRAG, avoiding unnecessary LLM calls for entity summarization (LightRAG triggers an LLM map-reduce summary once an entity accumulates more than `FORCE_LLM_SUMMARY_ON_MERGE` = 8 description fragments).

## Relationship to the initial sync

Registering a source performs an **initial sync** that establishes the baseline KG; CDC then keeps it current without manual re-ingestion. Both paths are triggered from the same registration call and monitored through the CDC status endpoints in Federated LightRAG REST API. Because updates are O(n) rather than a full O(N) rebuild (see [How LightRAG Works Today](how-lightrag-works-today.md)), real-time ingestion is feasible at all. The CDC service is delivered in Phase 7 of Implementation Phases — the most complex operational component — and its patterns are reused for MySQL in Phase 8.
