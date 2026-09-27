---
name: sanko-data-discovery
description: Use before any query on Sanko data (DWH, MySQL, reports, figures). The Sanko warehouse changes often, so discover the live schema you are allowed to read instead of assuming table or column names.
---

# Discover Sanko data before querying

The Sanko data warehouse evolves continuously: tables, columns and schemas are added or renamed. Never rely on remembered or guessed names. Discover the current structure every task.

## Connection

The database connection is provided by the environment of `run_python`:

- `SANKO_DB_HOST`, `SANKO_DB_PORT`, `SANKO_DB_USER`, `SANKO_DB_PASSWORD`
- `SANKO_DB_NAME` (your writable schema; `SANKO_DB_WORK_SCHEMA` is the same schema)

Connect with `database=os.environ['SANKO_DB_NAME']`. Company data lives in read-only schemas named `sanko_<area>_ref` (for example `sanko_accounting_ref`); find the ones you can read with `SHOW DATABASES` and always qualify tables as `schema.table`. There is no environment variable for them.

Never print, log or save the password or the full environment. To check that a variable exists, test `'NAME' in os.environ`. If a connection or query fails, read the error and fix your script; report a missing connection only after that check shows it is really missing.

## Discovery steps

0. **Start from the data profile.** The task may include a block "Data this account can read": the tables you are granted, with row counts, size and date ranges, refreshed monthly. Use it to judge how big the task is and which tables to look at first, then confirm the live structure below; it can be up to a month old.
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
