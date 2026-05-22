# Claude Operating Rules

---
Version: v2.3.0
Location: 0. Admin/CLAUDE.md
Purpose: Master rules for all phases and sections
---

## Core Principle

Never decide *what* to build and *how* to build in the same step.

---

## Primary Constraint

No system or subsystem should reasonably exceed ~1,500 lines of implementation code.
If a responsibility would exceed this, it must be split or explicitly flagged.

**LOC Budget Reality:**
- Framework boilerplate counts against budget
- Type definitions count against budget
- Target ~300 LOC per file
- If exceeding budget, split the component (this is intended)

---

## Forbidden Actions

- Do not propose architectures before Pass 2
- Do not write code before Pass 3
- Do not introduce technologies, frameworks, databases, or APIs
- Do not invent requirements or features
- Do not collapse systems or subsystems without justification
- Do not "fix forward" — if consolidation reveals conflicts, roll back to specs
- Do not add functionality during implementation without spec updates
- Do not delete PASS-NOTES entries — mark as superseded instead

---

## Required Behaviour

- Classify responsibilities, not implementations
- Prefer fewer, clearer systems
- Preserve uncertainty when information is incomplete
- Respect system and subsystem boundaries
- Justify all splits in terms of maintainability and size
- Follow provided templates exactly
- Check for supersession flags before acting on PASS-NOTES

---

## Size Heuristics

Assume a split is required if a responsibility involves:
- Multiple independent workflows
- Stateful lifecycle management
- External integrations plus internal logic
- Policy/validation mixed with execution
- Complex error handling or orchestration

---

## Output Rules

- Follow provided templates exactly
- Use Markdown only
- No extra commentary outside templates
- Keep language clear and conservative

---

## Escalation Rule

If a responsibility cannot clearly fit under ~1,500 LOC:
- Flag it explicitly
- Request human confirmation before proceeding

---

## Pass Governance

Claude must follow the project pass lifecycle as defined in:
- `0. Admin/0b. PassPhases 0-4/` admin docs
- `PASS-PROGRESS.md` in project

Rules:
- Passes must be executed in order
- Claude may not skip passes
- If a failure is detected, Claude must identify the correct restart point
- Claude must not patch later passes to compensate for earlier mistakes
- Changes to system or subsystem NOTES.md require re-approval

---

## Section-Specific Rules

### 0d PreCode — Rollback Rule

If Phases 29 (Database Schema Consolidation) or 30 (API Surface Consolidation) surface conflicts:
- **MUST roll back to Full Specs** (0c)
- **MUST NOT "fix forward"**
- Document the conflict in PRECODE-DECISION-NOTES.md
- Return to the relevant component spec, fix it, re-approve
- Then resume PreCode

**Why:** Fixing forward creates hidden divergence. Specs are the source of truth.

---

### 0e Frontend — Backend Contract

Create explicit `FRONTEND-BACKEND-CONTRACT.md` that consolidates:
- API-SURFACE.md (from Phase 30)
- Relevant TYPES (from Phase 26)
- Relevant ERROR-CODES (from Phase 26)

Frontend specs reference this contract, not scattered source files.

**Why:** Single binding artifact prevents interpretation drift.

---

### 0f Implementation — Guardrails

1. **0f implements, does not decide**
   - If implementation requires inventing behaviour not in specs, STOP
   - Escalate to relevant section (0c/0d/0e)
   - Do not "fix forward"

2. **Shared types are mandatory**
   - Generate types from TYPES.md in Phase 43
   - Use throughout backend and frontend
   - No duplicate type definitions

3. **Phase 47 and 53 never collapse**
   - Backend verification (47) must happen before frontend
   - Implementation verification (53) must happen before handoff
   - Other phases may be combined for small projects

4. **Performance constraints are binding**
   - Meet targets in DESIGN-SYSTEM.md
   - Document if unable to meet with reasoning
   - Do not ignore constraints

5. **Commit discipline**
   - Meaningful commits per logical unit
   - Audit trail verified in Phase 53

**Why:** Implementation is where methodology typically breaks down. These rules prevent scope creep, type drift, and undocumented changes.

---

### 0g Testing — Rules

1. **Test against specs, not implementation**
   - Tests verify behaviour matches specs
   - Tests remain valid if code is refactored
   - Spec documents are the source of truth

2. **Tests must be deterministic**
   - No reliance on real time, randomness, or external services
   - Tests must pass consistently (flakiness = bug)
   - Execution order must not matter

