# Master Pass Guide

---
Version: v1.0.0
Last Updated: {{timestamp}}
Location: 0. Admin/0b. PassPhases 0-4/
---

## Purpose
This is the **master index** for Phases 13-17 (Pass 0 through Pass 4).
Follow phases in order. Complete each phase before proceeding.

---

## Quick Reference

| Phase | Pass | Folder | Prompt | Templates | Output Location |
|-------|------|--------|--------|-----------|-----------------|
| 13a | 0a | `13a. Pass-0a-Systems-Subsystems/` | PASS-0a-PROMPT.md | SYSTEM-COVERAGE, SUBSYSTEM-COVERAGE | `{{project}}/pass-0/` |
| 13b | 0b | `13b. Pass-0b-External-Integrations/` | PASS-0b-PROMPT.md | PROVIDER-COVERAGE, SERVICE-COVERAGE | `{{project}}/pass-0/` |
| 13c | 0 | `13c. Pass-0-Checklist/` | PASS-0-CHECKLIST-PROMPT.md | PASS-0-CHECKLIST | `{{project}}/pass-0/` |
| 13d | 0d | `13d. Pass-0d-Internal-Integrations/` | PASS-0d-PROMPT.md | INTERNAL-INTEGRATIONS | `{{project}}/pass-0/` |
| 14 | 1 | `14. Pass-1-Grounding/` | PASS-1-PROMPT.md | SYSTEM, SUBSYSTEM, EXTERNAL-INTEGRATION, ADMIN | Component folders |
| 15 | 2 | `15. Pass-2-Contracts/` | PASS-2-PROMPT.md | CONTRACT | Component folders |
| 16 | 3 | `16. Pass-3-Dependencies/` | PASS-3-PROMPT.md | DEPENDENCY-MAP | `{{project}}/admin/` |
| 17 | 4 | `17. Pass-4-Implementation-Planning/` | PASS-4-PROMPT.md | IMPLEMENTATION-PLAN | `{{project}}/admin/` |
| 18 | — | `18. Post-Pass-Checklist/` | POST-PASS-CHECKLIST-PROMPT.md | POST-PASS-CHECKLIST | `{{project}}/admin/` |
| 19 | — | `19. Pass-Notes-Distribution/` | PASS-NOTES-DISTRIBUTION-PROMPT.md | PASS-NOTES | Component folders |

---

## Prerequisites

Before starting Phase 13, confirm Phase 12 (Pre-Pass 0 Checklist) shows **READY FOR PASS 0**:
- [ ] All foundation docs approved
- [ ] All systems built
- [ ] All subsystems built (or N/A)
- [ ] All providers built (or N/A)
- [ ] All services built (or N/A)

---

## Phase-by-Phase Workflow

---

### PHASE 13a: Pass 0a — Systems & Subsystems Coverage
**Folder:** `13a. Pass-0a-Systems-Subsystems/`

**Goal:** Review all systems and subsystems for coverage completeness.

| Item | Value |
|------|-------|
| **Prompt** | `Pass-0a-Systems-Subsystems/PASS-0a-PROMPT.md` |
| **Templates** | SYSTEM-COVERAGE-TEMPLATE.md, SUBSYSTEM-COVERAGE-TEMPLATE.md |
| **Input** | All SYSTEM-NOTES.md, SUBSYSTEM-NOTES.md |
| **Output** | `{{project}}/pass-0/SYSTEM-COVERAGE-*.md`, `{{project}}/pass-0/SUBSYSTEM-COVERAGE-*.md` |

**Process:**
1. System Coverage — one review per system (alphabetically)
2. Subsystem Coverage — one review per subsystem (grouped by parent)

**Checklist:**
- [ ] All system coverage reviews complete
- [ ] All subsystem coverage reviews complete
- [ ] User approves all reviews
- [ ] Status: COMPLETE

**Next:** Phase 13b

---

### PHASE 13b: Pass 0b — External Integrations Coverage
**Folder:** `13b. Pass-0b-External-Integrations/`

**Goal:** Review all external integrations for coverage completeness.

| Item | Value |
|------|-------|
| **Prompt** | `Pass-0b-External-Integrations/PASS-0b-PROMPT.md` |
| **Templates** | PROVIDER-COVERAGE-TEMPLATE.md, SERVICE-COVERAGE-TEMPLATE.md |
| **Input** | All PROVIDER-NOTES.md, SERVICE-NOTES.md |
| **Output** | `{{project}}/pass-0/PROVIDER-COVERAGE-*.md`, `{{project}}/pass-0/SERVICE-COVERAGE-*.md` |

