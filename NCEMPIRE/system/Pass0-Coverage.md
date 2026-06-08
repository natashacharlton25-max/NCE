# System Coverage Review — system

## System Role Summary
`system` is the core mechanical infrastructure layer: it writes, logs, parses, archives, scaffolds paths, cleans up, and executes failure plumbing for the whole platform. It owns *mechanics of persistence and bookkeeping* — it does not own *decisions* (failure, brand, retention policy), which live in `resilience/`, `brand/`, and the systems that call it.

## Coverage Assessment

### Covered Responsibilities

| Responsibility | Covered By |
|----------------|------------|
| Central structured logging (temp + D1/Workers Logs, job-log consolidation, redaction) | Logger (Spec Complete, PASS 0/2/3 approved, Workers-native) |
| Durable data writes (atomic write, append, move/copy/remove, path safety, locking) | Writer (full content spec in Writer.md, but local-FS framed — see Boundary Issues) |
| Parsing of JSON / CSV / Markdown / AI responses; field extraction; schema validation | Parsers (intent stated; placeholder only) |
| Archival / versioning / retention / recovery of stored data | Archivist (intent stated; placeholder only) |
| Path / folder / ID construction and scaffolding | Builders (intent MISMATCHED — see Boundary Issues; placeholder only) |
| Cleanup of temp files / stale exports / retention enforcement | GarbageCollector (intent stated; placeholder only) |
| Mechanical error handling (retries / backoff / fallbacks / escalation plumbing) | FailureHandler (intent OVERSTATED toward decisions — see Boundary Issues; placeholder only) |

### Partially Covered Responsibilities
- **Writer** — Behaviour is richly specified, but the spec is written against a POSIX local filesystem (atomic `rename`, `fsync`, advisory `.lock` files, `realpath` symlink resolution, disk-space preflight, `allowedRoots: /repos/ /jobs/ /exports/ /logs/`). On Cloudflare Workers there is no local FS; durable writes map to **R2 / D1 / KV**. The *responsibility* (single durable writer) is covered; the *mechanism described* is not yet valid for the target runtime. Treated as partial pending a Workers-target reconciliation pass.
- **FailureHandler** — The mechanical responsibility (execute retries/backoff/fallbacks/escalation) is named, but the current text claims it *decides* recovery strategies and *owns retry/backoff logic for the entire system*. Decision ownership belongs to `resilience/`; only the plumbing belongs here. Responsibility is partially covered and partially mis-scoped.
- **Archivist** — Intent present (archive/version/retention/recovery) but no spec. Boundary with `versioning/` and with R2 archival (Logger already hands job-log rows to Archivist for R2) is undefined.
- **Parsers / GarbageCollector** — Intent stated, no spec; no contract for inputs/outputs, error codes, or retention-policy source.

### Uncovered Responsibilities
- **Runtime-correct durable-write mechanics** — No subsystem yet specifies how writes/locks/atomicity work on Workers (R2 conditional puts, D1 transactions, no fsync/rename). This is a genuine gap, not just a placeholder.
- **Retention-policy source of truth** — GarbageCollector and Archivist both "apply retention policies," but where those policies are *decided* (here vs `resilience/` vs config) is unspecified.
- **CSV parsing** — Intent lists JSON/CSV/Markdown/AI; Parsers.md only mentions AI-response field extraction. CSV/Markdown coverage unconfirmed.

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| Logger | Sufficient | Spec Complete, PASS 0/2/3 approved; Workers-native dual-write; clear boundaries (records only, does not decide). Treat as built. | ~400–500 | Low |
| Writer | Sufficient-in-intent / Needs-runtime-reconcile | Detailed approved spec but local-FS/POSIX assumptions invalid on Workers; must be re-grounded to R2/D1/KV before it is truly sufficient. Single-writer role itself is sound. | ~600–900 | Medium |
| Archivist | Insufficient | Placeholder; archive/version/retention/recovery undefined; boundary with `versioning/` and R2 unspecified. | ~500–800 | Medium |
| Builders | Insufficient + Misclassified | Placeholder, and its doc describes content-creation orchestration (documents/emails/social, calls TemplateEngine/renderers), NOT path/folder/scaffold construction. Identity conflict. | ~300–600 (as path-builder) | Low–Medium |
| FailureHandler | Insufficient + Boundary-conflicted | Placeholder; doc claims it *decides* recovery and *owns retry/backoff for the entire system* — contradicts mechanical-only mandate and `resilience/` decision ownership. | ~400–700 (mechanical only) | Medium |
| GarbageCollector | Insufficient | Placeholder; cleanup intent clear but retention-policy source and Workers (R2/KV/D1 TTL) cleanup mechanics undefined. | ~250–450 | Low |
| Parsers | Insufficient | Placeholder; only AI-response extraction named; JSON/CSV/Markdown coverage and error contract undefined. | ~400–700 | Low–Medium |

