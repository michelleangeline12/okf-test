---
type: Concept
title: Federated LightRAG
description: >-
  An enterprise RAG platform that extends LightRAG with federated data-source
  connectors, per-workspace knowledge graphs, RBAC, and real-time CDC sync.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-05T02:30:09.090Z'
---
Federated LightRAG is an enterprise-grade RAG platform built on top of [LightRAG](lightrag-foundations.md) (graph-enhanced retrieval-augmented generation). It lets an organization connect many heterogeneous data sources, unify their knowledge into a single queryable knowledge graph per workspace, and govern access with fine-grained role-based permissions.

**Plan metadata:** Status: Draft · Date: April 2026 · Stack: Python backend + TypeScript frontend · Use case: enterprise knowledge management sync · Strategy: real-time CDC.

## What the platform achieves

| Goal | Outcome |
| --- | --- |
| Unified knowledge access | Users ask natural-language questions and get answers drawn from PostgreSQL databases, MongoDB collections, CSV files, PDF documents, REST APIs, and cloud storage — all through a single interface |
| Data sovereignty | Each workspace is isolated; users only see data they have permission to access, down to the individual data-source level |
| Always-current knowledge | Real-time CDC (change data capture) keeps the knowledge graph synchronized with source databases as data changes |
| Schema visibility | Every connected source has its information schema extracted, indexed, and explorable — enabling schema-aware queries such as “what tables exist in the HR database?” |
| Enterprise readiness | Multi-workspace isolation, RBAC, OIDC/SAML authentication, audit logging |

## How the pieces fit

The design is *extension, not replacement*: LightRAG's proven dual-level retrieval is kept, and four new capabilities are layered on top — a [Connector abstraction and BaseConnector interface](base-connector-interface.md), [schema extraction](schema-extraction-by-connector.md), a [CDC real-time sync pipeline](cdc-real-time-sync.md), and an [access control model](access-control-model.md). See [Federated System Architecture](federated-system-architecture.md) for the layer-by-layer view and [LightRAG Capability Gaps](lightrag-capability-gaps.md) for the problems each layer solves.

## Key interfaces

- [BaseConnector Interface](base-connector-interface.md) — the contract every data source implements
- [Workspace Manager](workspace-manager.md) — workspaces as first-class entities with members and source registries
- [Modified LightRAG Core](modified-lightrag-core.md) — provenance tagging, ACL-aware retrieval, incremental updates

## Delivery

Scope is sequenced into ten phases totalling 17–28 weeks (4–7 months): see [Implementation Roadmap](implementation-roadmap.md). A component-by-component diff against vanilla LightRAG is in [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md), and the public surface is in [REST API Surface](rest-api-surface.md).

> Source: [Architecture Plan: Federated LightRAG](/knowledgesources/c5f23978-31dc-46e3-83c4-e2dff88309d5/wiki?path=raw/lightrag-federated-architecture.md)
