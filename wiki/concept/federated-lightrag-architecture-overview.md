---
type: Concept
title: Federated LightRAG Architecture Overview
description: >-
  An enterprise-grade RAG platform built on LightRAG that unifies heterogeneous
  data sources into one governed knowledge graph per workspace, with
  fine-grained role-based access control.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-19T13:37:53.588Z'
---
## Summary

**Federated LightRAG** is an enterprise-grade RAG platform built *on top of* LightRAG (graph-enhanced retrieval-augmented generation, arXiv:2410.05779, EMNLP 2025). It lets an organization connect multiple heterogeneous data sources, unify their knowledge into a single queryable knowledge graph **per workspace**, and govern access with fine-grained role-based permissions. The architecture plan is recorded as **Status: Draft, Date: April 2026**, stack **Python backend + TypeScript frontend**, use case **enterprise knowledge management sync**, strategy **real-time CDC**.

## Goals and outcomes

| Goal | Outcome |
| --- | --- |
| Unified knowledge access | Natural-language questions answered from PostgreSQL databases, MongoDB collections, CSV files, PDF documents, REST APIs and cloud storage — through a single interface |
| Data sovereignty | Each workspace is isolated; users only see data they are permitted to access, down to the individual data-source level |
| Always-current knowledge | Real-time CDC keeps the knowledge graph synchronized with source databases as data changes |
| Schema visibility | Every connected source has its information schema extracted, indexed and explorable — enabling queries like "what tables exist in the HR database?" |
| Enterprise readiness | Multi-workspace isolation, RBAC, OIDC/SAML authentication, audit logging |

## The building blocks

1. A **connector layer** that turns databases, APIs, cloud storage and files into streamable content — [Base Connector Interface](base-connector-interface.md) and [Schema Extraction Per Connector](schema-extraction-per-connector.md).
2. A **modified LightRAG core** adding source provenance, ACL-aware retrieval and CDC-driven incremental updates — [Modified LightRAG Core](modified-lightrag-core.md), [Knowledge Graph with Provenance](knowledge-graph-with-provenance.md).
3. A **CDC / sync service** so the graph never goes stale — [CDC and Real-Time Sync Architecture](cdc-and-real-time-sync-architecture.md).
4. An **access control service** and **workspace manager** that turn LightRAG's bare workspace namespace into a governed, multi-team entity — [Access Control Model](access-control-model.md), [Workspace Manager Service](workspace-manager-service.md).
5. An **API gateway and frontend** exposing REST, WebSocket and GraphQL — [Federated LightRAG REST API](federated-lightrag-rest-api.md).

## Extend, not replace

LightRAG's retrieval quality is proven, so the plan extends it rather than rebuilding it. The baseline in [How LightRAG Works Today](../reference/how-lightrag-works-today.md) is preserved — including the 1200-token default chunk size, which stays configurable per workspace with **no regression**. The five gaps that motivate the extension are catalogued in [What LightRAG Lacks: Gaps and Solutions](../reference/what-lightrag-lacks-gaps-and-solutions.md), the before/after comparison in [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md), and delivery in [Implementation Phases](implementation-phases.md).

## References cited in the plan

LightRAG paper (Guo, Xia, Yu, Ao & Huang, 2024, arXiv:2410.05779, EMNLP 2025); LightRAG GitHub `HKUDS/LightRAG`; Sheth & Larson (1990) on federated database systems; Debezium (debezium.io); NodeRAG (arXiv:2504.11544); KG-Infused RAG (arXiv:2506.09542).
