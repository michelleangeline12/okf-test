---
type: Guide
title: OKF Test Knowledge Source
description: >-
  Map of the TEST-DEMO knowledge source: which raw documents it contains, what
  each one covers, and which wiki pages were derived from them.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-09T01:52:12.643Z'
---
## Summary

This wiki is built from the **TEST-DEMO** knowledge source, a repository whose README describes itself simply as a *"Test repository for OKF validation"* (type: guide, title: *OKF Test*). The raw/ folder mixes two distinct bodies of knowledge: an **OKF / knowledge-pipeline program** (requirements, meeting notes, metrics, sample content) and a **Federated LightRAG architecture plan**, plus two standalone training artifacts.

## What is in the source

| Raw document | Subject | Wiki pages derived from it |
| --- | --- | --- |
| `lightrag-federated-architecture.md` (+ .pdf) | Full architecture plan for Federated LightRAG | most architecture pages below |
| `requirements.md` (+ `requirements.docx`) | Functional / non-functional requirements for the OKF knowledge pipeline | [OKF Knowledge Pipeline Requirements](okf-knowledge-pipeline-requirements.md) |
| `meeting-notes.md` (+ .txt) | Q3 AI Strategy Review minutes | [Q3 AI Strategy Review Meeting](q3-ai-strategy-review-meeting.md) |
| `report.md` (+ `report.xlsx`) | Quarterly revenue/user table and team roster | [Quarterly Business Metrics](quarterly-business-metrics.md) |
| `sample.md` (+ .txt) | Primer on machine learning | [Introduction to Machine Learning](introduction-to-machine-learning.md) |
| `process diagram.md` (+ .png) | Source-to-Pay procurement workflow with AI intervention points | [Source-to-Pay Process Diagram](source-to-pay-process-diagram.md) |
| `Day 2_Global Coffee Challenge 1.md` (+ .pptx) | Breakout-session exercise deck | the three Global Coffee pages |

## Reading paths

- **Architecture track:** start with [Federated LightRAG Architecture Overview](../concept/federated-lightrag-architecture-overview.md), then [How LightRAG Works Today](../reference/how-lightrag-works-today.md) and [LightRAG Gaps and Enterprise Solutions](lightrag-gaps-and-enterprise-solutions.md).
- **Program track:** [OKF Knowledge Pipeline Requirements](okf-knowledge-pipeline-requirements.md) → [Q3 AI Strategy Review Meeting](q3-ai-strategy-review-meeting.md) → [Quarterly Business Metrics](quarterly-business-metrics.md).
- **Facilitation track:** [Global Coffee Breakout Session Runbook](global-coffee-breakout-session-runbook.md).

## Note on source fidelity

Binary originals (`.pptx`, `.pdf`, `.docx`, `.xlsx`, `.png`) are represented in the source by their converted markdown; where a conversion is only an image caption or a table, the wiki records exactly that and does not reconstruct the missing detail.
