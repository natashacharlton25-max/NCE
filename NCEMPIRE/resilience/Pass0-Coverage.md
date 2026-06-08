# System Coverage Review — resilience

## System Role Summary
The authoritative owner of all failure-handling **decisions** in NCEMPIRE — classification, retry, fallback, salvage, recovery-state construction, resume, escalation, human handoff, and failure audit. Per CLAUDE.md §4, failure decisions belong here; `system/FailureHandler` owns only mechanical error plumbing.

## Coverage Assessment

### Covered Responsibilities
| Responsibility | Covered By |
|----------------|------------|
| Classify failures (transient / permanent / policy / dependency) | FailureClassifier |
| Decide retry policy (count, backoff, jitter, cap) | RetryPolicyEngine |
| Resolve fallback strategy per failure class | FallbackStrategyResolver |
| Salvage usable partial output from a failed run | SalvageCoordinator |
| Reconstruct resumable state from interrupted workflows | RecoveryStateBuilder |
| Manage resume points / checkpoints | ResumePointManager |
| Produce human-readable failure explanations + options | FailureNarrator |
| Route failures by severity to handler/human | EscalationRouter |
| Pause + hold workflows awaiting human input | HumanHandoffManager |
| Record failures for audit | FailureAuditRecorder |

### Partially Covered Responsibilities
- **Provider/integration circuit-breaking (health over time):** FallbackStrategyResolver decides *what to fall back to*, but marking a provider unhealthy (and for how long) is not unambiguously owned here vs `ai/ModelManager`'s fallback chains.
- **Timeout / deadline detection:** resilience decides what to do *after* a failure; detecting "this exceeded its deadline" is shared with `orchestration/StatusHandler`.

### Uncovered Responsibilities
- None that imply a missing subsystem. The items above are ownership/boundary questions, not absent functions.

## Subsystem Sufficiency Review
| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| FailureClassifier | Sufficient | Single clear job: map raw failures → categories that drive downstream policy | 400–650 | Low |
| RetryPolicyEngine | Sufficient | Pure decision logic (backoff/jitter/cap) | 300–500 | Low |
| FallbackStrategyResolver | Sufficient | Chooses fallback path per failure class (boundary w/ ai/ — see below) | 350–550 | Low |
| SalvageCoordinator | Sufficient | Recovers usable partial output; absorbs old content/ContentSalvageModule | 400–650 | Low |
| RecoveryStateBuilder | Sufficient | Reconstructs resumable state from interrupted work | 450–700 | Low |
| ResumePointManager | Sufficient | Checkpoint/resume bookkeeping | 300–500 | Low |
| FailureNarrator | Sufficient | Failure → human-readable explanation (absorbs old recovery/FailureNarrator) | 300–450 | Low |
| EscalationRouter | Sufficient | Severity → handler/human routing | 300–450 | Low |
| HumanHandoffManager | Sufficient | Pauses workflow, holds state until human input (boundary w/ review/) | 400–600 | Low |
| FailureAuditRecorder | Sufficient | Append-only failure records (boundary w/ audit/) | 300–500 | Low |

## Missing Capability Areas
| Capability | Impact | Est. LOC | Notes |
|-----------|--------|----------|------|
| Circuit-breaker state (provider/integration health window) | Medium | 150–250 | No unambiguous owner between FallbackStrategyResolver and ai/ModelManager |
| Dead-letter / poison-work quarantine (queue-driven stages on Workers) | Low–Medium | 100–200 | No named owner for "give up and quarantine" |

## Boundary & Classification Issues
- **resilience/FailureClassifier ↔ system/FailureHandler** — FileTree marks `system/FailureHandler` "mechanical only." The decide-here / execute-there split must be explicit or the two overlap.
- **resilience/FailureAuditRecorder ↔ audit/** — must settle whether it writes to audit's tables/schema or its own store; failure-record authority should sit with one system.
- **resilience/SalvageCoordinator ↔ content/** — salvaging partial *content* could read as a content/ concern (old model had content/ContentSalvageModule).
- **resilience/HumanHandoffManager ↔ review/ + orchestration/Notification** — "pause for a human" overlaps review/ReviewQueue (approval queue) and Notification (alerting). Trigger vs queue vs notify ownership to be drawn.
- **resilience/FallbackStrategyResolver ↔ ai/ModelManager** — AI provider fallback chains already live in ai/; risk of two fallback authorities.

## Overall Build Size Estimate
- **Total estimated LOC:** ~3,500–5,550
- **Largest expected file:** RecoveryStateBuilder (~700 LOC)
- **No files expected to exceed 1500 LOC.**

## Risk Assessment
- **Risk Level:** Low–Medium
- **Rationale:** Subsystem decomposition is clean and complete for the stated role; risk is neither size nor coverage. It is the five cross-system boundaries (system/, audit/, content/, review/, ai/) — as a *global constraint* system, those lines are load-bearing and, if left unspecified, would let failure logic leak.

## Verdict
**CAN** fulfil role as currently structured. All 10 subsystems are sufficient and appropriately scoped, with no file-size risk. The CAN is conditional on drawing the flagged cross-system boundaries (failure-decision authority vs system/FailureHandler; failure records vs audit/; salvage vs content/; handoff vs review/; fallback vs ai/) at Pass 1 — these are ownership lines, not missing capabilities.

**Status:** Draft Complete – Awaiting Review
