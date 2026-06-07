# Logger — Specification

> **Status:** Draft Complete – Awaiting Review
> **Version:** 1.0.0 (PASS 3)
> **Platform target:** Cloudflare Workers — D1 + Workers Logs + R2
> **Pre-spec:** see Subsystem.md (PASS 2, approved) and Pass0.md (KEEP, approved)

## Overview

Logger is the single entry point for all structured logging in the system. Every
module emits log entries through Logger; Logger persists them and makes them
retrievable. It also consolidates the per-step entries of one job into a single
ordered job log, finalized when the job completes.

Two invariants govern all behaviour:

1. **Logging never breaks the caller.** A failure inside Logger (including a
   storage-dependency failure) must never propagate into the caller's critical
   path. Logging is observability, not critical path. *Job succeeds + log fails =
   acceptable. Job fails because log failed = unacceptable.* (Ruling 2026-06-07.)
2. **Dual-write, no blind spot.** Every entry is written to **Workers Logs**
   (the always-on live stream) AND **D1** (the queryable store). Workers Logs is
   best-effort live signal; D1 is the durable, queryable record. If the D1 write
   fails, the Workers Logs line still exists, so no entry is ever fully lost.
   (Ruling 2026-06-07.)

Boundary (ruling 2026-06-07): Logger **records what it is told to log**. It does
not read module outputs to extract cost/token data. CostTracker owns cost/token
data and *calls* Logger to log cost events — never the reverse.

## Data Model

Single D1 table, `logs`, discriminated by `log_type`. (Decision 2026-06-07: one
table + `log_type`, not separate system/job tables.)

| Field | Type | Required | Description |
|------|------|----------|-------------|
| `id` | TEXT (UUID) PK | Yes | Unique entry id (`crypto.randomUUID()`). |
| `log_type` | TEXT | Yes | `'system'` or `'job'`. |
| `level` | TEXT | Yes | `debug` \| `info` \| `warn` \| `error` \| `fatal`. |
| `timestamp` | TEXT (ISO8601) | Yes | When the entry was created. |
| `message` | TEXT | Yes | Human-readable message. |
| `job_id` | TEXT | No | Set when `log_type='job'`; NULL for system logs. |
| `chain_name` | TEXT | No | From context, when present. |
| `brand_id` | TEXT | No | From context, when present. |
| `step` | INTEGER | No | Step index, for job step entries. |
| `module` | TEXT | No | Emitting module (from context or call). |
| `function` | TEXT | No | Emitting function, when supplied. |
| `status` | TEXT | No | Step status (`success`/`failed`/…) for job steps. |
| `duration_ms` | INTEGER | No | Step/operation duration, when supplied. |
| `data` | TEXT (JSON) | No | Structured payload (post-redaction), serialized. |
| `alert` | INTEGER (0/1) | Yes | 1 if alert-worthy (warn and above), else 0. |
| `created_at` | TEXT (ISO8601) | Yes | DB insertion time (defaults to now). |

### DDL

```sql
CREATE TABLE logs (
    id           TEXT PRIMARY KEY,
    log_type     TEXT NOT NULL,            -- 'system' | 'job'
    level        TEXT NOT NULL,            -- debug|info|warn|error|fatal
    timestamp    TEXT NOT NULL,            -- ISO8601
    message      TEXT NOT NULL,
    job_id       TEXT,
    chain_name   TEXT,
    brand_id     TEXT,
    step         INTEGER,
    module       TEXT,
    function     TEXT,
    status       TEXT,
    duration_ms  INTEGER,
    data         TEXT,                     -- JSON string (redacted)
    alert        INTEGER NOT NULL DEFAULT 0,
    created_at   TEXT NOT NULL DEFAULT (strftime('%Y-%m-%dT%H:%M:%fZ','now'))
);

CREATE INDEX idx_logs_type      ON logs(log_type);
CREATE INDEX idx_logs_job       ON logs(job_id);
CREATE INDEX idx_logs_level     ON logs(level, timestamp);
CREATE INDEX idx_logs_alert     ON logs(alert) WHERE alert = 1;
```

A **job log** is not a stored row — it is the ordered set of `logs` rows for a
`job_id`, assembled on read (or on finalize). `finalizeJobLog` writes a summary
row (`log_type='job'`, `status` set, `data` carrying totals) and may trigger
archival of the job's rows to R2.

## Validation Rules

- `level` must be one of the five known levels → else `LOG_ENTRY_INVALID`.
- `message` must be a non-empty string → else `LOG_ENTRY_INVALID`.
- `log_type` must be `'system'` or `'job'`; `'job'` requires a `job_id` → else `LOG_ENTRY_INVALID`.
- `data`, after serialization, must be ≤ the configured row-size limit
  (config `maxDataBytes`) → over-limit is truncated with a `_truncated: true`
  marker (not rejected; logging must not fail the caller).
- Redaction runs on every entry before persistence (see Behaviour → Redaction).

## Behaviour

