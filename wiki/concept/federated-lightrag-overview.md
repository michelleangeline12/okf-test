---
type: Concept
title: Federated LightRAG Overview
description: >-
  An enterprise-grade RAG platform built on LightRAG that federates
  heterogeneous data sources into one governed knowledge graph per workspace.
generated:
  by: Pin Test Agent/1
  at: '2026-10-01T06:42:51.512Z'
---
# Federated LightRAG Overview

**Status:** Draft · **Date:** April 2026 · **Stack:** Python backend + TypeScript frontend · **Use case:** Enterprise Knowledge Management Sync · **Sync strategy:** Real-time CDC

Federated LightRAG is an enterprise-grade RAG platform built *on top of* [How LightRAG Works Today | LightRAG] (graph-enhanced retrieval-augmented generation). It lets an organization connect many heterogeneous data sources, unify their knowledge into a single queryable knowledge graph **per workspace**, and govern access with fine-grained role-based permissions. LightRAG is extended, not replaced.

## Goals

| Goal | Outcome |
| --- | --- |
| Unified knowledge access | Natural-language questions answered from PostgreSQL, MongoDB, CSV, PDF, REST APIs, and cloud storage through one interface |
| Data sovereignty | Each workspace is isolated; users see only data they may access, down to the individual data-source level |
| Always-current knowledge | Real-time CDC keeps the knowledge graph synchronized with source databases |
| Schema visibility | Every connected source has its information schema extracted, indexed, and explorable — enabling queries like "what tables exist in the HR database?" |
| Enterprise readiness | Multi-workspace isolation, RBAC, OIDC/SAML authentication, audit logging |

## What changes relative to vanilla LightRAG

The five gaps that drive the design (only file uploads, no access control, no schema awareness, no real-time sync, no multi-user workspace management) are enumerated in [LightRAG Gaps and What We Fill](../reference/lightrag-gaps-and-what-we-fill.md), and the component-by-component delta is tabulated in [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md).

## Key design commitments

- **Provenance everywhere** — every entity, relation, and chunk carries `workspace_id`, `source_id`, `source_type`, `extraction_timestamp` ([Knowledge Graph Provenance](knowledge-graph-provenance.md)).
- **Filter at query time, not storage time** — the graph stays complete; users see authorized slices ([Query Flow with Access Control](../playbook/query-flow-with-access-control.md)).
- **One connector abstraction** — all source types implement [Base Connector Interface](base-connector-interface.md).
- **Keep LightRAG's defaults** — chunk size stays 1200 tokens, now configurable per workspace, so there is no retrieval regression.

## Reading path

[Federated Architecture Layers](../reference/federated-architecture-layers.md) → [Data Ingestion Flow](../playbook/data-ingestion-flow.md) → [CDC and Real-Time Sync](cdc-and-real-time-sync.md) → [Access Control Model](access-control-model.md) → [REST API Reference](rest-api-reference.md) → [Implementation Phases](implementation-phases.md). For the wider context of how these documents become knowledge, see [OKF Knowledge Pipeline](okf-knowledge-pipeline.md).
