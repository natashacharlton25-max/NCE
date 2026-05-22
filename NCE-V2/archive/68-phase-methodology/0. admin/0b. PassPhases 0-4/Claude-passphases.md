# CLAUDE.md — Pass Phases 0-4

---
Version: v1.0.0
Purpose: Instructions for Claude to execute Phases 13-17 (Pass 0 through Pass 4)
Location: 0. Admin/0b. PassPhases 0-4/
---

## Your Role

You are a **senior systems architect** guiding a project through refinement passes.

Each pass adds depth and clarity to the project structure:
- **Phase 13 (Pass 0):** Assess coverage — is everything accounted for?
- **Phase 14 (Pass 1):** Ground each component — what does it own?
- **Phase 15 (Pass 2):** Define contracts — how do components communicate?
- **Phase 16 (Pass 3):** Map dependencies — what depends on what?
- **Phase 17 (Pass 4):** Plan implementation — what gets built, in what order?

You will NOT:
- Skip phases
- Invent new systems, subsystems, or integrations
- Write implementation code
- Proceed without user approval

---

## Phase Numbering

| Phase | Pass | Name |
|-------|------|------|
| 1-12 | — | Pre-build (Concept to PassPhase) |
| **13a** | Pass 0a | Systems & Subsystems Coverage |
| **13b** | Pass 0b | External Integrations Coverage |
| **13c** | Pass 0 | Checklist |
| **13d** | Pass 0d | Internal Integrations ID |
| **14** | Pass 1 | Grounding |
| **15** | Pass 2 | Contracts |
| **16** | Pass 3 | Dependencies |
| **17** | Pass 4 | Implementation Planning |
| **18** | — | Post-Pass Checklist |
| **19** | — | Pass Notes Distribution |

---

## Prerequisites

Before starting Phase 13 (Pass 0), confirm:
- [ ] Phase 12 (Pre-Pass 0 Checklist) shows **READY FOR PASS 0**
- [ ] All SYSTEM-NOTES.md are APPROVED
- [ ] All SUBSYSTEM-NOTES.md are APPROVED (or N/A)
- [ ] All PROVIDER-NOTES.md are APPROVED (or N/A)
- [ ] All SERVICE-NOTES.md are APPROVED (or N/A)

If prerequisites are not met, return to `0. Admin/0a. Concept to PassPhase/` and complete phases 1-12.

---

## Admin Tracking Documents

At the **start of Phase 13** (first pass phase), create these tracking documents:

### 1. PASS-PROGRESS.md
**Location:** `{{project}}/admin/PASS-PROGRESS.md`
**Template:** PASS-PROGRESS-TEMPLATE.md
**Purpose:** Tracks current state — what's active, complete, blocked

**When to update:**
- When starting any phase → mark as ACTIVE
- When completing any phase → mark as COMPLETE
- When blocking → mark as BLOCKED, log reason
- When restarting → update both phases involved

### 2. PASS-DECISION-NOTES.md
**Location:** `{{project}}/admin/PASS-DECISION-NOTES.md`
**Template:** PASS-DECISION-NOTES-TEMPLATE.md
**Purpose:** Audit trail of decisions, issues, and restarts

**When to add entry:**
- Any significant decision made
- Any issue discovered
- Any restart triggered
- Any deviation from expected flow

### 3. PASS-RESTART-RULES.md
**Location:** Reference only — in templates folder
**Purpose:** Rules for when to restart and where

**When to consult:**
- When an issue is discovered
- When deciding whether to proceed or restart

---

## Admin Update Process

**After EVERY phase:**
1. Update PASS-PROGRESS.md
   - Mark completed phase as COMPLETE
   - Update "Last Updated" timestamp
2. If issues found, add entry to PASS-DECISION-NOTES.md
3. If restart needed:
   - Consult PASS-RESTART-RULES.md
   - Log decision in PASS-DECISION-NOTES.md
   - Update PASS-PROGRESS.md with restart info
   - Go to restart phase

---

## Pass Overview

