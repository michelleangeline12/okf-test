---
type: Concept
title: Federated LightRAG Overview
description: >-
  An enterprise-grade RAG platform built on LightRAG that unifies heterogeneous
  data sources into one governed knowledge graph per workspace.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-08T10:39:31.744Z'
---
# Federated LightRAG Overview

**Status:** Draft · **Date:** April 2026 · **Stack:** Python backend + TypeScript frontend · **Use case:** Enterprise knowledge management sync · **Strategy:** Real-time CDC

Federated LightRAG is an enterprise-grade RAG platform built *on top of* [How LightRAG Works Today](how-lightrag-works-today.md) — the plan extends LightRAG rather than replacing it. It enables an organization to connect multiple heterogeneous data sources, unify their knowledge into a single queryable knowledge graph per workspace, and govern access with fine-grained role-based permissions.

## Goals and outcomes

| Goal | Outcome |
| --- | --- |
| Unified knowledge access | Users ask natural-language questions and get answers drawn from PostgreSQL databases, MongoDB collections, CSV files, PDF documents, REST APIs, and cloud storage — all through a single interface |
| Data sovereignty | Each workspace is isolated; users only see data they have permission to access, down to the individual data-source level |
| Always-current knowledge | Real-time CDC (change data capture) keeps the knowledge graph synchronized with source databases as data changes |
| Schema visibility | Every connected data source has its information schema extracted, indexed, and explorable — enabling schema-aware queries like "what tables exist in the HR database?" |
| Enterprise readiness | Multi-workspace isolation, RBAC, OIDC/SAML authentication, audit logging |

## What the extension adds

Five gaps in vanilla LightRAG drive the design — file-only ingestion, no access control, no schema awareness, no real-time sync, and no multi-user workspace management. Each is answered by a new layer, described in [What LightRAG Lacks](what-lightrag-lacks.md) and [System Layers and Why They Exist](system-layers-and-why-they-exist.md).

## Key design decisions

- **Provenance everywhere.** Every entity, relation, and chunk is tagged with `workspace_id`, `source_id`, `source_type`, and `extraction_timestamp` — see [Knowledge Graph Provenance](knowledge-graph-provenance.md).
- **Filter at query time, not at storage time.** The graph stays complete; users see authorized slices — see [ACL-Aware Query Flow](acl-aware-query-flow.md).
- **One connector abstraction.** New source types are added by implementing a single class — see [BaseConnector Interface](baseconnector-interface.md).
- **Embedded Debezium** instead of a Kafka Connect cluster — see [CDC and Real-Time Sync](cdc-and-real-time-sync.md).

Delivery is sequenced in ten phases: [Implementation Phases](implementation-phases.md). The before/after picture is in [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md), and the surface exposed to users is the [Federated LightRAG REST API](federated-lightrag-rest-api.md).
