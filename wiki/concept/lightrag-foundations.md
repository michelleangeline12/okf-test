---
type: Concept
title: LightRAG Foundations
description: >-
  How vanilla LightRAG works today — its core pipeline, knowledge-graph
  construction, entity merging, workspace isolation, and incremental updates.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-05T02:30:09.090Z'
---
LightRAG is a graph-enhanced RAG framework (arXiv:2410.05779, EMNLP 2025). Understanding it matters because Federated LightRAG extends it rather than replacing it.

## Core pipeline

Document → chunking (default **1200 tokens, 100 overlap**) → LLM entity & relation extraction → knowledge graph (NetworkX / Neo4j / PostgreSQL) → dual-level retrieval → context fusion → LLM response. The user query path runs entity extraction on the query itself before retrieval.

## Knowledge graph construction

Documents are split into chunks; an LLM extracts entities (Person, Organization, Location, Event, Concept, …) and relationships (typed edges with keywords and descriptions) from each chunk.

**Entity merging:** when two chunks produce entities with the same normalized name, LightRAG merges them — descriptions are concatenated with a `<SEP>` separator, and if the fragment count exceeds `FORCE_LLM_SUMMARY_ON_MERGE` (default **8**), an LLM map-reduce summarization condenses them.

Entity (graph node) properties in practice:

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

## Retrieval

Retrieval operates at two levels with six query modes (naive, local, global, hybrid, mix, bypass; default `mix`) — details in [Dual-Level Retrieval](dual-level-retrieval.md).

## Storage and isolation

LightRAG exposes four pluggable storage layers — see [Pluggable Storage Layers](pluggable-storage-layers.md). It natively supports workspace-based isolation via a `workspace` field:

```python
@dataclass
class LightRAG:
    workspace: str = field(default_factory=lambda: os.getenv("WORKSPACE", ""))
    """Workspace for data isolation."""
```

All backends use the workspace value as a namespace/prefix, so workspaces coexist on the same backend without data leakage. Federated LightRAG extends this native concept with RBAC and federated data sources (see [Workspace Manager](workspace-manager.md)).

## Incremental updates

LightRAG supports **O(n) incremental updates** versus O(N) full rebuild: new entities are matched by normalized name against the existing graph, merged if found, or created as new nodes. This property is what makes real-time CDC ingestion feasible — see [CDC Real-Time Sync](cdc-real-time-sync.md).

What LightRAG does *not* provide is catalogued in [LightRAG Capability Gaps](lightrag-capability-gaps.md).

> Source: [Architecture Plan: Federated LightRAG](/knowledgesources/c5f23978-31dc-46e3-83c4-e2dff88309d5/wiki?path=raw/lightrag-federated-architecture.md)
