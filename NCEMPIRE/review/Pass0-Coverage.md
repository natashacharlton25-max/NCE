# System Coverage Review — review

## System Role Summary
The `review` system provides the human-oversight layer of NCEMPIRE: it queues items that need human attention, lets a human render final accept/reject/request-changes decisions, and lets a human override AI/rule outputs while capturing rationale for the audit trail. It is the seat of human authority over the automated pipeline.

## Coverage Assessment

### Covered Responsibilities

| Responsibility | Covered By |
|----------------|------------|
| Queue items needing human approval | ReviewQueue |
| Prioritise queued items by severity/risk | ReviewQueue |
| Track review lifecycle / status | ReviewQueue |
| Final accept / reject / request-changes decision | ApprovalManager |
| Override automated (AI/rule) decisions | ApprovalManager (stated) + ManualOverride (stated) |
| Capture override rationale / justification | ManualOverride |
| Manage override permissions | ManualOverride |

### Partially Covered Responsibilities
- **Authority/override boundary between ApprovalManager and ManualOverride.** Both modules claim "override automated decisions" (ApprovalManager Role) / "override AI/rules" (ManualOverride Role). The split — ApprovalManager = pipeline-decision authority, ManualOverride = capability/permission + rationale capture — is plausible but never stated. Each is a one-line placeholder; Subsystem/Spec/Schema are empty templates.
- **Audit-trail ownership.** ApprovalManager "Owns: Audit trail" and ManualOverride "Owns: Justification records," yet `audit/HumanOverrideLedger` declares ManualOverride as its source-of-record. Who persists vs. who emits is undefined.
- **Notifying reviewers.** Queue-and-prioritise implies reviewers must be alerted, but no module references `orchestration/Notification`. Trigger/ownership of the alert is unspecified.

### Uncovered Responsibilities
- **Assignment / routing of queued items to specific reviewers or roles** (who gets which item) — not owned by any subsystem.
- **SLA / timeout / escalation when a human does not respond** — no escalation path defined (compare resilience/HumanHandoffManager).
- **Decision persistence contract & idempotency** (re-decision, decision reversal) — no schema or state model exists.
- **Linkage back to the paused pipeline** — how an approval/override resumes the upstream work item is unowned (handoff boundary, see below).

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| ApprovalManager | Underdefined | Real, distinct authority responsibility, but only a placeholder one-liner; data model, decision states, error handling all empty. "Override automated decisions" duplicates ManualOverride wording and needs disambiguation. | 250–450 | Low |
| ManualOverride | Underdefined | Distinct job (override capability + rationale capture), but spec empty. Owns "Justification records" while audit/HumanOverrideLedger claims to own override storage — persistence boundary unresolved. | 250–450 | Low |
| ReviewQueue | Underdefined | Clear, distinct queueing+prioritisation role; likely the largest module (queue state, ordering, lifecycle), but no schema, no priority algorithm, no assignment/notify contract specified. | 400–700 | Low |

## Missing Capability Areas

| Capability | Impact | Est. LOC | Notes |
|------------|--------|----------|-------|
| Reviewer assignment / routing | High | 150–300 | No module owns who-gets-what; queue prioritisation alone does not place items with humans. |
| Response SLA / timeout escalation | Medium | 100–200 | No path when a human never acts; overlaps resilience/HumanHandoffManager — classify before building, do not build here by default. |
| Decision persistence & state machine | High | 150–300 | accept/reject/request-changes need a durable, idempotent state contract; currently no schema/state model. |
| Reviewer alerting hook | Medium | 50–120 | Expected to delegate to orchestration/Notification; integration point undeclared. |

## Boundary & Classification Issues
- **resilience/HumanHandoffManager (resilience):** "pause-for-human handoff" is the mechanism that *suspends a pipeline and routes it to a human*; `review/ReviewQueue` holds the resulting human work. These are adjacent, not duplicate — but the resume/handoff contract is unowned by either (both are placeholder/empty). Per CLAUDE.md, the failure/pause **decision** belongs to resilience; review should consume the handoff, not own the pause. Flag for explicit boundary: handoff trigger = resilience, human work item = review.
- **audit/HumanOverrideLedger (audit):** explicitly names `ManualOverride` as its event source and claims to own "Override event storage." This directly conflicts with ManualOverride "Owns: Justification records." Resolve as: ManualOverride **emits** override events + rationale; HumanOverrideLedger **persists** them. ManualOverride should write *to* the ledger, not keep its own record. (Audit is global per CLAUDE.md.)
- **orchestration/Notification (orchestration):** reviewer alerting belongs to orchestration, not review; ReviewQueue should *request* notifications, not implement channels. Integration currently undeclared on both sides.
- **Internal (ApprovalManager vs ManualOverride):** overlapping "override automated decisions" claims. Not a cross-system issue but a within-system authority/override split that must be stated before either spec is filled.

## Overall Build Size Estimate
Total estimated build: **~900–1,600 LOC** across the three subsystems (ApprovalManager 250–450, ManualOverride 250–450, ReviewQueue 400–700), plus ~450–920 LOC if the four missing capabilities are absorbed here rather than delegated. Largest expected file: **ReviewQueue** (queue state + prioritisation + lifecycle). No single file is expected to exceed 1,500 LOC; **no High size risk** under the current 3-subsystem decomposition.

## Risk Assessment
**Risk Level: Medium.** Not a sizing risk (all files comfortably under 1,500 LOC). The risk is **definitional and boundary**: every authoritative file except the three module one-liners is an empty template, two cross-system ownership conflicts exist (audit ledger vs ManualOverride records; resilience handoff vs ReviewQueue), an internal override-authority overlap is unresolved, and four operationally necessary capabilities (assignment, SLA/escalation, decision persistence, reviewer alerting) have no owner. These are decisions to be surfaced, not designed here (assessment only).

## Verdict
**CAN** fulfil its role as currently structured — the 3-subsystem decomposition (ReviewQueue / ApprovalManager / ManualOverride) cleanly covers the stated intent (queue, approve, override+rationale) with no redundant or oversized subsystem and no DROP/MERGE warranted. Conditions: (1) state the ApprovalManager-vs-ManualOverride override-authority split; (2) resolve override-record ownership so ManualOverride writes to audit/HumanOverrideLedger rather than storing its own; (3) classify the ReviewQueue ↔ resilience/HumanHandoffManager handoff/resume boundary; (4) assign owners for reviewer routing, response-SLA escalation, decision persistence, and the orchestration/Notification alert hook; (5) populate Subsystem/Spec/Schema (currently empty templates).

**Status:** Draft Complete – Awaiting Review