3. **Traceability is mandatory**
   - Every test traces to a spec (TEST-TRACE.md)
   - Every spec should have coverage
   - Tests without traceability become arbitrary

4. **0g finds, 0f fixes**
   - Testing discovers bugs, implementation fixes them
   - All fixes return to 0f, re-pass Phase 53
   - No mid-phase patching

5. **Phase 54 and 60 never collapse**
   - Test Planning (54) must happen before testing
   - Test Verification (60) must happen before handoff
   - Other phases may be combined for small projects

6. **Flakiness check required**
   - Full suite must pass 3x consecutively
   - Flaky test = Phase 60 fails

**Why:** Testing without discipline creates false confidence. These rules ensure tests actually verify the system works.

---

### 0h Prerelease — Rules

1. **RC Immutability is absolute**
   - RC tags are immutable — never move a tag
   - If RC fails, create rc.N+1 and restart from Phase 61
   - Old RC tags remain for audit trail
   - **Never suggest:** "patch rc.1", "update the tag", "retest same RC"

2. **Locked items require new RC**
   - Items lock progressively through phases (61 → 62 → 63 → 64)
   - Any change to a locked item requires new RC + restart from Phase 61
   - Phase 64: ALL items frozen — no changes allowed
   - Use Lock Status Tracker to verify

3. **Gates are binary**
   - All CI/CD gates are pass/fail
   - No judgement calls, no "looks good enough"
   - Performance, security, license gates block deploy if failed

4. **0h hardens, does not change**
   - No new features during 0h
   - No bug fixes (defer to backlog or return to 0f)
   - No test changes (return to 0g)
   - Production readiness only

5. **Rollback must be verified**
   - Rollback dry run required on staging
   - Target: < 10 minutes (document if exceeds)
   - Data integrity verified after rollback

6. **Sign-offs are explicit**
   - Required roles must sign with name and timestamp
   - Release Owner has final GO/NO-GO authority
   - Fallback chain if Release Owner unavailable

7. **Phase 61 and 64 never collapse**
   - Phase 61 (CI/CD Pipeline) — RC creation foundation
   - Phase 64 (Final Review) — Release gate, sign-off
   - Phases 62-63 may be combined for small projects

**Why:** Prerelease is where shortcuts cause production incidents. These rules enforce discipline at the most critical stage.

---

### 0i Rollout — Rules

1. **Deploy exactly what 0h approved**
   - Only the signed-off RC deploys
   - Verify commit SHA matches RC tag
   - No last-minute changes

2. **0i Readiness Review required**
   - 15-minute cross-functional review before Phase 65
   - All key roles present and confirmed ready
   - Signed "GO to begin 0i" (separate from "GO to deploy")

3. **Abort 0i Criteria are absolute**
   - If ANY abort condition exists, do NOT proceed to Phase 66
   - Conditions: upstream degraded, monitoring drift, secrets mismatch, freeze violation, missing sign-offs, on-call/Release Owner unavailable, deployer fatigue

4. **Golden Hour is sacred**
   - First 60 minutes post-deploy, Release Owner must be available
   - No meetings, no distractions
   - If unavailable, reschedule deployment

5. **On-call has autonomous rollback authority**
   - On-call can rollback without approval during incident
   - Do NOT wait for consensus during active incident
   - Document decision, execute immediately

6. **Evidence before rollback**
   - Capture metrics snapshot BEFORE initiating rollback
   - Without evidence, root cause analysis becomes guesswork
   - Use EVIDENCE-SNAPSHOT-GUIDE.md

7. **Noisy signals require duration**
   - Known noisy conditions (JVM warmup, cache miss, CPU spike) require sustained duration before rollback evaluation
   - Do NOT rollback on transient spikes
   - Use Noisy Signal Classification table

8. **Hotfix boundary is absolute**
   - No code changes during 0i stabilization
   - If hotfix needed: exit 0i → hotfix path → new RC → restart from 0h Phase 61
   - Document in DEVIATION-REPORT.md

9. **Phase 66 and 68 never collapse**
   - Phase 66 (Production Deployment) — deployment execution
   - Phase 68 (Stabilization & Handoff) — operational handoff
   - Phases 65 and 67 may be combined for small projects

10. **Release fatigue guardrail**
    - No more than 2 production deployments per team per day without Director approval
    - Prevents attention degradation from multiple clean releases

**Why:** Rollout is where all prior work succeeds or fails. These rules ensure disciplined execution and rapid response to issues.

---

## 0i Authority Matrix

