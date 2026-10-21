---
type: Reference
title: Modified LightRAG Core
description: >-
  LightRAG's engine with three additions — source provenance tagging, ACL-aware
  retrieval and CDC-driven incremental updates — exposed as the
  FederatedLightRAG class.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-21T06:10:36.347Z'
---
## Summary

Vanilla LightRAG treats all data equally. The modified core keeps the proven dual-level retrieval described in [How LightRAG Works Today](how-lightrag-works-today.md) and adds three things: **source provenance tagging**, **ACL-aware retrieval** and **CDC-driven incremental updates**. It is the layer between the ingestion pipeline and the storage layer in [Federated LightRAG System Layers](federated-lightrag-system-layers.md).

## Interface

```python
class FederatedLightRAG:
    def __init__(
        self,
        workspace_id: str,
        storage_config: Dict[str, Any],
        llm_config: Dict[str, Any],
        embedding_config: Dict[str, Any],
    ): ...

    async def insert_with_provenance(self, content: ExtractedContent,
                                     workspace_id: str, source_id: str) -> None: ...
    async def query_with_acl(self, query: str, mode: str,
                             accessible_sources: List[str],
                             accessible_doc_types: List[str]): ...
    async def incremental_update(self, changes: List[ChangeEvent],
                                 workspace_id: str) -> None: ...
    async def delete_source_data(self, source_id: str) -> None: ...
    async def get_source_schema(self, source_id: str) -> InformationSchema: ...
```

## Method behaviour

| Method | What it does |
| --- | --- |
| `insert_with_provenance` | Inserts `ExtractedContent` (produced by a connector, see [Base Connector Interface](base-connector-interface.md)) into the KG with source tagging. Every entity, relation and chunk receives `workspace_id`, `source_id`, `source_type` and `extraction_timestamp` — see [Knowledge Graph with Provenance](../concept/knowledge-graph-with-provenance.md). |
| `query_with_acl` | 1) Perform normal dual-level retrieval; 2) filter results to only the accessible `source_id`s and `doc_type`s; 3) generate the response from the filtered context. Filters are supplied by the access control service — see [Access Control Model](access-control-model.md). |
| `incremental_update` | Processes CDC change events: **INSERT** extracts entities and adds them to the KG; **UPDATE** re-extracts the affected entities and merges them; **DELETE** removes entities no longer present; **SCHEMA_CHANGE** re-extracts the schema and updates schema nodes. Fed by [CDC and Real-Time Sync Architecture](cdc-and-real-time-sync-architecture.md). |
| `delete_source_data` | Removes all entities, relations and chunks belonging to one source — used when a data source is disconnected from a workspace. Only possible because of provenance tags. |
| `get_source_schema` | Returns the stored `InformationSchema` for a source, backing the schema-explorer endpoints in Federated LightRAG REST API. |

## Why extend rather than rewrite

Retrieval quality is already proven, so nothing in the retrieval path is replaced. The only behavioural change on the read side is a post-retrieval filter, which preserves the graph's structural integrity: the graph stays complete and users simply see the slices they are authorized for. Default chunking is unchanged at 1200 tokens (now configurable per workspace), so there is **no regression** — the full comparison is in LightRAG vs Federated LightRAG.

Incremental updates are feasible because LightRAG already supports O(n) updates against an existing graph (new entities matched by normalized name, merged or created), which is why the core modification is additive rather than structural. The modified core is built in Phase 3 of Implementation Phases, before any connector can insert data.
