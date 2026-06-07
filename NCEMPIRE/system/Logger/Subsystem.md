# Logger

## Purpose
Logger is the single point through which all modules emit structured log entries. It stores them durably and makes them retrievable. It also consolidates the per-step entries of a single job into one coherent job log.

## Role Within System
The `system/` domain provides mechanical primitives every other system relies on. Logging is one such primitive: every module, manager, and worker emits operational signal (milestones, warnings, failures, timings) that must be captured consistently and queried later for debugging, audit support, and observability. Logger owns the *how* of recording that signal so no other module has to.

## Owns
- The structured log-entry contract (shape of a log record: level, timestamp, message, context, data).
- Log levels and their semantics (debug, info, warn, error, fatal).
- Job-log consolidation: assembling per-step entries for one `jobId` into a single ordered job log, and finalizing it on job completion.
- Context propagation: a context (e.g. jobId, chainName, brandId) set once and attached to subsequent entries.
- Retrieval/filtering of stored log entries (by job, by level, by time window).
- The redaction rule: what must never be persisted (secrets, full prompt content, raw large responses, PII).

## Does NOT Own
- Cost or token data — CostTracker owns calculation and the token/cost tables; CostTracker *calls* Logger to log a cost event. Logger does not read outputs to extract cost/token figures. (Per boundary ruling 2026-06-07.)
- Recovery decisions — FailureHandler decides retry/escalation; Logger only records that a failure occurred.
- The durable write/storage mechanism itself — persistence is delegated (see Dependencies). Logger defines *what* and *when* to record, not the storage engine internals.
- Chain execution — Orchestrator runs steps; Logger receives their completions.
- Alerting/notification delivery — Logger may mark an entry as alert-worthy (warn+); Notification delivers.

## Inputs
- Source: Any module via the logging interface (`log`/`debug`/`info`/`warn`/`error`/`fatal`).
  Description: A log entry — level, message, and optional structured `data`.
- Source: Orchestrator, after each chain step.
  Description: Step completion info (jobId, step index, module, function, status, timings) to append to the job log.
- Source: SuccessHandler / FailureHandler, on job completion.
  Description: Final job status to finalize and close the job log.
- Source: Any caller setting context.
  Description: A context object (jobId, chainName, brandId, …) attached to subsequent entries.

## Outputs
- Destination: D1 (system log table + job-log/step records). [Workers target — see ruling 2026-06-07]
  Description: Durable structured log rows for system-wide and per-job logging; the primary queryable store.
- Destination: Workers Logs (platform observability stream).
  Description: Real-time log lines for live monitoring / `wrangler tail`-style inspection.
- Destination: R2 (archive), when retention promotes a finalized job log out of D1.
  Description: Long-term retained job logs as objects, written under namespaced keys.
- Destination: Callers of retrieval functions.
  Description: A job log, or a filtered set of recent entries.

## Dependencies
### System Dependencies
- DatabaseHandler — durable insert/query of log rows in D1 (replaces the planning doc's FileManager/Writer disk model).
- Archivist — promotion of finalized job logs to R2 archive (when retention applies).
- Orchestrator (caller, not dependency-of) — supplies step completions; listed here for traceability only.

### External / Library Dependencies
- Cloudflare D1 binding (via DatabaseHandler).
- Cloudflare Workers Logs (platform-native; no library).
- Cloudflare R2 binding (via Archivist) — archive only.

## Error Categories
- Validation — malformed log entry (missing level/message, unknown level, oversized payload).
- Policy — attempt to log a forbidden field (secret/PII/full-content); entry is redacted or rejected per policy.
- Dependency — D1/R2 unavailable or insert failure; logging itself must degrade safely (never throw into the caller's critical path).
- State — job log referenced for an unknown/already-finalized jobId; step appended after finalize.
- Unknown — uncategorized internal failure.

## Test Intent
- Happy path: emit entries at each level; append steps to a job log; finalize; retrieve the consolidated log and recent-filtered entries.
- Failure path: D1 insert fails → Logger degrades without breaking the caller; forbidden-field redaction triggers; append-after-finalize is rejected with State error.
- Boundary conditions: oversized `data` payload at the row limit; unknown jobId; unknown level; empty/null message; concurrent appends to the same jobId.

## Notes
- Workers target confirmed 2026-06-07: translate the planning doc's *intent* (durable logging, level routing, job consolidation, redaction) — not its filesystem *mechanism* (`/repos/logs/`, file rotation, `maxFileSize`). FS → D1 rows; rotation → row limits/TTL; archive → R2.
- A core invariant: **logging must never break the thing being logged.** Dependency failures degrade silently (best-effort) rather than propagating.
- Concrete D1 DDL, level→output routing table, redaction field list, and retention/TTL values belong in Spec.md (PASS 3), not here.
