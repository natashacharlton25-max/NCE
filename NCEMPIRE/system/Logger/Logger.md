# Logger Module

> **Purpose:** Structured logging with configurable levels and dual-write durability
> **Type:** System Services Module
> **Status:** Spec Complete
> **Version:** 1.0.0

---

## Overview

Logger is the single entry point for all structured logging. Every module emits
log entries through Logger; Logger persists them and makes them retrievable, and
consolidates the per-step entries of one job into a single ordered job log.

Platform target: **Cloudflare Workers** — entries are **dual-written** to
**Workers Logs** (always-on live stream) and **D1** (queryable store, single
`logs` table discriminated by `log_type`). Finalized job logs may be archived to
**R2**.

Two invariants:
- **Logging never breaks the caller** (silent-degrade — observability, not critical path).
- **Dual-write, no blind spot** (D1 failure still leaves the Workers Logs line).

Boundary: Logger **records what it is told to log**; it does not extract cost/token
data. CostTracker owns cost data and calls Logger, not the reverse.

---

## Spec Artifacts

| Artifact | Status |
|----------|--------|
| Pass0.md | Approved — KEEP |
| Subsystem.md | Approved (PASS 2) |
| Spec.md | Approved (PASS 3) |
| Error codes | `LOG_` category in ERROR-CODES.md v1.8.0 |

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

- Owns logging patterns for entire system (per Spec Writing Guidelines).
- Format: structured rows; live stream via Workers Logs.
- Levels: debug, info, warn, error, fatal.
- Divergence from legacy planning doc (DOCS - Preplanning/.../LOGGER.md): that doc
  had Logger reading outputPaths to extract cost/token data and writing to a local
  filesystem. Both are superseded — Logger does not extract cost data (CostTracker
  boundary), and storage is D1/Workers Logs/R2, not `/repos/logs/`.

---

*Spec version: 1.0.0*
*Created: 2026-01-19*
*Updated: 2026-06-07 - Spec Complete: PASS 0/2/3 approved, Workers target, dual-write, CostTracker boundary*
*Status: Spec Complete*
