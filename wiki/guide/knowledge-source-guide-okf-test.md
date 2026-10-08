---
type: Guide
title: 'Knowledge Source Guide: OKF Test'
description: >-
  A map of the OKF Test knowledge source — what raw material it contains and
  which wiki pages were authored from each source file.
generated:
  by: OKF Wiki Author/1
  at: '2026-10-08T10:39:31.744Z'
---
# Knowledge Source Guide: OKF Test

This wiki is built from a small validation repository (`OKF Test`, described in its README as a "Test repository for OKF validation"). The raw/ folder mixes three kinds of material: a substantial technical architecture plan, a workshop facilitation deck, and a set of tiny sample files used to exercise the conversion pipeline (Markdown, TXT, DOCX, XLSX, PPTX, PDF, PNG).

## Source inventory

| Raw source | Converted to | Wiki pages |
| --- | --- | --- |
| `lightrag-federated-architecture.md` / `.pdf` | Slide/table-heavy text extraction | The whole Federated LightRAG cluster |
| `Day 2_Global Coffee Challenge 1.md` / `.pptx` | Per-slide text dump | [Global Coffee Challenge Run of Show](global-coffee-challenge-run-of-show.md) and its two reference pages |
| `meeting-notes.md` / `.txt` | Plain text | [Q3 AI Strategy Review](q3-ai-strategy-review.md) |
| `requirements.md` / `.docx` | Plain text with escaped numbering | [OKF Knowledge Pipeline Requirements](okf-knowledge-pipeline-requirements.md) |
| `report.md` / `.xlsx` | Two markdown tables | [Quarterly Adoption Metrics](quarterly-adoption-metrics.md) |
| `process diagram.md` / `.png` | Image plus alt-text only | [Source-to-Pay Process with AI Intervention Points](source-to-pay-process-with-ai-intervention-points.md) |
| `sample.md` / `.txt` | Plain markdown | [Introduction to Machine Learning](introduction-to-machine-learning.md) |

## Reading paths

- **Architecture readers:** start with [Federated LightRAG Overview](../concept/federated-lightrag-overview.md), then [How LightRAG Works Today](../concept/how-lightrag-works-today.md), then the layer, flow, and interface references.
- **Facilitators:** start with [Global Coffee Challenge Run of Show](global-coffee-challenge-run-of-show.md).
- **Program tracking:** [Q3 AI Strategy Review](q3-ai-strategy-review.md) and [Quarterly Adoption Metrics](quarterly-adoption-metrics.md).

## Notes on the demo dataset

The `report` spreadsheet also carries a team roster alongside the quarterly figures: Alice (Engineer, Platform) and Bob (Designer, Product). These are sample rows for pipeline validation, not the attendees recorded in the meeting notes.

A guardrail scan marker is present at the repository root; it records `mode: off`, `flagged: 0`, and `scan: skipped`.