| Action | Authority | Notes |
|--------|-----------|-------|
| Start 0i (Readiness Review) | Release Owner | GO decision |
| Pause canary | Deployer or On-call | No approval needed |
| Resume canary | Deployer + On-call consensus | Requires agreement |
| Rollback | On-call (autonomous) | Do NOT wait |
| Abort release entirely | Release Owner | Final authority |
| Extend Golden Hour | Release Owner | |
| Exit 0i for hotfix | Release Owner | |
| Close release | Release Owner | |

---

## External Audit Requirements

External AI audits provide independent verification at critical section boundaries. Claude must support the audit process.

### Mandatory Audit Checkpoints

| Section | Phase | Audit Requirement |
|---------|-------|-------------------|
| 0c Full Specs | 28 | MANDATORY — Must pass before 0d |
| 0f Implementation | 53 | MANDATORY — Must pass before 0g |
| 0h Prerelease | 64 | MANDATORY — Must pass before 0i |

### Claude's Audit Responsibilities

1. **Prepare handoff package** — At section end, create 0X-AUDIT-HANDOFF.md
2. **Self-assess honestly** — Score work 1-5 on completeness, consistency, accuracy
3. **List concerns** — Document uncertainties, areas needing extra review
4. **Provide file manifest** — List all files with brief descriptions
5. **Respond to findings** — Fix issues flagged by external auditor

### Handoff Package Contents

When completing a mandatory audit section, create handoff with:
- Executive summary (2-3 paragraphs)
- Self-assessment scores (be honest)
- Concerns and uncertainties
- Key decisions made
- File manifest
- Dependency bundles (related file groups)
- Recommended files for review

### Responding to Audit Findings

| Finding Severity | Claude's Action |
|------------------|-----------------|
| 🔴 Critical | Fix immediately, document fix |
| 🟠 High | Fix before proceeding |
| 🟡 Medium | Fix or explain why not |
| 🔵 Low | Note for future, may proceed |

### Rules

- **Do NOT** argue with audit findings without evidence
- **Do NOT** skip handoff package creation
- **Do NOT** proceed past audit checkpoint without human approval
- **DO** acknowledge when auditor is correct
- **DO** defend position when you have evidence

**Why:** External perspective catches blind spots. Honest self-assessment is more valuable than defensive posturing.

---

## PASS-NOTES Supersession

When a later decision supersedes an earlier one:
- Add `SUPERSEDED BY: Phase XX` flag to the old note
- Add `SUPERSEDES: Phase YY` flag to the new note
- Do NOT delete old notes (history matters)
- Check for supersession flags before acting on any note

**Why:** Conflicting instructions cause AI hallucination.

---

## LLM-Friendly Code Standards

Code must be written for AI comprehension:

1. **Composition over inheritance** — Flat structures are easier to parse
2. **Explicit over implicit** — No magic, no hidden behavior
3. **Small files** — Target ~300 LOC per file
4. **Clear naming** — Descriptive, not clever
5. **Minimal boilerplate** — Framework boilerplate counts against LOC budget
6. **Comments for "why"** — Not "what" (code shows what)

**Why:** The ~1,500 LOC constraint exists for AI context limits.

---

## Governance Documents

Administrative documents that control methodology execution. Claude must follow these when applicable.

### Key Governance Files

| Document | When to Use |
|----------|-------------|
| FAILURE-RECOVERY-RULES.md | When issues are found, need to roll back |
| RISK-ACCEPTANCE-TEMPLATE.md | When accepting a known risk |
| CROSS-AI-VALIDATION.md | When Claude and auditor disagree |
| AUDIT-PROCESS.md | When preparing for external audit |

### Core Governance Rules

1. **No Fix-Forward** — Fix problems at source, not in later phases
2. **Human Authority** — Humans decide; Claude advises
3. **Mandatory Re-Audit** — Rolling back past audit checkpoint requires re-audit
4. **Documented Risk** — If proceeding despite risk, document formally

### Rollback Trigger Matrix

| Problem Found In | Roll Back To | Re-Audit Required? |
|------------------|--------------|-------------------|
| 0c Full Specs | 0a or 0b | No |
| 0c Audit | 0c (fix specs) | **Yes** |
| 0d PreCode | 0c (fix specs) | Yes (0c) |
| 0f Implementation | 0c or 0f | Yes if past 0c |
| 0f Audit | 0f or 0c | **Yes** |
| 0h Prerelease | 0f or 0g | Yes if past 0f |
| 0h Audit | Depends | **Yes** |

**Rule:** If you roll back past an audit point, that audit must be re-run.

---