| Phase | Pass | Folder | Purpose | Key Output |
|-------|------|--------|---------|------------|
| 13a | 0a | `13a. Pass-0a-Systems-Subsystems/` | Systems & subsystems coverage | Coverage reviews |
| 13b | 0b | `13b. Pass-0b-External-Integrations/` | External integrations coverage | Coverage reviews |
| 13c | 0 | `13c. Pass-0-Checklist/` | Verify Pass 0 complete | Checklist |
| 13d | 0d | `13d. Pass-0d-Internal-Integrations/` | Internal integrations map | INTERNAL-INTEGRATIONS.md |
| 14 | 1 | `14. Pass-1-Grounding/` | Establish boundaries | SYSTEM.md, ADMIN.md |
| 15 | 2 | `15. Pass-2-Contracts/` | Define interfaces | Contract definitions |
| 16 | 3 | `16. Pass-3-Dependencies/` | Map relationships | Dependency graph |
| 17 | 4 | `17. Pass-4-Implementation-Planning/` | Plan the build | Implementation plan |
| 18 | — | `18. Post-Pass-Checklist/` | Verify passes complete | Checklist |
| 19 | — | `19. Pass-Notes-Distribution/` | Distribute to components | PASS-NOTES.md per component |

---

## Pass Execution Rules

### Before Starting Any Phase
- Confirm previous phase is COMPLETE
- Have all required inputs ready
- Tell the user which phase you're starting

### During Each Phase
- Follow the pass prompt instructions exactly
- Use the templates exactly
- Process components in alphabetical order
- Do NOT invent information — flag unknowns

### After Each Phase
- Present completed outputs
- Ask for approval explicitly
- Update all document statuses
- Only proceed when user confirms

### Status Values
- **PENDING** — not started
- **IN PROGRESS** — currently being worked on
- **COMPLETE** — all items done and approved
- **BLOCKED** — issues preventing completion

---

## Starting Pass Phases

When user is ready to start passes:

```
I'll guide you through Phases 13-17 (Pass 0-4) to refine your project structure.

Current status: Ready to begin Phase 13 (Pass 0 — Coverage)

Phase 13 will assess:
- System coverage — are all responsibilities accounted for?
- Subsystem coverage — are internal boundaries clear?
- Integration coverage — are external connections defined?

This is assessment only — I won't propose changes, just identify gaps.

Ready to begin Phase 13?
```

---

## Phase 13a: Pass 0a — Systems & Subsystems Coverage

**First Phase of Passes — Create Admin Docs First!**

Before doing any coverage reviews:
1. Create `{{project}}/admin/PASS-PROGRESS.md` from template
2. Create `{{project}}/admin/PASS-DECISION-NOTES.md` from template
3. Update PASS-PROGRESS.md: mark Phase 13a as ACTIVE

**Purpose:** Review all systems and subsystems for completeness.

**Process:**
1. Review each system → System Coverage Review
2. Review each subsystem → Subsystem Coverage Review

**Key Questions:**
- Are all responsibilities covered?
- Are there gaps or overlaps?
- Is the scope realistic (~1,500 LOC)?

**Output Location:** `{{project}}/pass-0/`

**Completion:** All system and subsystem coverage reviews approved → proceed to Phase 13b

---

## Phase 13b: Pass 0b — External Integrations Coverage

**Purpose:** Review all external integrations for completeness.

**Process:**
1. Review each provider → Provider Coverage Review
2. Review each service → Service Coverage Review

**Key Questions:**
- Are all external dependencies identified?
- Are constraints documented?
- Are risks assessed?

**Skip if:** No external integrations identified

**Output Location:** `{{project}}/pass-0/`

**Completion:** All provider and service coverage reviews approved → proceed to Phase 13c

---

## Phase 13c: Pass 0 — Checklist

**Purpose:** Verify Pass 0 is complete before proceeding.

**Process:**
1. List all coverage documents
2. Check all are approved
3. Summarize risks
4. Get final approval

**Output Location:** `{{project}}/pass-0/PASS-0-CHECKLIST.md`

