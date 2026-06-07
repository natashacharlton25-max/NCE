# DatabaseHandler — Specification

> **Status:** Draft Complete – Awaiting Review
> **Version:** 1.0.0 (PASS 3)
> **Platform target:** Cloudflare Workers — single shared D1
> **Pre-spec:** see Subsystem.md (PASS 2, approved) and Pass0.md (KEEP, approved)

## Overview

DatabaseHandler is the single async interface to the system's **one shared D1
database**. Every module that needs durable state goes through it; no module
touches the D1 binding directly. It provides query execution, query-builder
helpers, atomic batches/transactions, full-text search, and migration
coordination, with consistent result mapping and error reporting.

Platform facts that shape everything below (per ruling 2026-06-07):
- **D1 is async** — every operation returns a Promise (unlike the legacy
  synchronous better-sqlite3 model).
- **Single shared D1** — one database, tables namespaced by domain; not
  per-module `.db` files.
- **No connection pooling / no file backup** — D1 is a runtime binding with its
  own migration + Time Travel model; those legacy responsibilities are dropped.

## Data Model

DatabaseHandler does not own any tables of its own except the migration ledger it
uses to track applied migrations. (Each module owns its own table DDL in its own
Spec.md; DatabaseHandler *applies* them.)

| Field | Type | Required | Description |
|------|------|----------|-------------|
| `id` | INTEGER PK AUTOINCREMENT | Yes | Ledger row id. |
| `migration_name` | TEXT | Yes | Unique migration identifier (forward-only). |
| `applied_at` | TEXT (ISO8601) | Yes | When the migration was applied. |
| `checksum` | TEXT | No | Hash of the migration body, to detect drift. |

### DDL (migration ledger)

```sql
CREATE TABLE IF NOT EXISTS _migrations (
    id             INTEGER PRIMARY KEY AUTOINCREMENT,
    migration_name TEXT NOT NULL UNIQUE,
    applied_at     TEXT NOT NULL DEFAULT (strftime('%Y-%m-%dT%H:%M:%fZ','now')),
    checksum       TEXT
);
```

### Table namespacing convention
Single shared D1; tables are named to keep domains distinct. Convention:
`<domain>_<entity>` (e.g. `logs`, `jobs`, `cost_records`, `theme_themes`). The
authoritative per-table DDL lives in each owning module's Spec.md; this convention
is the rule those specs follow.

## Validation Rules

- Every statement must be parameterized — raw string interpolation of caller
  values is rejected (`DATABASE_QUERY_INVALID`). Bind params only.
- Query-builder requests must reference a known table and columns → else
  `DATABASE_QUERY_INVALID`.
- A batch must contain ≥1 statement; an empty batch is a no-op (not an error).
- Migration names must be unique and applied in order; re-applying an applied
  migration is skipped (idempotent), not an error.

## Behaviour

### Public interface (async)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `queryOne(sql, params)` | sql, params[] | row \| null | First matching row, or null. |
| `queryMany(sql, params)` | sql, params[] | row[] | All matching rows. |
| `run(sql, params)` | sql, params[] | { changes, lastRowId } | Execute a write; returns run metadata. |
| `batch(statements)` | {sql,params}[] | results[] | Atomic multi-statement execution (D1 `batch()`). |
| `build(spec)` | builder spec | { sql, params } | Compose a parameterized query from a CRUD/list/upsert spec. |
| `search(table, query, opts)` | table, query, opts | row[] | FTS5 full-text search over an FTS-enabled table. |
| `migrate(migrations)` | migration[] | { applied[], skipped[] } | Apply forward-only migrations, recording each in `_migrations`. |
| `paginate(sql, params, page)` | sql, params, page | { rows, page, hasMore } | List with limit/offset paging. |

### Normal Flow (read)
1. Caller invokes `queryOne`/`queryMany`/`search` with SQL + bound params (or a
   builder spec).
2. DatabaseHandler validates the statement is parameterized and references known
   structure.
3. It executes via the D1 binding (`prepare().bind().all()/first()`), awaits.
4. It maps the D1 result into plain row objects and returns them.

### Normal Flow (write / batch)
1. Caller invokes `run` (single) or `batch` (atomic group).
2. For `batch`, all statements are submitted via D1 `batch()` — **atomic**: all
   succeed or all roll back.