## Missing Capability Areas
- **Workers-runtime write/lock/atomicity model** — no subsystem currently defines durable-write semantics for R2/D1 (the Writer spec assumes a local FS that does not exist on the target). High-impact gap.
- **Single retention-policy authority** — GarbageCollector + Archivist both enforce retention with no defined decision source.
- **Parser scope completeness** — CSV and Markdown parsing implied by intent but not represented.

## Boundary & Classification Issues
- **FailureHandler ↔ resilience/ (THE key boundary).** Global rule: failure *decisions* belong to `resilience/`; `system/FailureHandler` is MECHANICAL ONLY (executes plumbing). The current `FailureHandler.md` violates this — it states "decide recovery strategies," "Owns retry/backoff logic for entire system," and lists decision concerns (failure classification, retry policy selection, fallback-chain definition, graceful-degradation policy, user-notification policy). These are `resilience/` decisions. FailureHandler should *execute* the strategy resilience selects, not author it. **Mechanical-vs-decision boundary is currently breached and must be corrected before spec.** (Note: Logger's approved Pass0 also phrases this as "FailureHandler decides recovery" — that referrer carries the same overstatement and should be reconciled in the same pass.)
- **Builders identity conflict.** Target intent = path/folder/ID construction and scaffolding. `Builders.md` instead describes high-level document/email/social content builders that orchestrate TemplateEngine and renderers. That is content-creation orchestration, not mechanical scaffolding, and would pull logic/decisions into `system`. Either the doc is mislabeled or the wrong module landed under `system`. Flag for the owner; do not silently rename or re-scope.
- **Writer ↔ Workers runtime (local-FS assumption).** Writer assumes POSIX semantics (atomic rename, fsync, advisory lock files, realpath, disk preflight, `/repos/`-style allowed roots). On Workers these map to R2/D1/KV with different atomicity and no local FS. Flagged per brief: this subsystem's role currently assumes a local filesystem.
- **Writer ↔ services/FileManager (write/read split).** Writer is the sole writer; reads belong to the services read path. Writer.md's "Used By" list and `validatePath` are consistent with a write-only role, but the read side (services/FileManager) is referenced only implicitly. Boundary is plausible but unconfirmed.
- **Archivist ↔ versioning/.** Both touch versioning. `system/Archivist` = archival/retention/recovery mechanics; `versioning/` = version *management* (semantics of versions). Undefined split; Logger already depends on Archivist for R2 job-log archival, so this must be pinned.
- **Index ↔ Writer status discrepancy.** `Index.md` marks Writer "Spec Complete" and notes a legacy-checklist "Approved" vs doc "Spec Complete" discrepancy. Separately, `Writer/Spec.md` is still the *empty template* — the real content lives in `Writer/Writer.md`. The canonical-spec location for Writer is itself ambiguous. Documentary inconsistency to reconcile (not resolved here).

## Overall Build Size Estimate
Roughly **2,850–4,650 LOC** across the 7 subsystems (TS, excluding tests/types), dominated by Writer, Archivist, FailureHandler, and Parsers. No single file is projected above ~1,500 LOC on current intent, so no High size-risk file is identified — *provided* Writer is not expanded to absorb both local-FS and Workers paths in one module (which would push it toward the threshold). Estimates are conservative and will move once the placeholders are specced.

## Risk Assessment
- **Overall risk: MEDIUM-HIGH.** Logger is solid and built; the other six are placeholders or runtime-mismatched.
- **Highest risks:** (1) FailureHandler mechanical-vs-decision boundary breach — a classification error that, if specced as-is, duplicates `resilience/` authority; (2) Writer's local-FS model being invalid on the Workers target; (3) Builders identity mismatch importing content-orchestration logic into `system`.
- **Secondary:** undefined Archivist↔versioning split, no single retention-policy authority, Parsers scope completeness, and the Writer canonical-spec/status discrepancy.
- No High file-size risk identified at this stage.

## Verdict
**CANNOT** fulfil its role *as currently structured* — pending conditions:
1. **Reclassify FailureHandler as mechanical-only**; move recovery-strategy *decisions* (classification, retry policy, fallback-chain, degradation, notification) to `resilience/`. Reconcile the Logger Pass0 referrer that says FailureHandler "decides."
2. **Resolve the Builders identity conflict** — confirm whether `system/Builders` is path/scaffold construction (target intent) or the content-builder module currently documented; do not merge the two roles into `system`.
3. **Re-ground Writer (and GC/Archivist cleanup mechanics) to the Workers runtime** (R2/D1/KV), replacing POSIX rename/fsync/lock-file/disk-preflight assumptions.
4. **Define Archivist↔versioning/ split** and a single retention-policy authority shared by Archivist + GarbageCollector.
5. **Reconcile the Writer canonical-spec/status discrepancy** (empty `Spec.md` vs populated `Writer.md`; Index "Spec Complete" vs legacy "Approved").

Logger (built/sufficient) and Writer's *single-writer role* (sound) are the green elements. With the five conditions above resolved, the 7-subsystem set is structurally adequate to cover the system's intent — no missing subsystem and no required new subsystem identified.

**Status:** Draft Complete – Awaiting Review

---
### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Are there any corrections or refinements required?
- May I proceed, or should I revise this first?
---
