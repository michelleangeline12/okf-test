---
type: Concept
title: LightRAG Foundations
description: >-
  How LightRAG works today — pipeline, graph construction, storage, workspaces
  and incremental updates — as the baseline for federation work.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-05T02:12:00.369Z'
---
LightRAG is a graph-enhanced RAG framework (arXiv:2410.05779, EMNLP 2025). The federation effort **extends** it rather than replacing it, so its native behaviour defines what can be reused unchanged.

## Core pipeline

Document → chunking (default **1200 tokens, 100 overlap**) → LLM entity & relation extraction → knowledge graph (NetworkX / Neo4j / PostgreSQL) → dual-level retrieval → context fusion → LLM response. The user query path mirrors this: query → entity extraction from the query → retrieval.

## Knowledge graph construction

Documents are split into chunks; an LLM extracts entities (Person, Organization, Location, Event, Concept, …) and typed relationships with keywords and descriptions. When two chunks produce entities with the same normalized name, LightRAG merges them: descriptions are concatenated with a `<SEP>` separator, and once the fragment count exceeds `FORCE_LLM_SUMMARY_ON_MERGE` (default **8**), an LLM map-reduce summarization condenses them.

Node properties look like:

```json
{
  "entity_name": "Tesla Inc",
  "entity_type": "organization",
  "description": "American electric vehicle manufacturer<SEP>Also produces solar panels",
  "source_id": "chunk_abc123<SEP>chunk_def456",
  "file_path": "report.pdf<SEP>overview.docx",
  "created_at": 1712345678,
  "weight": 1.0
}
```

## Native workspace isolation

`LightRAG` carries a `workspace` field (defaulting to the `WORKSPACE` env var). Every storage backend uses it as a namespace/prefix, so workspaces coexist on shared storage without leakage. The federation layer builds RBAC and federated sources on top of this native concept — see [Workspace Manager](workspace-manager.md).

## Incremental updates

LightRAG supports **O(n) incremental updates** instead of an O(N) full rebuild: new entities are matched by normalized name against the existing graph, merged if found, or created as new nodes. This property is what makes real-time CDC ingestion feasible — see [CDC Real-Time Sync Pipeline](cdc-real-time-sync-pipeline.md).

## Retrieval and storage

Retrieval behaviour is described in [Dual-Level Retrieval](dual-level-retrieval.md); the four pluggable storage layers in [Pluggable Storage Backends](../reference/pluggable-storage-backends.md).

## Gaps we fill

File-upload-only ingestion, no access control, no schema awareness, no real-time sync, and no multi-user workspace management — each mapped to a new component in [Federated LightRAG Overview](federated-light-rag-overview.md).
