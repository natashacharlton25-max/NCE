# DatabaseHandler

## Purpose
DatabaseHandler is the single interface through which every module reads from and writes to the system's D1 database. It centralizes query execution, query-builder helpers, transactions/batches, full-text search, and migration coordination so no module talks to the D1 binding directly.

## Role Within System
The `services/` domain provides cross-cutting capabilities every system relies on. Durable state is one such capability: jobs, logs, costs, themes, audit records, and tenant config all live in D1. DatabaseHandler owns the *how* of persistence — a consistent, async query interface over a single shared D1 — so each module deals in its own tables and queries, not in D1 binding mechanics.

## Owns
- The async query interface over the D1 binding (execute, query-one, query-many, run).
- Query-builder helpers for common patterns (CRUD, list/paginate, upsert).
- Transaction and batch coordination (D1 `batch()` for atomic multi-statement work).
- Full-text search access (SQLite FTS5 via D1).
- Migration coordination (applying forward-only schema migrations).
- The single-shared-D1 namespacing convention (table naming so domains coexist in one database). [Decision 2026-06-07]
- Result mapping (D1 rows → typed objects).

## Does NOT Own
- Repo/object/file access — FileManager owns R2/object access; DatabaseHandler is D1 only.
- Discovery/search across repos — Librarian owns cross-repo search/recommend; DatabaseHandler executes queries it's given, it does not decide what to look for.
- Validate-and-save workflow — Archivist owns schema-validated saves; DatabaseHandler is the storage primitive beneath it.
- Schema *definition* — each module owns its own table DDL (in its Spec.md); DatabaseHandler applies/runs migrations, it does not author schemas.
- Caching — CacheHandler owns KV-backed caching; DatabaseHandler always hits D1.
- Connection pooling / file backup-restore — no D1 equivalent; dropped (see Notes).

## Inputs
- Source: Any module needing durable state.
  Description: A query (SQL + bound params) or a query-builder request (table, fields, filters), plus optional transaction/batch grouping.
- Source: MigrationHandler / system startup.
  Description: Migration sets to apply (forward-only).

## Outputs
- Destination: Cloudflare D1 (single shared database).
  Description: Executed statements (reads return rows; writes return affected-row metadata).
- Destination: Calling module.
  Description: Mapped result rows / run metadata, or a structured error.

## Dependencies
### System Dependencies
- MigrationHandler (caller/coordination) — supplies migrations to apply; listed for traceability.
- (Logger calls DatabaseHandler, not the reverse — Logger is a consumer.)

### External / Library Dependencies
- Cloudflare D1 binding (the database itself; accessed via the Workers runtime `env`).
- SQLite FTS5 (built into D1).

## Error Categories
- Validation — malformed query / missing bind params / unknown table or column.
- Policy — attempt to run a statement outside the module's allowed namespace (if enforced).
- Dependency — D1 unavailable, statement execution failure, migration failure.
- State — transaction/batch in an invalid state; migration version conflict.
- Unknown — uncategorized internal failure.

## Test Intent
- Happy path: execute parameterized read and write; build+run a CRUD/list query; run a multi-statement batch atomically; run an FTS query; apply a migration.
- Failure path: D1 execution error surfaces as DATABASE_ERROR; failed migration rolls back / halts; malformed query rejected with validation error.
- Boundary conditions: empty result set; large result pagination; batch where one statement fails (atomicity); concurrent writes to the same table; binding the maximum number of params.

## Notes
- Workers/D1 target confirmed 2026-06-07. Translate the planning doc's *intent* (centralized, safe, reusable DB access), not its better-sqlite3 *mechanism*.
- **Dropped vs the old model** (no D1 equivalent): connection pooling (D1 is a binding, not pooled connections); file-based backup/restore before migrations (D1 uses Wrangler migrations + Time Travel). Recorded as superseded so the divergence from the old DatabaseHandler notes is explicit.
- **Single shared D1** (not per-module themes.db/jobs.db/cache.db). Table namespacing is the convention; concrete naming scheme + the async method signatures + FTS5 helpers belong in Spec.md (PASS 3).
- All queries are **async** (D1), unlike the synchronous better-sqlite3 assumption throughout the legacy docs.
