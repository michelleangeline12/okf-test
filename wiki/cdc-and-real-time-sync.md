---
type: Concept
title: CDC and Real-Time Sync
description: >-
  The change data capture pipeline that watches source databases and files and
  updates the knowledge graph incrementally.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# CDC and Real-Time Sync

**What it is:** a change data capture (CDC) pipeline that monitors source databases and files for changes, then updates the knowledge graph incrementally. It is what delivers the "always-current knowledge" goal of [Federated LightRAG Overview](federated-lightrag-overview.md).

## Pipeline stages (bottom to top)

| Stage | Components |
| --- | --- |
| **Data sources** | PostgreSQL WAL, MySQL binlog, MongoDB Change Streams, File System (Watchdog) |
| **CDC layer** | Debezium Engine (embedded), MongoDB Change Stream Listener, File Watcher (Watchdog) |
| **Change Event Bus** | Redis Streams |
| **Change processing** | Change Normalizer → Change Deduplicator → Workspace Router |
| **LightRAG processing** | Incremental KG Updater; Schema Change Detector emits `schema changed` |

## Design decisions

- **Why Debezium embedded (not Kafka Connect):** running Debezium embedded in the Python process avoids the operational overhead of a separate Kafka/Connect cluster. For enterprise scale, this can be swapped for a standalone Debezium Server.
- **Why a Change Event Bus:** it decouples change detection from knowledge-graph updates. Multiple changes to the same entity are deduplicated before reaching LightRAG, avoiding unnecessary LLM calls for entity summarization.

## How changes are applied

`incremental_update()` maps change types to graph operations — INSERT extracts and adds entities, UPDATE re-extracts and merges affected entities, DELETE removes entities no longer present, and SCHEMA_CHANGE re-extracts the schema and updates schema nodes. This builds on LightRAG's O(n) incremental updates described in [LightRAG Fundamentals](lightrag-fundamentals.md), and depends on provenance tags from [Knowledge Graph with Provenance](knowledge-graph-with-provenance.md).

CDC status and changelog are exposed through the endpoints in [Federated LightRAG REST API](federated-lightrag-rest-api.md); the Debezium-based CDC Service is Phase 7 of [Federated LightRAG Implementation Phases](federated-lightrag-implementation-phases.md).
