---
type: Concept
title: Federated LightRAG Overview
description: >-
  An enterprise RAG platform built on LightRAG that unifies heterogeneous data
  sources into one governed, per-workspace knowledge graph.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-05T02:12:00.369Z'
---
Federated LightRAG is an enterprise-grade RAG platform built **on top of** LightRAG (graph-enhanced retrieval-augmented generation). It lets an organization connect many heterogeneous data sources, unify their knowledge into a single queryable knowledge graph per workspace, and govern access with fine-grained role-based permissions. Status: Draft (April 2026). Stack: Python backend + TypeScript frontend. Use case: enterprise knowledge management sync with a real-time CDC strategy.

## What it achieves

| Goal | Outcome |
| --- | --- |
| Unified knowledge access | Natural-language answers drawn from PostgreSQL, MongoDB, CSV, PDF, REST APIs and cloud storage through one interface |
| Data sovereignty | Each workspace is isolated; users see only data they are permitted to access, down to the individual data source |
| Always-current knowledge | Real-time CDC keeps the knowledge graph synchronized with source databases |
| Schema visibility | Every connected source has its information schema extracted, indexed and explorable ("what tables exist in the HR database?") |
| Enterprise readiness | Multi-workspace isolation, RBAC, OIDC/SAML authentication, audit logging |

## Gaps in vanilla LightRAG that this closes

LightRAG only accepts file uploads, has no access control, no schema awareness, no real-time sync, and no multi-user workspace management. Federated LightRAG answers these with a Data Source Connector Layer, RBAC with source-level filtering, Information Schema Extraction, a Debezium CDC pipeline, and a Workspace Manager. See [LightRAG Foundations](lightrag-foundations.md) for the baseline behaviour being extended.

## Where to go next

- Layer-by-layer design: [Federated System Architecture](federated-system-architecture.md)
- Writing data in: [Data Ingestion Flow](data-ingestion-flow.md)
- Reading data out safely: [Access-Controlled Query Flow](access-controlled-query-flow.md)
- Delta versus the upstream project: [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md)
- Delivery order: [Implementation Roadmap](implementation-roadmap.md)