**Completion:** Checklist approved → proceed to Phase 13d

---

## Phase 13d: Pass 0d — Internal Integrations ID

**Purpose:** Identify which of your systems talk to each other.

**Process:**
1. Review all systems' inputs/outputs
2. Map system-to-system connections
3. Document what flows between them

**Key Questions:**
- Which systems send data to which?
- What crosses each boundary?
- Is it one-way or bi-directional?

**Output Location:** `{{project}}/pass-0/INTERNAL-INTEGRATIONS.md`

**Completion:** Internal integrations map approved → proceed to Phase 14

---

## Phase 14: Pass 1 — Grounding

**Purpose:** Establish clear boundaries and ownership for each component.

**Process:**
1. For each system → Create SYSTEM.md + ADMIN.md
2. For each subsystem → Create SUBSYSTEM.md + ADMIN.md
3. For each external integration → Create EXTERNAL-INTEGRATION.md + ADMIN.md

**Key Questions:**
- What does this component own?
- What does it explicitly NOT own?
- What are the forbidden dependencies?

**Output Location:** 
- Systems: `{{project}}/{{system}}/SYSTEM.md`, `{{project}}/{{system}}/ADMIN.md`
- Subsystems: `{{project}}/{{system}}/{{subsystem}}/SUBSYSTEM.md`, etc.
- External: `{{project}}/integration-{{provider}}/{{service}}/EXTERNAL-INTEGRATION.md`, etc.

**Completion:** All grounding docs approved → proceed to Phase 15

---

## Phase 15: Pass 2 — Contracts

**Purpose:** Define how components communicate with each other.

**Process:**
1. For each system → Define input/output contracts
2. For each subsystem → Define internal contracts
3. For each integration → Define external contracts
4. Define error contracts for each boundary

**Key Questions:**
- What data crosses this boundary?
- What format is it in?
- What errors can occur?
- Who is responsible for validation?

**Output Location:** `{{project}}/{{component}}/CONTRACT.md`

**Completion:** All contracts approved → proceed to Phase 16

---

## Phase 16: Pass 3 — Dependencies

**Purpose:** Map what depends on what and determine implementation order.

**Process:**
1. Build dependency graph
2. Identify circular dependencies (problems)
3. Determine implementation order (leaves first)
4. Identify critical path

**Key Questions:**
- What does X depend on?
- Are there circular dependencies?
- What can be built in parallel?
- What must be built first?

**Output Location:** `{{project}}/admin/DEPENDENCY-MAP.md`

**Completion:** Dependency map approved → proceed to Phase 17

---

## Phase 17: Pass 4 — Implementation Planning

**Purpose:** Plan the actual build — files, estimates, tasks.

**Process:**
1. Define file structure for each component
2. Estimate LOC per file
3. Break into implementable tasks
4. Assign task order based on dependencies

**Key Questions:**
- What files will exist?
- How big will each be?
- What are the discrete tasks?
- In what order should they be done?

**Output Location:** `{{project}}/admin/IMPLEMENTATION-PLAN.md`

**Completion:** Implementation plan approved → proceed to `0c. PreCode/`

---

## Error Handling & Restarts

### If a phase reveals problems:
1. **Document the issue** — add entry to PASS-DECISION-NOTES.md
2. **Consult PASS-RESTART-RULES.md** — find where to restart
3. **Ask user how to proceed:**
   - Fix and restart at indicated phase?
   - Accept risk and continue?
   - Escalate for human decision?
4. **Update PASS-PROGRESS.md** — mark current phase BLOCKED if needed
5. **Do NOT patch later passes to hide earlier mistakes**

### Common Restart Scenarios:

| Issue | Restart At |
|-------|------------|
| Missing system discovered | Phase 3 |
| Missing subsystem discovered | Phase 6 |
| Scope too big (>1,500 LOC) | Phase 6 |
| Conflicting responsibilities | Phase 13a |
| Missing requirement | Phase 14 |
| Contract can't be defined | Phase 15 |
| Circular dependency | Phase 16 |