**Skip if:** No external integrations identified (mark as N/A)

**Process:**
1. Provider Coverage — one review per provider (alphabetically)
2. Service Coverage — one review per service (grouped by provider)

**Checklist:**
- [ ] All provider coverage reviews complete (or N/A)
- [ ] All service coverage reviews complete (or N/A)
- [ ] User approves all reviews
- [ ] Status: COMPLETE

**Next:** Phase 13c

---

### PHASE 13c: Pass 0 — Checklist
**Folder:** `13c. Pass-0-Checklist/`

**Goal:** Verify Pass 0 is complete before proceeding to Pass 1.

| Item | Value |
|------|-------|
| **Prompt** | `Pass-0-Checklist/PASS-0-CHECKLIST-PROMPT.md` |
| **Template** | PASS-0-CHECKLIST-TEMPLATE.md |
| **Input** | All Pass 0a and 0b coverage reviews |
| **Output** | `{{project}}/pass-0/PASS-0-CHECKLIST.md` |

**Process:**
1. List all coverage documents created
2. Verify all are APPROVED
3. Summarize risks identified
4. Get final approval

**Checklist:**
- [ ] All Pass 0a reviews listed and approved
- [ ] All Pass 0b reviews listed and approved (or N/A)
- [ ] Risks summarized
- [ ] User approves Pass 0 Checklist
- [ ] Status: COMPLETE

**Next:** Phase 13d

---

### PHASE 13d: Pass 0d — Internal Integrations ID
**Folder:** `13d. Pass-0d-Internal-Integrations/`

**Goal:** Identify which systems talk to each other internally.

| Item | Value |
|------|-------|
| **Prompt** | `13d. Pass-0d-Internal-Integrations/PASS-0d-PROMPT.md` |
| **Template** | INTERNAL-INTEGRATIONS-TEMPLATE.md |
| **Input** | All SYSTEM-NOTES.md (inputs/outputs sections) |
| **Output** | `{{project}}/pass-0/INTERNAL-INTEGRATIONS.md` |

**Process:**
1. Review each system's inputs and outputs
2. Map system-to-system connections
3. Document what data flows between them
4. Note direction (one-way or bi-directional)

**Checklist:**
- [ ] Update PASS-PROGRESS.md: Phase 13d ACTIVE
- [ ] All internal integrations identified
- [ ] Integration matrix created
- [ ] User approves INTERNAL-INTEGRATIONS.md
- [ ] Update PASS-PROGRESS.md: Phase 13d COMPLETE

**Next:** Phase 14

---

### PHASE 14: Pass 1 — Grounding
**Folder:** `14. Pass-1-Grounding/`

**Goal:** Establish clear boundaries and ownership for each component.

| Item | Value |
|------|-------|
| **Prompt** | `14. Pass-1-Grounding/PASS-1-PROMPT.md` |
| **Templates** | SYSTEM-TEMPLATE.md, SUBSYSTEM-TEMPLATE.md, EXTERNAL-INTEGRATION-TEMPLATE.md, ADMIN-TEMPLATE.md |
| **Input** | All *-NOTES.md + Pass 0 coverage reviews |
| **Output** | SYSTEM.md, SUBSYSTEM.md, EXTERNAL-INTEGRATION.md, ADMIN.md in component folders |

**Process:**
1. Systems — create SYSTEM.md + ADMIN.md for each (alphabetically)
2. Subsystems — create SUBSYSTEM.md + ADMIN.md for each (grouped by parent)
3. External Integrations — create EXTERNAL-INTEGRATION.md + ADMIN.md for each service

**Checklist:**
- [ ] Update PASS-PROGRESS.md: Phase 14 ACTIVE
- [ ] All systems have SYSTEM.md + ADMIN.md
- [ ] All subsystems have SUBSYSTEM.md + ADMIN.md
- [ ] All external integrations have EXTERNAL-INTEGRATION.md + ADMIN.md (or N/A)
- [ ] User approves all grounding docs
- [ ] Update PASS-PROGRESS.md: Phase 14 COMPLETE
- [ ] Status: COMPLETE

**Next:** Phase 15

---

### PHASE 14: Pass 1 — Grounding
**Folder:** `14. Pass-1-Grounding/`

**Goal:** Establish clear boundaries and ownership for each component.

