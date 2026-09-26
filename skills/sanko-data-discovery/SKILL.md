---
name: sanko-data-discovery
description: Use before any query on Sanko data (DWH, MySQL, reports, figures). The Sanko warehouse changes often, so discover the live schema you are allowed to read instead of assuming table or column names.
---

# Discover Sanko data before querying

The Sanko data warehouse evolves continuously: tables, columns and schemas are added or renamed. Never rely on remembered or guessed names. Discover the current structure every task.

## Connection

The database connection is provided by the environment of `run_python`:

- `SANKO_DB_HOST`, `SANKO_DB_PORT`, `SANKO_DB_USER`, `SANKO_DB_PASSWORD`
- `SANKO_DB_NAME` (your writable schema), `SANKO_DB_REF_SCHEMA` (read-only reference data), `SANKO_DB_WORK_SCHEMA`

Never print, log or save the password or the full environment.

## Discovery steps

1. **List what you can read.** `SHOW DATABASES;` then, for each relevant schema, query `information_schema.TABLES` (`TABLE_NAME`, `TABLE_ROWS`, `TABLE_COMMENT`, `UPDATE_TIME`).
2. **Read the meaning.** Query `information_schema.COLUMNS` (`COLUMN_NAME`, `DATA_TYPE`, `COLUMN_COMMENT`) for candidate tables. Table and column comments are the documentation; prefer tables whose comments match the question.
3. **Check relationships.** Query `information_schema.KEY_COLUMN_USAGE` for foreign keys; otherwise infer joins from matching `*_id` columns and verify with a count of unmatched rows.
4. **Sample before trusting.** `SELECT ... LIMIT 20` and profile key columns (row count, date range, nulls, distinct values) before computing.
5. **Query and compute.** Use explicit column lists, filters and date ranges. Aggregate in SQL where possible; load into pandas only what is needed.
6. **Write only to your own schema.** Reference data is read-only. Put intermediate tables in your writable schema (`SANKO_DB_NAME`) with a clear prefix, and drop them when no longer needed.

## Reporting

- State which schemas, tables and date ranges were used, and any data quality issues found.
- If the needed data is not present or not readable, say so plainly and list what exists instead. Never fabricate figures.
- Deliver results as files (Excel for tables, charts as PNG); keep the final summary short.
