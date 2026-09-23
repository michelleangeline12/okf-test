---
type: Concept
title: Federated LightRAG Overview
description: >-
  An enterprise-grade RAG platform built on LightRAG that unifies heterogeneous
  data sources into per-workspace knowledge graphs with fine-grained access
  control.
generated:
  by: Pin Test Agent/1
  at: '2026-09-23T16:43:41.585Z'
---
# Federated LightRAG Overview

Federated LightRAG is an architecture plan (status: **Draft**, dated **April 2026**) for an enterprise-grade RAG platform built on top of [LightRAG Fundamentals](lightrag-fundamentals.md) — a graph-enhanced retrieval-augmented generation framework. The plan extends LightRAG rather than replacing it.

**Stack:** Python backend + TypeScript frontend. **Use case:** enterprise knowledge management sync. **Strategy:** real-time CDC (change data capture).

## What is being built

A platform that lets organizations connect multiple heterogeneous data sources, unify their knowledge into a single queryable knowledge graph **per workspace**, and govern access with fine-grained role-based permissions.

## Goals

| Goal | Outcome |
| --- | --- |
| Unified knowledge access | Natural-language answers drawn from PostgreSQL, MongoDB, CSV files, PDF documents, REST APIs, and cloud storage through a single interface |
| Data sovereignty | Each workspace is isolated; users only see data they are permitted to access, down to the individual data-source level |
| Always-current knowledge | Real-time CDC keeps the knowledge graph synchronized with source databases |
| Schema visibility | Every connected source has its information schema extracted, indexed, and explorable — enabling schema-aware queries such as "what tables exist in the HR database?" |
| Enterprise readiness | Multi-workspace isolation, RBAC, OIDC/SAML authentication, audit logging |

## Where to go next

- The five gaps in vanilla LightRAG and how they are closed: [LightRAG Gaps and Solutions](lightrag-gaps-and-solutions.md)
- The layered design: [Federated LightRAG System Layers](federated-lightrag-system-layers.md)
- Feature-by-feature comparison: [LightRAG vs Federated LightRAG](lightrag-vs-federated-lightrag.md)
- Delivery plan: [Federated LightRAG Implementation Phases](federated-lightrag-implementation-phases.md)

This plan is one of the knowledge sources processed by the [OKF Knowledge Pipeline](okf-knowledge-pipeline.md).
