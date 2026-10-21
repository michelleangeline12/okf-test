---
type: Reference
title: Schema Extraction Per Connector
description: >-
  How each connector pulls an information schema — SQL information_schema joins,
  MongoDB sampling, OpenAPI and GraphQL introspection, pandas profiling for
  files — and stores it as KG metadata.
generated:
  by: test-okf-in-deep-agent/1
  at: '2026-10-21T06:10:36.347Z'
---
## Summary

Each connector extracts the information schema (tables, columns, types, foreign keys) from its data source, and that schema is stored as **metadata nodes in the knowledge graph**. Users need to discover what data exists: schema-aware queries such as "what tables are in the HR database?" or "which columns contain PII?" become possible, and schema metadata helps the LLM generate better answers when querying structured data.

## Per-source techniques

| Source | Technique |
| --- | --- |
| PostgreSQL | Join `information_schema.tables` → `columns` → `key_column_usage` → `table_constraints` → `referential_constraints` → `key_column_usage` (referenced) to get schema, table, column, data type, nullability, default, constraint type and referenced table/column. Excludes `pg_catalog` and `information_schema`; ordered by schema, table, `ordinal_position`. |
| MySQL | Same shape against `information_schema.TABLES` / `COLUMNS` / `KEY_COLUMN_USAGE`, additionally selecting `COLUMN_KEY` and `REFERENCED_TABLE_NAME`/`REFERENCED_COLUMN_NAME` (non-null only). Excludes `mysql`, `information_schema`, `performance_schema`, `sys`. |
| MongoDB | Aggregation over a `$sample` of 1,000 documents: `$project` the `$type` of the root and `$objectToArray` of each document, `$unwind` the fields, then `$group` by field name collecting the set of observed types and a count — inferred schema for a schemaless store. |
| REST API (OpenAPI) | `GET {base_url}/openapi.json`, then return the list of `paths`, `components.schemas` and `info`. |
| CSV / XLSX | `pd.read_csv(file_path, nrows=100)`; per column return `name`, `dtype`, `null_rate` (`isnull().mean()`), `unique_count` (`nunique()`) and the first five `sample_values`, plus `row_count`. |
| GraphQL | POST an introspection query for `__schema { types { name kind fields { name type { name kind } } } queryType { name } mutationType { name } }` and return `data.__schema`. |

## Normalized output

All techniques produce the same `InformationSchema` object (`source_id`, `source_type`, `tables