### Normal Flow
1. Caller invokes `log(level, message, data)` (or a level shortcut, or
   `addToJobLog`). Context (jobId/chainName/brandId/module) is merged in.
2. Logger builds the entry, assigns `id` and `timestamp`, sets `alert` (1 if
   level ≥ warn), and runs **redaction** over `message` + `data`.
3. **Dual-write:**
   a. Emit the formatted line to **Workers Logs** (always, best-effort).
   b. Insert the row into **D1** via DatabaseHandler.
4. Return a lightweight success result to the caller. (Logger does not block the
   caller on storage latency beyond the insert; failures degrade per below.)

### Job-log consolidation
- `setContext({jobId, chainName, brandId})` — context attached to later entries.
- `addToJobLog(jobId, stepInfo)` — Orchestrator calls this after each step;
  Logger writes a `log_type='job'` row with `step/module/function/status/
  duration_ms`. (It does **not** read the step's outputPath to mine cost/token
  data — that is CostTracker's job.)
- `finalizeJobLog(jobId, status)` — SuccessHandler/FailureHandler call this on
  completion; Logger writes a summary row and, if retention applies, hands the
  job's rows to Archivist for R2 archival.

### Redaction
Before persistence, Logger strips/masks forbidden content from `message` and
`data`. Forbidden (never persisted): API keys / secrets, passwords/tokens, full
prompt content, raw large model responses (only summary/metadata), PII. A
detected forbidden field is masked (`"[REDACTED]"`); if redaction cannot be
performed safely, the field is dropped and a `LOG_FORBIDDEN_FIELD` warning entry
is recorded. Redaction never rejects the whole entry.

### Edge Cases
- **D1 insert fails:** Workers Logs line already emitted (dual-write) → entry is
  not lost. Logger records the dependency failure as best-effort and returns
  success to the caller. Never throws. (`DATABASE_ERROR` is noted internally at
  effective level 0 — informational — because of dual-write.)
- **Workers Logs unavailable:** D1 write still proceeds; the reverse blind-spot
  protection.
- **Unknown jobId on addToJobLog:** entry still written as a job row; a
  `LOG_JOB_FINALIZED`/state note is attached only if the job was already
  finalized (append-after-finalize).
- **append after finalize:** rejected for consolidation purposes with
  `LOG_JOB_FINALIZED` (the late entry is still written as a standalone job row so
  signal is not lost, but it is not folded into the finalized summary).
- **Oversized `data`:** truncated, marked `_truncated: true`, never rejected.
- **Logger not initialized:** `MODULE_NOT_READY` (transient; caller-side this is
  swallowed — logging must not break the caller).

## Error Handling

| Code | Category | Level | Description |
|------|----------|-------|-------------|
| `LOG_ENTRY_INVALID` | Validation | 2 | Entry has unknown level, empty message, or job type without job_id. |
| `LOG_FORBIDDEN_FIELD` | Policy | 2 | A forbidden field (secret/PII/full-content) was detected; field redacted/dropped. |
| `LOG_JOB_FINALIZED` | State | 2 | Step appended to an already-finalized job log; written standalone, not folded into summary. |
| `DATABASE_ERROR` | Dependency | 3* | D1 insert/query failed. *Effective level 0 for Logger due to dual-write (entry preserved in Workers Logs). Reused from registry. |
| `MODULE_NOT_READY` | State | 1 | Logger used before initialize(). Reused from registry; caller-side swallowed. |

All Logger errors are handled internally under the silent-degrade invariant:
Logger logs its own failure best-effort and returns success to the caller. Logger
does **not** route to FailureHandler for its own faults (that would risk
re-entrancy); FailureHandler still *uses* Logger to record failures of other
modules.

## Retry / Escalation
- Logger does not retry D1 inserts on the caller's critical path (would add
  latency to every logged operation). Dual-write is the resilience mechanism, not
  retry.
- Logger never escalates its own faults to human/FailureHandler. Storage outages
  surface through normal platform observability (Workers Logs, D1 health), not
  through the job pipeline.

## Observability
- **Events:** `log.written` (per entry, debug-level meta), `job.log.finalized`
  (per job, with summary totals).
- **Logs:** Logger's own failures are emitted to Workers Logs only (avoids
  re-entrant D1 dependency).
- **Metrics:** entries written (by level, by log_type), D1 insert failure rate,
  redaction hits, job logs finalized, average finalize duration.

## Versioning
- Compatibility guarantees: the `logs` table shape and the public function
  signatures (`log`/`debug`/`info`/`warn`/`error`/`fatal`/`setContext`/
  `addToJobLog`/`finalizeJobLog`/`getJobLog`/`getRecent`) are the v1 contract.
  Additive columns/indexes are non-breaking; removing/renaming columns or
  changing `log_type` values is breaking.
- Migration notes: forward-only D1 migrations (per platform). Retention/TTL and
  R2 archival key scheme are configuration, not schema, and may evolve without a
  contract bump.

---
### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Are there any corrections or refinements required?
- May I proceed (register the LOG_ codes + update [Module].md status), or revise this first?
---
