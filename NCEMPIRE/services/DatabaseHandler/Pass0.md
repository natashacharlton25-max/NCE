# PASS 0 — Subsystem Viability

Subsystem: DatabaseHandler
Parent System: services

## One-Sentence Job
Provides the single, shared interface through which all modules read from and write to the system's D1 database — query execution, query-builder helpers, transactions/batches, FTS, and migration coordination.

## Distinct Responsibility?
Yes

## Overlap With Siblings?
None — DatabaseHandler owns the D1 access interface (the *how* of querying). FileManager owns repo/object access (R2/files); Librarian owns discovery/search across repos; Archivist owns validate-and-save workflow. Modules call DatabaseHandler for their own tables; they do not open D1 themselves.

## Stability Expectation
Stable

## Size Signal
<500 LOC

## Verdict
KEEP

**Status:** Approved

---

**Platform note (2026-06-07):** On the Workers/D1 target the responsibility is *narrower* than the original better-sqlite3 model — connection pooling and file backup/restore have no D1 equivalent and are dropped; queries become async; storage is a single shared D1 with namespaced tables (not per-module .db files). The module remains real and necessary (query builders, transactions/batch, FTS, migration coordination), so verdict is KEEP, not MERGE/DROP.
