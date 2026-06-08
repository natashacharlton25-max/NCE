# System Coverage Review — state

## System Role Summary
The `state` system is the canonical source of truth for system state, execution context, and workflow transitions — it assembles the execution context every workflow runs against, holds the authoritative current/historical state, and gates which state moves are legal. It is foundational: every workflow depends on it for context-in and state-out.

## Coverage Assessment

### Covered Responsibilities
| Responsibility | Covered By |
|----------------|------------|
| Assemble execution context (merge brand, audience, theme, template, user, job) | ContextAssembler |
| Own the context shape and context resolution order | ContextAssembler |
| Hold canonical current state (workflow, content, AI execution) | StateManager |
| Track workflow progress / lifecycle position | StateManager |
| Capture state snapshots | StateManager |
| Enable recovery and rollback (provide the canonical state recovery rebuilds *from*) | StateManager |
| Deterministic replay of state | StateManager |
| Define allowed-transition rules | StateTransitionValidator |
| Reject / signal illegal lifecycle jumps | StateTransitionValidator |

### Partially Covered Responsibilities
- **Recovery / rollback execution.** StateManager's module doc claims it "enables recovery and rollback" and owns "state snapshots," while `resilience/RecoveryStateBuilder` (per the resilience Pass 0) claims to "reconstruct resumable state" and `resilience/ResumePointManager` claims checkpoint/resume bookkeeping. The *canonical-state* side is covered here; the *recovery-state reconstruction / resume-point* side appears to be claimed by resilience. The split (who stores snapshots vs who rebuilds from them) is asserted on both sides but reconciled in neither.
- **Workflow status vs workflow state.** StateManager tracks "workflow state"; `orchestration/Orchestrator` owns jobs.db (job queue) and `orchestration/StatusHandler` reports job status. Whether "this job is at step N" is canonical state (here) or orchestration status is not settled.
- **Snapshot persistence / storage contract.** StateManager owns "state snapshots" conceptually, but no Repository/Schema content exists (all support docs are empty templates), so the persistence contract is unspecified.

### Uncovered Responsibilities
- None that imply a missing subsystem. The three subsystems cover assemble → hold → gate. The gaps above are ownership/boundary questions and unwritten detail, not absent functions. (See Missing Capability Areas for two thinner spots.)

## Subsystem Sufficiency Review
| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| ContextAssembler | Sufficient | Single clear job: merge brand/audience/theme/template/user/job into one stable execution object and own resolution order. Pure assembly; reads brand (global) rather than deciding it. Boundary w/ brand/ noted below. | 400–650 | Low |
| StateManager | Sufficient (scope must be cut at resilience line) | Canonical state store, transitions log, snapshots, replay. This is the system's centre of gravity and the only realistic >1000 LOC risk if it also absorbs recovery-state reconstruction + resume bookkeeping that resilience claims. Kept canonical-only, it is comfortably bounded. | 700–1,200 | Medium |
| StateTransitionValidator | Sufficient | Pure decision logic: transition-rule table + illegal-jump rejection. Narrow and stable. | 300–500 | Low |

## Missing Capability Areas
| Capability | Impact | Est. LOC | Notes |
|-----------|--------|----------|------|
| State persistence / snapshot storage contract | Medium | (within StateManager) | StateManager "owns snapshots" but no Repository/Schema doc exists; how snapshots persist on D1 and their retention is unspecified. Not a missing subsystem — a missing contract. |
| Concurrency / write-ordering for canonical state | Low–Medium | (within StateManager) | As the single source of truth for parallel workflows, concurrent-write / ordering guarantees have no named owner. Flag for Pass 1, not a new subsystem. |

## Boundary & Classification Issues
- **state/StateManager ↔ resilience/RecoveryStateBuilder + resilience/ResumePointManager (LOAD-BEARING).** Both systems claim recovery/resume state. StateManager: "enables recovery and rollback," owns "state snapshots." resilience Pass 0: RecoveryStateBuilder "reconstructs resumable state," ResumePointManager owns checkpoints/resume. Likely correct split = state owns *canonical* state + snapshots (the source of truth); resilience owns *failure-driven reconstruction* and resume-point selection (a failure DECISION, which per CLAUDE.md §4 belongs to resilience/). This must be drawn explicitly at Pass 1 or snapshots/recovery state will have two authorities. (Assessment only — not deciding the split.)
- **state/StateManager ↔ orchestration/Orchestrator + orchestration/StatusHandler.** Orchestrator owns jobs.db (job queue/flow); StatusHandler reports job status. The orchestration Pass 0 asserts "state manages persistent state, orchestration coordinates operations," but "workflow position/status" sits on the seam. Who is canonical for "where is this job" must be settled.
- **state/ContextAssembler ↔ brand/.** Brand is a global constraint; brand DECISIONS belong to brand/ (CLAUDE.md §4). ContextAssembler must *read/resolve* brand context into the execution object, never *decide* it. The boundary hint (brand as context source) holds, provided ContextAssembler stays a consumer of brand output.
- **state/ContextAssembler ↔ source systems (audience / theme / template / user).** ContextAssembler merges from several systems; it owns the merged *shape and resolution order*, not the source data. Consumer-only relationship to each source should be explicit at Pass 1.
- **Renderer rule (informational).** Not implicated — no renderer logic appears in state; ContextAssembler producing context for renderers keeps logic out of renderers, consistent with the global rule.

## Overall Build Size Estimate
- **Total estimated LOC:** ~1,400–2,350
- **Largest expected file:** StateManager (~700–1,200 LOC) — the only Medium size risk; stays under 1500 if recovery-state reconstruction is held in resilience/ and snapshot storage detail does not balloon it.
- **No files expected to exceed 1500 LOC** under the canonical-only scoping. If StateManager additionally absorbs recovery reconstruction + resume bookkeeping, it would trend toward/over the 1500 LOC High-risk line.

## Risk Assessment
- **Risk Level:** Medium
- **Rationale:** Coverage and decomposition are sound (assemble → hold → gate is complete and non-overlapping internally). Risk is not internal sufficiency. It is (1) the unresolved state ↔ resilience recovery-state boundary, which is load-bearing because it determines StateManager's true size and could push it over 1500 LOC, and (2) the unwritten persistence/snapshot and concurrency contracts. All system- and subsystem-level support docs (System, Index, Subsystem, Spec, Schema, Repositorys) are still empty templates — only the three module .md stubs carry intent — so these boundaries currently rest on cross-system assertions that the state docs do not yet confirm.

## Verdict
**CAN** fulfil role as currently structured. The three subsystems are sufficient and appropriately scoped to manage execution context, canonical state, and transition legality. The CAN is conditional on, at Pass 1: (a) drawing the state ↔ resilience boundary so canonical state/snapshots live here and failure-driven recovery reconstruction + resume-point selection live in resilience/ (per CLAUDE.md §4); (b) settling workflow-position authority against orchestration/; (c) confirming ContextAssembler is a consumer of brand (and other source systems), not a decider; and (d) specifying StateManager's snapshot persistence and concurrency contract so it stays under the 1500 LOC line. These are ownership lines and unwritten contracts, not missing capabilities.

**Status:** Draft Complete – Awaiting Review
