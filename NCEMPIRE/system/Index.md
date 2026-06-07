# system — Index

## Subsystems

| Subsystem | Responsibility | Status | Spec | Tests | Notes |
|----------|----------------|--------|------|-------|-------|
| Logger | Structured logging; dual-write to Workers Logs + D1; job-log consolidation | Spec Complete | Spec.md (v1.0.0) | — | PASS 0/2/3 approved 2026-06-07. Workers target. LOG_ codes in ERROR-CODES v1.8.0. |
| Writer | Durable write operations (atomic writes, paths, locking) | Spec Complete | Writer.md | — | Legacy checklist marked "Approved"; own doc says "Spec Complete" — discrepancy unreconciled (see notes). |
| FailureHandler | Failure classification + recovery routing (mechanical only) | Placeholder | — | — | Marked "mechanical only" in FileTree. |
| Parsers | Parsing (JSON, AI responses, schema validation, extraction) | Placeholder | — | — | |
| Archivist | Validate-and-save / archival workflow | Placeholder | — | — | |
| Builders | Path/ID/scaffold builders (document/email/social) | Placeholder | — | — | |
| GarbageCollector | Cleanup of temp/expired data | Placeholder | — | — | |

## External Dependencies

| System | Reason |
|-------|--------|
| services/DatabaseHandler | Logger D1 inserts/queries; (other system modules as specced) |
| (R2 binding via Archivist) | Logger job-log archival |

## Feeds

### Feeds In
- Log entries from all modules (→ Logger)
- Step completions from Orchestrator (→ Logger job logs)

### Feeds Out
- Durable log rows (D1), live log stream (Workers Logs), archived job logs (R2)

## Error Domains
- LOG_ (Logger) — see ERROR-CODES.md v1.8.0
- (Writer/Parsers/etc. error domains to be recorded as those specs are completed)

## Index Notes
- This index is the co-located tracker for the `system/` domain. (Canonical-tracker
  question — this vs legacy DOCS - StructureDefined/reference/MODULE-SPEC-CHECKLIST.md —
  is deferred per user 2026-06-07; legacy checklist left untouched.)
- Only Logger filled this pass. Writer status carried from its own doc; the other
  5 subsystems remain Placeholder and are not yet through PASS 0.
- Discrepancy to reconcile later: legacy checklist says Writer "Approved"; Writer.md
  says "Spec Complete". Not resolved here.