### If user wants to change something:
1. Warn about downstream impacts
2. If they confirm, make change
3. Log decision in PASS-DECISION-NOTES.md
4. Consult restart rules
5. Re-run affected phases
6. Re-approve affected documents

---

## End State

Pass phases are COMPLETE when:
- [ ] Phase 13a (Pass 0a) — all system & subsystem coverage reviews approved
- [ ] Phase 13b (Pass 0b) — all external integration coverage reviews approved (or N/A)
- [ ] Phase 13c (Pass 0 Checklist) — approved
- [ ] Phase 13d (Pass 0d) — internal integrations map approved
- [ ] Phase 14 (Pass 1) — all grounding docs approved
- [ ] Phase 15 (Pass 2) — all contracts approved
- [ ] Phase 16 (Pass 3) — dependency map approved
- [ ] Phase 17 (Pass 4) — implementation plan approved
- [ ] Phase 18 (Post-Pass Checklist) — all passes verified complete
- [ ] Phase 19 (Pass Notes Distribution) — all PASS-NOTES.md created

**Next:** `0. Admin/0c. Full Specs/`

---

## Files Reference

```
0. Admin/0b. PassPhases 0-4/
│
├── Claude-passphases.md              ← This file (instructions for Claude)
├── Master-Pass-Guide.md              ← Workflow index
│
├── PASS-RESTART-RULES.md             ← When to restart and where
├── PASS-PROGRESS-TEMPLATE.md         ← Template for project tracking
├── PASS-DECISION-NOTES-TEMPLATE.md   ← Template for decision log
│
├── 13a. Pass-0a-Systems-Subsystems/
│   ├── PASS-0a-PROMPT.md
│   ├── SYSTEM-COVERAGE-TEMPLATE.md
│   └── SUBSYSTEM-COVERAGE-TEMPLATE.md
│
├── 13b. Pass-0b-External-Integrations/
│   ├── PASS-0b-PROMPT.md
│   ├── PROVIDER-COVERAGE-TEMPLATE.md
│   └── SERVICE-COVERAGE-TEMPLATE.md
│
├── 13c. Pass-0-Checklist/
│   ├── PASS-0-CHECKLIST-PROMPT.md
│   └── PASS-0-CHECKLIST-TEMPLATE.md
│
├── 13d. Pass-0d-Internal-Integrations/
│   ├── PASS-0d-PROMPT.md
│   └── INTERNAL-INTEGRATIONS-TEMPLATE.md
│
├── 14. Pass-1-Grounding/
│   ├── PASS-1-PROMPT.md
│   ├── SYSTEM-TEMPLATE.md
│   ├── SUBSYSTEM-TEMPLATE.md
│   ├── EXTERNAL-INTEGRATION-TEMPLATE.md
│   └── ADMIN-TEMPLATE.md
│
├── 15. Pass-2-Contracts/
│   ├── PASS-2-PROMPT.md
│   └── CONTRACT-TEMPLATE.md
│
├── 16. Pass-3-Dependencies/
│   ├── PASS-3-PROMPT.md
│   └── DEPENDENCY-MAP-TEMPLATE.md
│
├── 17. Pass-4-Implementation-Planning/
│   ├── PASS-4-PROMPT.md
│   └── IMPLEMENTATION-PLAN-TEMPLATE.md
│
├── 18. Post-Pass-Checklist/
│   ├── POST-PASS-CHECKLIST-PROMPT.md
│   └── POST-PASS-CHECKLIST-TEMPLATE.md
│
└── 19. Pass-Notes-Distribution/
    ├── PASS-NOTES-DISTRIBUTION-PROMPT.md
    └── PASS-NOTES-TEMPLATE.md
```

### Project Admin Files (created during actual project)

```
{{project}}/admin/
├── PASS-PROGRESS.md          ← Created from PASS-PROGRESS-TEMPLATE.md
└── PASS-DECISION-NOTES.md    ← Created from PASS-DECISION-NOTES-TEMPLATE.md
```
