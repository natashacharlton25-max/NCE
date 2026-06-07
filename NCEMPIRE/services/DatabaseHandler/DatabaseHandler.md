# DatabaseHandler Module

> **Purpose:** Single async interface to the shared D1 database
> **Type:** Data Management / Services Module
> **Status:** Spec Complete
> **Version:** 1.0.0

---

## Overview

DatabaseHandler is the single interface through which every module reads from and
writes to the system's **one shared D1 database**. No module touches the D1
binding directly. It provides an async query interface, query-builder helpers,
atomic batches, FTS5 search, and forward-only migration coordination.

Platform target: **Cloudflare Workers / D1** (single shared database, tables
namespaced by domain). All operations are async.

---

## Spec Artifacts

| Artifact | Status |
|----------|--------|
| Pass0.md | Approved — KEEP |
| Subsystem.md | Approved (PASS 2) |
| Spec.md | Approved (PASS 3) |
| Error codes | DATABASE_ category in ERROR-CODES.md v1.9.0 (incl. 2 new) |

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

- Single shared D1 with `<domain>_<entity>` table namespacing (decision 2026-06-07,
  confirming STACK-PROPOSAL's "single shared D1, default for v1").
- **Does not silent-degrade** (unlike Logger): state-affecting failures surface to
  the caller so correctness is never silently lost.
- **Logger ↔ DatabaseHandler cycle** resolved acyclic-at-runtime: DatabaseHandler
  logs its OWN failures straight to Workers Logs, not back through Logger's D1
  path (prevents a D1-outage failure loop). Logger's dual-write still captures
  everything in Workers Logs.
- **Divergence from legacy notes:** connection pooling and file backup/restore are
  dropped (no D1 equivalent — D1 uses Wrangler migrations + Time Travel). Sync
  better-sqlite3 → async D1.

---

*Spec version: 1.0.0*
*Created: 2026-01-20*
*Updated: 2026-06-07 - Spec Complete: PASS 0/2/3 approved, D1 single-shared target, 2 new error codes, cycle resolved*
*Status: Spec Complete*
