---
type: Reference
title: How LightRAG Works Today
description: >-
  Baseline LightRAG behaviour — chunking, entity extraction, dual-level
  retrieval, pluggable storage, workspace isolation and incremental updates —
  that the federated platform extends.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-09T01:52:12.643Z'
---
## Summary

LightRAG is a graph-enhanced RAG framework (arXiv:2410.05779, EMNLP 2025). Understanding its current behaviour matters because the federated platform **extends it, not replaces it** — see [Federated LightRAG Architecture Overview](../concept/federated-lightrag-architecture-overview.md).

## Core pipeline

`Document → Chunking (default 1200 tokens, 100 overlap) → LLM entity & relation extraction → Knowledge Graph (NetworkX / Neo4j / PostgreSQL) → Dual-level retrieval → Context fusion → LLM response`. On the query side, entities are extracted from the user query and fed into the same retrieval stage.

## Knowledge graph construction

Documents are split into chunks; an LLM extracts **entities** (Person, Organization, Location, Event, Concept, …) and **relationships** (typed edges with keywords and descriptions) from each chunk. When two chunks produce entities with the same normalized name, LightRAG merges them: descriptions are concatenated with a `<SEP>` separator, and if the fragment count exceeds `FORCE_LLM_SUMMARY_ON_MERGE` (default **8**), an LLM map-reduce summarization condenses them.

Actual graph node properties:

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

## Dual-level retrieval

Simple vector search finds isolated facts; graph traversal finds relationships. Both
