---
type: Concept
title: Federated LightRAG Architecture Overview
description: >-
  An enterprise-grade RAG platform built on LightRAG that unifies heterogeneous
  data sources into one queryable knowledge graph per workspace, governed by
  fine-grained role-based access control.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-09T01:52:12.643Z'
---
## Summary

**Federated LightRAG** is an enterprise-grade RAG platform built *on top of* LightRAG (graph-enhanced retrieval-augmented generation). It enables an organization to connect multiple heterogeneous data sources, unify their knowledge into a single queryable knowledge graph **per workspace**, and govern access with fine-grained role-based permissions. The plan is recorded as **Status: Draft, Date: April 2026**, stack **Python backend + TypeScript frontend**, use case **enterprise knowledge management sync**, strategy **real-time CDC**.

## What we are achieving

| Goal | Outcome |
| --- | --- |
| Unified knowledge access | Users ask natural-language questions and get answers drawn from PostgreSQL databases, MongoDB collections, CSV files, PDF documents, REST APIs and cloud storage — all through a single interface |
| Data sovereignty | Each workspace is isolated; users only see data they have permission to access, down to the individual data-source level |
| Always-current knowledge | Real-time CDC (change data capture) keeps the knowledge graph synchronized with source databases as data changes |
| Schema visibility | Every connected data source has its information schema extracted, indexed and explorable — enabling schema-aware queries such as "what tables exist in the HR database?" |
| Enterprise readiness | Multi-workspace isolation, RBAC, OIDC/SAML authentication, audit logging |

## The five building blocks

1. A **connector layer** that turns databases, APIs, cloud storage and files into streamable content — see [Base Connector Interface](base-connector-interface.md) and [Schema Extraction Per Connector](schema-extraction-per-connector.md).
2. A **modified LightRAG core** that adds source provenance, ACL-aware retrieval and CDC-driven incremental updates — see [Knowledge Graph Provenance](knowledge-graph-provenance.md).
3. A **CDC / sync service** so the graph never goes stale — see [CDC and Real-Time Sync Architecture](cdc-and-real-time-sync-architecture.md).
4. An **access control service** and **workspace manager** that turn LightRAG's bare workspace namespace into a governed, multi-team entity — see [Access Control Model](access-control-model.md) and [Workspace Manager Service](workspace-manager-service.md).
5. An **API gateway and frontend** exposing REST, WebSocket and GraphQL — see [Federated LightRAG REST API](federated-lightrag-rest-api.md).

## Why "extend, not replace"

LightRAG is a graph-enhanced RAG framework (arXiv:2410.05779, EMNLP 2025) with proven retrieval quality. The plan explicitly extends it rather than rebuilding it, so the baseline behaviour documented in [How LightRAG Works Today](../reference/how-lightrag-works-today.md) is preserved — including the 1200-token default chunk size, which stays configurable per workspace with **no regression**.

## Related

- [LightRAG Gaps and Enterprise Solutions](lightrag-gaps-and-enterprise-solutions.md)
- [Federated LightRAG System Layers](federated-lightrag-system-layers.md)
- [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md)
- [Implementation Plan and Phases](implementation-plan-and-phases.md)

## References cited in the plan

LightRAG paper (Guo, Xia, Yu, Ao & Huang, 2024, arXiv:2410.05779, EMNLP 2025); LightRAG GitHub `HKUDS/LightRAG`; Sheth & Larson (1990) on federated database systems; Debezium (debezium.io); NodeRAG (arXiv:2504.11544); KG-Infused RAG (arXiv:2506.09542).