## Reference Documents

### Section Instructions

| Document | Location | Purpose |
|----------|----------|---------|
| PROJECT-MASTER.md | Root | Complete methodology reference |
| Claude-prebuild.md | `0. Admin/0a/` | Phase 1-12 instructions |
| Claude-passphases.md | `0. Admin/0b/` | Phase 13-19 instructions |
| Claude-fullspecs.md | `0. Admin/0c/` | Phase 20-28 instructions |
| Claude-precode.md | `0. Admin/0d/` | Phase 29-36 instructions |
| Claude-frontend.md | `0. Admin/0e/` | Phase 37-42 instructions |
| Claude-implementation.md | `0. Admin/0f/` | Phase 43-53 instructions |
| Claude-testing.md | `0. Admin/0g/` | Phase 54-60 instructions (when built) |
| Claude-prerelease.md | `0. Admin/0h/` | Phase 61-64 instructions |
| Claude-rollout.md | `0. Admin/0i/` | Phase 65-68 instructions |

### Governance Documents

| Document | Location | Purpose |
|----------|----------|---------|
| FAILURE-RECOVERY-RULES.md | `0. Admin/` | Rollback and re-work procedures |
| RISK-ACCEPTANCE-TEMPLATE.md | `0. Admin/` | Formal risk acceptance |

### External Audit Documents

| Document | Location | Purpose |
|----------|----------|---------|
| AUDIT-PROCESS.md | `00. external-audit/core/` | Step-by-step audit guide |
| CROSS-AI-VALIDATION.md | `00. external-audit/core/` | Handling AI disagreements |
| AUDITOR-CONTROL-GUIDE.md | `00. external-audit/core/` | Managing difficult auditors |
| 0X-AUDIT-PROMPT.md | `00. external-audit/0X-section/` | Section-specific audit prompt |
| 0X-CHECKLIST.md | `00. external-audit/0X-section/` | Section-specific checklist |

Each section's Claude-*.md file provides phase-specific instructions.
This document provides the universal rules that apply to ALL phases.

---

## Quick Reference

| Rule | Summary |
|------|---------|
| **Core Rules** | |
| Core Principle | Separate "what" from "how" |
| Size Limit | ~1,500 LOC per component, ~300 per file |
| No Fix Forward | Roll back to specs on conflicts |
| No Scope Creep | Spec updates before new functionality |
| Supersession | Flag old notes, don't delete |
| LLM-Friendly | Composition, explicit, small files |
| **Implementation** | |
| Shared Types | Single source, no duplicates |
| Phase 47/53 | Verification phases never collapse |
| Performance | Binding constraints from DESIGN-SYSTEM.md |
| **Testing** | |
| Traceability | Tests map to specs |
| Test Determinism | No randomness, time, external deps |
| Flakiness Check | 3x consecutive pass required |
| Test Traceability | Every test maps to spec |
| 0g finds, 0f fixes | Testing discovers, implementation fixes |
| **Prerelease** | |
| RC Immutability | Tags never move; failed RC → rc.N+1 |
| Locked Items | Changes require new RC + restart Phase 61 |
| Binary Gates | CI/CD pass/fail, no judgement calls |
| Phase 61/64 | Prerelease phases never collapse |
| Rollback Verified | Dry run required, < 10 min target |
| Sign-offs Explicit | Name + timestamp, Release Owner GO/NO-GO |
| **Rollout** | |
| 0i Readiness Review | 15-min cross-functional GO before deployment |
| Abort 0i Criteria | Any condition = do NOT proceed to Phase 66 |
| Golden Hour | 60 min, Release Owner available |
| On-call Autonomous | Rollback authority without approval |
| Evidence Before Rollback | Capture snapshot BEFORE rollback |
| Noisy Signals | Require sustained duration before rollback |
| Hotfix Boundary | No changes during 0i; exit for hotfix path |
| Phase 66/68 | Rollout phases never collapse |
| Release Fatigue | Max 2 deploys/day without Director approval |
| **External Audit** | |
| Mandatory Audits | 0c (Phase 28), 0f (Phase 53), 0h (Phase 64) |
| Audit Handoff | Prepare 0X-AUDIT-HANDOFF.md at section end |
| Honest Self-Assessment | Score work 1-5, list concerns |
| Respond to Findings | Fix Critical/High before proceeding |
| **Governance** | |
| Human Authority | Humans decide; Claude advises |
| Rollback Re-Audit | Rolling back past audit = re-audit required |
| Documented Risk | Accepted risks must be formally documented |

---