3. Run metadata (`changes`, `lastRowId`) is returned.

### Migration Flow
1. `migrate(migrations)` reads the `_migrations` ledger.
2. For each migration not yet applied (in order): execute it, then record it in
   `_migrations` (with checksum).
3. If a migration fails, halt — do not record it; surface
   `DATABASE_MIGRATION_FAILED`. Already-applied migrations are skipped.

### Edge Cases
- **D1 execution failure:** surfaced as `DATABASE_ERROR` (level 3). Unlike
  Logger, DatabaseHandler does NOT silent-degrade — a failed write must be
  visible to the caller, because state correctness depends on it.
- **Batch partial failure:** D1 `batch()` is atomic; a failure rolls the whole
  batch back. Caller receives `DATABASE_ERROR`; no partial writes.
- **Empty result:** `queryOne` → null; `queryMany`/`search` → `[]`. Not errors.
- **Migration already applied:** skipped, reported in `skipped[]`.
- **Checksum mismatch on an applied migration:** `DATABASE_MIGRATION_CONFLICT`
  (a previously-applied migration's body changed — drift; halt for review).
- **Unparameterized statement:** rejected before execution (`DATABASE_QUERY_INVALID`).
- **Used before init:** `MODULE_NOT_READY`.

## Error Handling

| Code | Category | Level | Description |
|------|----------|-------|-------------|
| `DATABASE_ERROR` | Dependency | 3 | D1 statement execution failed (read/write/batch). *(reused from registry)* |
| `DATABASE_INIT_FAILED` | Dependency | 4 | Could not initialize/access the D1 binding. *(reused)* |
| `DATABASE_MIGRATION_FAILED` | Dependency | 4 | A migration failed to apply. *(reused)* |
| `DATABASE_QUERY_INVALID` | Validation | 2 | Unparameterized statement, or unknown table/column in builder. *(NEW — pending registry)* |
| `DATABASE_MIGRATION_CONFLICT` | State | 4 | Applied migration's checksum changed (drift). *(NEW — pending registry)* |
| `MODULE_NOT_READY` | State | 1 | Used before initialize(). *(reused)* |

Unlike Logger, DatabaseHandler **does not** silent-degrade: state-affecting
failures are returned to the caller so FailureHandler / the caller can decide
(retry, escalate). Reads/writes are not swallowed.

## Retry / Escalation
- DatabaseHandler does not itself retry — it surfaces `DATABASE_ERROR` (level 3)
  and lets FailureHandler apply the registry's strategy. (Transient D1 blips are a
  candidate for FailureHandler retry config, not in-module retry.)
- Migration failures (level 4) halt and escalate; no auto-recovery.

## Observability
- **Events:** `db.migration.applied` (per migration), `db.batch.executed`.
- **Logs:** execution failures logged via Logger (DatabaseHandler is a Logger
  consumer; Logger is a DatabaseHandler consumer — see note).
- **Metrics:** query count (by type), execution failure rate, batch size,
  migration apply time, slow-query count.

### Circular-dependency note (Logger ↔ DatabaseHandler)
Logger dual-writes to D1 *via* DatabaseHandler; DatabaseHandler logs its own
failures *via* Logger. To avoid re-entrancy: DatabaseHandler emits its own
failures to **Workers Logs directly** (not back through Logger's D1 path),
mirroring Logger's silent-degrade discipline. This keeps the dependency acyclic
at runtime.

## Versioning
- Compatibility guarantees: the public async interface (queryOne/queryMany/run/
  batch/build/search/migrate/paginate) and the `_migrations` ledger shape are the
  v1 contract. Additive helpers are non-breaking.
- Migration notes: forward-only. The single-shared-D1 + namespacing convention is
  architectural; changing it (e.g. to D1-per-domain) is a breaking change
  requiring a contract bump and data migration.

---
### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Approve the 2 NEW error codes (`DATABASE_QUERY_INVALID` L2, `DATABASE_MIGRATION_CONFLICT` L4) for the registry?
- Is the Logger↔DatabaseHandler acyclic-at-runtime resolution acceptable?
- May I proceed, or revise first?
---