| Item | Value |
|------|-------|
| **Prompt** | `Pass-1-Grounding/PASS-1-PROMPT.md` |
| **Templates** | SYSTEM-TEMPLATE.md, SUBSYSTEM-TEMPLATE.md, EXTERNAL-INTEGRATION-TEMPLATE.md, ADMIN-TEMPLATE.md |
| **Input** | All *-NOTES.md + Phase 13 coverage reviews |
| **Output** | SYSTEM.md, SUBSYSTEM.md, EXTERNAL-INTEGRATION.md, ADMIN.md in component folders |

**Process:**
1. For each system → Create SYSTEM.md + ADMIN.md
2. For each subsystem → Create SUBSYSTEM.md + ADMIN.md
3. For each external integration → Create EXTERNAL-INTEGRATION.md + ADMIN.md

**Checklist:**
- [ ] All systems have SYSTEM.md + ADMIN.md
- [ ] All subsystems have SUBSYSTEM.md + ADMIN.md
- [ ] All external integrations have EXTERNAL-INTEGRATION.md + ADMIN.md
- [ ] User approves all grounding docs
- [ ] Status: COMPLETE

**Next:** Phase 15

---

### PHASE 15: Pass 2 — Contracts
**Folder:** `15. Pass-2-Contracts/`

**Goal:** Define how components communicate with each other.

| Item | Value |
|------|-------|
| **Prompt** | `Pass-2-Contracts/PASS-2-PROMPT.md` |
| **Template** | CONTRACT-TEMPLATE.md |
| **Input** | All grounding docs from Phase 14 |
| **Output** | CONTRACT.md in each component folder |

**Process:**
1. For each system → Define input/output contracts
2. For each subsystem → Define internal contracts
3. For each external integration → Define external contracts
4. Define error contracts for each boundary

**Checklist:**
- [ ] All systems have CONTRACT.md
- [ ] All subsystems have CONTRACT.md
- [ ] All external integrations have CONTRACT.md
- [ ] User approves all contracts
- [ ] Status: COMPLETE

**Next:** Phase 16

---

### PHASE 16: Pass 3 — Dependencies
**Folder:** `16. Pass-3-Dependencies/`

**Goal:** Map what depends on what and determine implementation order.

| Item | Value |
|------|-------|
| **Prompt** | `Pass-3-Dependencies/PASS-3-PROMPT.md` |
| **Template** | DEPENDENCY-MAP-TEMPLATE.md |
| **Input** | All contracts from Phase 15 |
| **Output** | `{{project}}/admin/DEPENDENCY-MAP.md` |

**Process:**
1. Build dependency graph for all components
2. Identify circular dependencies (problems)
3. Determine implementation order (leaves first)
4. Identify critical path

**Checklist:**
- [ ] Dependency graph complete
- [ ] No unresolved circular dependencies
- [ ] Implementation order defined
- [ ] User approves dependency map
- [ ] Status: COMPLETE

**Next:** Phase 17

---

### PHASE 17: Pass 4 — Implementation Planning
**Folder:** `17. Pass-4-Implementation-Planning/`

**Goal:** Plan the actual build — files, estimates, tasks.

| Item | Value |
|------|-------|
| **Prompt** | `Pass-4-Implementation-Planning/PASS-4-PROMPT.md` |
| **Template** | IMPLEMENTATION-PLAN-TEMPLATE.md |
| **Input** | Dependency map from Phase 16 + all component docs |
| **Output** | `{{project}}/admin/IMPLEMENTATION-PLAN.md` |

**Process:**
1. Define file structure for each component
2. Estimate LOC per file
3. Break into implementable tasks
4. Assign task order based on dependencies

**Checklist:**
- [ ] File structure defined for all components
- [ ] LOC estimates for all files
- [ ] Tasks broken down
- [ ] Task order assigned
- [ ] User approves implementation plan
- [ ] Status: COMPLETE

**Next:** Phase 18

---

### PHASE 18: Post-Pass Checklist
**Folder:** `18. Post-Pass-Checklist/`

**Goal:** Verify all Pass 0-4 phases are complete before distributing notes.

| Item | Value |
|------|-------|
| **Prompt** | `18. Post-Pass-Checklist/POST-PASS-CHECKLIST-PROMPT.md` |
| **Template** | POST-PASS-CHECKLIST-TEMPLATE.md |
| **Input** | All pass outputs |
| **Output** | Verification in `{{project}}/admin/` |

**Process:**
1. Verify Pass 0 complete (13a-d)
2. Verify Pass 1 complete (all grounding docs)
3. Verify Pass 2 complete (all contracts)
4. Verify Pass 3 complete (dependency map)
5. Verify Pass 4 complete (implementation plan)

