# resilience

## Purpose
The authoritative owner of failure handling for NCEMPIRE. When any step fails, resilience decides how the system recovers — classify → choose strategy → reconstruct recoverable state → escalate / hand off when automation is exhausted → record — and **orchestrates** that recovery by dispatching the work to the systems that own it. (CLAUDE.md §4: "failure decisions belong to resilience/".)

## Scope (What This System Owns)
- **The single failure-decision graph** for the whole system. No other system owns "what do we do when this fails."
- **Recovery orchestration** — `FailureHandler`, folded in as the orchestrating core (ruled 2026-06-08): owns the recovery paths, rules, and strategy; **dispatches** execution to the owning system (resend → `ai/AICaller`, regenerate → the generator, record → `system/Logger`). Owns the decision, *not* the execution.
- **Classification** — `FailureClassifier`, keyed to ERROR-CODES.md. **Single authority for severity:** it maps reported error **code → level (1–4) → severity (low/medium/high/critical)**. Severity is a *derived* property of the classified error; reporting services pass the **code**, never a severity.
- **Retry & fallback policy** — `RetryPolicyEngine` (sets the policy: is this class retryable, how many, backoff) and `FallbackStrategyResolver` (decides *when/whether* to fall back). On the Workers target these **parameterize Cloudflare Queues** (native retry/backoff/DLQ); resilience does not run an in-process retry loop.
- **Salvage** — `SalvageCoordinator`: decides what partial output is salvageable and coordinates its recovery.
- **Recovery state & resume** — `RecoveryStateBuilder`, `ResumePointManager`: reconstruct resumable state from interrupted work and manage checkpoints — *derived from*, not a replacement for, canonical state in `state/`.
- **Live circuit-breaker** — per-service/provider failure **counters + windows + halt/health flag**, held in a **Durable Object** (atomic increment, strongly-consistent halt). Replaces the old in-process `halt()/resume()/isHealthy()`/pattern-window model, which cannot survive a stateless runtime.
- **Escalation & human handoff** — `EscalationRouter` (route by severity), `HumanHandoffManager` (decide handoff; hold the paused workflow in a Durable Object until a human responds).
- **Failure narration & records** — `FailureNarrator` (human-readable explanation + options); `FailureAuditRecorder` (append-only failure records + the manual-review queue, in resilience's own **D1** tables via `services/DatabaseHandler`).
- **Status lifecycle** of every failure: `pending → retrying → stored → resolved → dismissed` (carried unchanged from FAILSYS-PLAN; "retrying" = in-Queue retry once parameterized — a Pass 3 mechanism detail).

## Explicit Non-Goals
- **Does not perform the underlying work.** resilience dispatches; the owning systems execute (AICaller makes the call, generators regenerate, Logger logs, content/ assembles salvaged content).
- **Does not own canonical workflow state.** That is `state/StateManager`; resilience reads it and reconstructs *recovery* state from it.
- **Does not run platform-native retry mechanics.** Cloudflare Queues do routine retry/backoff/dead-letter; resilience sets the policy, the platform executes it.
- **Does not assign severity from a caller-supplied field.** Severity is derived once, by `FailureClassifier`, from the error code.
- **Does not make brand / quality / content decisions** (brand/, checks/, verification/).
- **Does not own the decision/override audit trail.** `audit/` owns DecisionTimeline / HumanOverrideLedger / AIUsageAttribution; resilience records *failure* events only.
- **Does not validate data or write final content** (Archivist validates; Writer writes) — carried from FAILSYS-PLAN's Does-NOT spine.

## Core Responsibilities
- Guarantee every failure is classified and met with an explicit recovery decision — **no silent failures**.
- Hold the one failure-decision graph so recovery logic is never duplicated across systems.
- Dispatch recovery actions to owning systems and track the outcome.
- Maintain the live circuit-breaker; halt/degrade the system when auto-halt conditions are met and expose a flag every invocation (and every Queue consumer) reads before doing work.
- Reconstruct recoverable state and resume from the last good checkpoint.
- Escalate to, and hold work for, a human when automated recovery is exhausted.
- Record every failure and recovery decision for traceability and manual review.

## System Guarantees
- Every failure receives a classification + an explicit recovery decision (never undefined).
- No silent failure: a failure is recovered, salvaged, escalated, or handed to a human — and always recorded.
- Severity is single-sourced (derived by FailureClassifier from the error code) — no two authorities can disagree.
- The halt flag is strongly consistent at the moment it is read (Durable Object), so a halted system is honoured immediately, including during a failure storm.
- Recovery state is reconstructable from canonical state + failure records.
- Escalation always terminates at a human when automation is exhausted (no infinite retry).

## System Boundaries

### Allowed Dependencies
- `state/` — read canonical workflow state to reconstruct recovery state.
- `system/Logger` — dispatch target for recording.
- `services/DatabaseHandler` — persist failure records to resilience's D1 tables.
- `ai/` and generators (via public interfaces) — dispatch targets for recovery actions.
- `orchestration/` — Queue context; resilience parameterizes Queue retry policy.
- **Durable Objects** (platform primitive) — live circuit-breaker state + paused-handoff state.
- ERROR-CODES.md (reference) — code → level → severity + recovery mapping.

### Forbidden Dependencies
- Must not be called so as to create a cycle (resilience is a global constraint: systems depend on it; it reaches others only through their public interfaces as dispatch targets).
- Must not absorb brand / quality / content decision logic.
- Must not re-implement work owned elsewhere (it dispatches, never duplicates).

### Cross-System Boundary Rulings (2026-06-08)
- **A — FailureAuditRecorder ↔ audit/ (RULED):** resilience owns **failure** records (its own D1 tables); `audit/` owns the **decision/override** ledgers. Failure events ≠ decision trail.
- **C — HumanHandoffManager ↔ review/ + orchestration/Notification (RULED):** resilience **decides + holds** the paused workflow (Durable Object); `review/ReviewQueue` provides the human approval queue; `orchestration/Notification` alerts. Decide+hold / queue / alert.
- **B — SalvageCoordinator ↔ content/ (RULED 2026-06-08):** resilience **decides** "attempt salvage" and coordinates; `content/` **executes** the content-level assembly (knowing what valid partial content *is* is content-domain knowledge resilience cannot hold). ⚠️ **Executor gap (verified):** `content/` has **no ContentSalvageModule** — it was a Model-A module, not carried into the 31 (only `resilience/SalvageCoordinator`, the decider, exists). content/ owns the concern, but its executor must be **created when content/ is grounded**; resilience must not dispatch salvage to an empty target until then.
- **D — FallbackStrategyResolver ↔ ai/ModelManager (RULED — restates the earlier provider-fallback ruling):** single provider-fallback authority = `ai/ModelManager` — it owns the model **chain** (e.g. Mini→Grok routing) and executes the swap; resilience owns the **trigger** (when/whether to fall back) and **never holds a parallel provider list**.

## Lifecycle / Maturity
- Status: **Draft** (Pass 1 grounding)
- Expected evolution: Pass 2 locks per-subsystem ownership (incl. FailureHandler as orchestrating core, and the circuit-breaker subsystem's DO contract); Pass 3 specs error codes, observability, the dispatch contracts, and the Queue-retry parameterization. All four cross-system boundaries are **ruled** (2026-06-08); the one carried obligation is that `content/` must create its salvage executor when grounded (boundary B).

## Notes
- **Stack-level adoption (recorded here per ruling 2026-06-08): Durable Objects are ADOPTED**, not merely proposed. STACK-PROPOSAL.md §1.3 already warranted DOs for stateful coordination, naming `RateLimiter` (per-provider rate state — structurally identical to this circuit-breaker) and `HumanHandoffManager` (a resilience subsystem). Adopting DOs once provides the correct primitive for all three (circuit-breaker, RateLimiter, HumanHandoffManager). *STACK-PROPOSAL should be updated to reflect DO = adopted.*
- **Origin doc:** `DOCS - Preplanning/Services/FAILSYS-PLAN.md`. It validates this grounding ("FailSys owns ALL failure logic… Services report failures, FailSys decides what to do"; Does-NOT: "Fix failures itself", "Know how to perform retries (routes back)"). Three platform gaps were translated, not copied: file storage (`/_system/failsys/`) → **D1**; hand-rolled retry rules → **Queue parameters**; `registerRetryHandler()` in-process callbacks → **re-enqueue/dispatch** (stateless Workers hold no callback map).
- **FailureHandler** physically relocates `system/` → `resilience/` (ruled). `system/` drops to 6 mechanical subsystems; resilience holds 11.
- Pass-0 unowned-capability flags now homed: circuit-breaker (DO, above); dead-letter/poison-work → Queue DLQ parameterized by RetryPolicyEngine.

**Status:** Draft Complete – Awaiting Review