**Checklist:**
- [ ] All passes verified complete
- [ ] No blocking issues
- [ ] Ready for distribution

**Next:** Phase 19

---

### PHASE 19: Pass Notes Distribution
**Folder:** `19. Pass-Notes-Distribution/`

**Goal:** Distribute relevant pass info into each component folder for context.

| Item | Value |
|------|-------|
| **Prompt** | `19. Pass-Notes-Distribution/PASS-NOTES-DISTRIBUTION-PROMPT.md` |
| **Template** | PASS-NOTES-TEMPLATE.md |
| **Input** | All pass-0 coverage, DEPENDENCY-MAP.md, IMPLEMENTATION-PLAN.md |
| **Output** | PASS-NOTES.md in each component folder |

**Process:**
1. For each system — extract relevant info → create PASS-NOTES.md
2. For each subsystem — extract relevant info → create PASS-NOTES.md
3. For each external service — extract relevant info → create PASS-NOTES.md

**Checklist:**
- [ ] Every system has PASS-NOTES.md
- [ ] Every subsystem has PASS-NOTES.md
- [ ] Every external service has PASS-NOTES.md
- [ ] All PASS-NOTES.md approved

**Next:** `0. Admin/0c. Full Specs/`


---

## Output Locations (During Actual Project)

| Phase | Output | Location |
|-------|--------|----------|
| 13 | Coverage reviews | `{{project}}/pass-0/` |
| 14 | SYSTEM.md, ADMIN.md | `{{project}}/{{system}}/` |
| 14 | SUBSYSTEM.md, ADMIN.md | `{{project}}/{{system}}/{{subsystem}}/` |
| 14 | EXTERNAL-INTEGRATION.md, ADMIN.md | `{{project}}/integration-{{provider}}/{{service}}/` |
| 15 | CONTRACT.md | Each component folder |
| 16 | DEPENDENCY-MAP.md | `{{project}}/admin/` |
| 17 | IMPLEMENTATION-PLAN.md | `{{project}}/admin/` |

---

## File Index by Phase

| Phase | Pass | Folder | Files |
|-------|------|--------|-------|
| 13a | 0a | `13a. Pass-0a-Systems-Subsystems/` | PASS-0a-PROMPT.md, SYSTEM-COVERAGE-TEMPLATE.md, SUBSYSTEM-COVERAGE-TEMPLATE.md |
| 13b | 0b | `13b. Pass-0b-External-Integrations/` | PASS-0b-PROMPT.md, PROVIDER-COVERAGE-TEMPLATE.md, SERVICE-COVERAGE-TEMPLATE.md |
| 13c | 0 | `13c. Pass-0-Checklist/` | PASS-0-CHECKLIST-PROMPT.md, PASS-0-CHECKLIST-TEMPLATE.md |
| 13d | 0d | `13d. Pass-0d-Internal-Integrations/` | PASS-0d-PROMPT.md, INTERNAL-INTEGRATIONS-TEMPLATE.md |
| 14 | 1 | `14. Pass-1-Grounding/` | PASS-1-PROMPT.md, SYSTEM-TEMPLATE.md, SUBSYSTEM-TEMPLATE.md, EXTERNAL-INTEGRATION-TEMPLATE.md, ADMIN-TEMPLATE.md |
| 15 | 2 | `15. Pass-2-Contracts/` | PASS-2-PROMPT.md, CONTRACT-TEMPLATE.md |
| 16 | 3 | `16. Pass-3-Dependencies/` | PASS-3-PROMPT.md, DEPENDENCY-MAP-TEMPLATE.md |
| 17 | 4 | `17. Pass-4-Implementation-Planning/` | PASS-4-PROMPT.md, IMPLEMENTATION-PLAN-TEMPLATE.md |
| 18 | — | `18. Post-Pass-Checklist/` | POST-PASS-CHECKLIST-PROMPT.md, POST-PASS-CHECKLIST-TEMPLATE.md |
| 19 | — | `19. Pass-Notes-Distribution/` | PASS-NOTES-DISTRIBUTION-PROMPT.md, PASS-NOTES-TEMPLATE.md |

---

## What's Next After Pass Phases?

When Phase 17 shows **COMPLETE**, proceed to:

**Location:** `0. Admin/0c. Full Specs/`

Full Specs will cover:
- Pre-spec notes
- Full implementation specs
- Schemas & field mappings
- Error codes & constants
- API specs
- Hardening
