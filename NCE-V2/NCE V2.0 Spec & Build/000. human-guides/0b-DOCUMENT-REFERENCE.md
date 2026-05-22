# Section 0b: Complete Document Reference

---
**Section:** 0b PassPhases 0-4
**Phases:** 13-19
**Location:** `0. Admin/0b. PassPhases 0-4/` + Phase folders
---

## Admin Folder Documents

Location: `0. Admin/0b. PassPhases 0-4/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-passphases.md** | Instructions for Claude. Defines Claude's role as systems architect, explains each pass, lists rules. | Claude reads this |
| **Master-Pass-Guide.md** | Detailed guide for all passes. Explains each phase, inputs, outputs, and the full workflow. | Reference for both |
| **PASS-PROGRESS-TEMPLATE.md** | Template to track progress through passes. Copy this and fill in as you complete phases. | You fill this in |
| **PASS-DECISION-NOTES-TEMPLATE.md** | Template to log decisions made during passes. Copy and add entries when you make choices. | You fill this in |
| **PASS-RESTART-RULES.md** | Rules for when to restart a pass. Explains when issues require going back to earlier phases. | Reference for both |

---

## Phase-by-Phase Documents

### Phase 13a: Pass 0a — Systems & Subsystems Coverage

**Folder:** `13a. Pass-0a-Systems-Subsystems/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-0a-PROMPT.md** | Instructions for Claude. Tells Claude to review all systems and subsystems to check if anything is missing. | Give to Claude |
| **SYSTEM-COVERAGE-TEMPLATE.md** | Template for reviewing one system - what it covers, any gaps, any overlaps. | Claude creates one per system |
| **SUBSYSTEM-COVERAGE-TEMPLATE.md** | Template for reviewing one subsystem - what it covers, any gaps. | Claude creates one per subsystem |

**Outputs:** 
- `SYSTEM-COVERAGE-{name}.md` (one per system)
- `SUBSYSTEM-COVERAGE-{name}.md` (one per subsystem)

---

### Phase 13b: Pass 0b — External Integrations Coverage

**Folder:** `13b. Pass-0b-External-Integrations/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-0b-PROMPT.md** | Instructions for Claude. Tells Claude to review all external integrations for completeness. | Give to Claude |
| **PROVIDER-COVERAGE-TEMPLATE.md** | Template for reviewing one provider - services used, any gaps. | Claude creates one per provider |
| **SERVICE-COVERAGE-TEMPLATE.md** | Template for reviewing one service - features used, any missing. | Claude creates one per service |

**Outputs:**
- `PROVIDER-COVERAGE-{name}.md` (one per provider)
- `SERVICE-COVERAGE-{name}.md` (one per service)

---

### Phase 13c: Pass 0 Checklist

**Folder:** `13c. Pass-0-Checklist/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-0-CHECKLIST-PROMPT.md** | Instructions for Claude. Tells Claude to verify all Pass 0 coverage reviews are complete. | Give to Claude |
| **PASS-0-CHECKLIST-TEMPLATE.md** | Checklist template - all systems reviewed? All subsystems? All integrations? | Claude fills this |

**Output:** `PASS-0-CHECKLIST.md`

---

### Phase 13d: Pass 0d — Internal Integrations

**Folder:** `13d. Pass-0d-Internal-Integrations/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-0d-PROMPT.md** | Instructions for Claude. Tells Claude to map how your internal systems connect to each other. | Give to Claude |
| **INTERNAL-INTEGRATIONS-TEMPLATE.md** | Template showing which systems talk to which, data that flows between them. | Claude fills this |

**Output:** `INTERNAL-INTEGRATIONS.md`

---

### Phase 14: Pass 1 — Grounding

**Folder:** `14. Pass-1-Grounding/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-1-PROMPT.md** | Instructions for Claude. Tells Claude to create detailed grounding documents for each component. | Give to Claude |
| **SYSTEM-TEMPLATE.md** | Full system definition - identity, ownership, boundaries, guarantees, constraints. | Claude creates one per system |
| **SUBSYSTEM-TEMPLATE.md** | Full subsystem definition - parent system, scope, responsibilities. | Claude creates one per subsystem |
| **EXTERNAL-INTEGRATION-TEMPLATE.md** | Full integration definition - provider, service, what it does for you. | Claude creates one per integration |
| **ADMIN-TEMPLATE.md** | Administrative info - owner, status, version, change log. | Claude creates one per component |

**Outputs (for each component):**
- `SYSTEM.md` or `SUBSYSTEM.md` or `EXTERNAL-INTEGRATION.md`
- `ADMIN.md`

---

### Phase 15: Pass 2 — Contracts

**Folder:** `15. Pass-2-Contracts/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-2-PROMPT.md** | Instructions for Claude. Tells Claude to define how each component communicates with others. | Give to Claude |
| **CONTRACT-TEMPLATE.md** | Contract definition - what data goes in, what comes out, error conditions. | Claude creates one per component |

**Output:** `CONTRACT.md` (one per component)

---

### Phase 16: Pass 3 — Dependencies

**Folder:** `16. Pass-3-Dependencies/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-3-PROMPT.md** | Instructions for Claude. Tells Claude to map what depends on what and find the build order. | Give to Claude |
| **DEPENDENCY-MAP-TEMPLATE.md** | Dependency graph showing relationships, build order, any circular dependencies (problems!). | Claude fills this |

**Output:** `DEPENDENCY-MAP.md`

---

### Phase 17: Pass 4 — Implementation Planning

**Folder:** `17. Pass-4-Implementation-Planning/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-4-PROMPT.md** | Instructions for Claude. Tells Claude to plan the implementation - files, sizes, tasks. | Give to Claude |
| **IMPLEMENTATION-PLAN-TEMPLATE.md** | Build plan - file structure per component, estimated lines of code, task breakdown. | Claude fills this |

**Output:** `IMPLEMENTATION-PLAN.md`

---

### Phase 18: Post-Pass Checklist

**Folder:** `18. Post-Pass-Checklist/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **POST-PASS-CHECKLIST-PROMPT.md** | Instructions for Claude. Tells Claude to verify all passes are complete. | Give to Claude |
| **POST-PASS-CHECKLIST-TEMPLATE.md** | Final checklist - Pass 0 done? Pass 1? Pass 2? Pass 3? Pass 4? Any blockers? | Claude fills this |

**Output:** `POST-PASS-CHECKLIST.md`

---

### Phase 19: Pass Notes Distribution

**Folder:** `19. Pass-Notes-Distribution/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PASS-NOTES-DISTRIBUTION-PROMPT.md** | Instructions for Claude. Tells Claude to create context summaries for each component. | Give to Claude |
| **PASS-NOTES-TEMPLATE.md** | Context summary - key decisions, dependencies, constraints relevant to this component. | Claude creates one per component |

**Output:** `PASS-NOTES.md` (one per component)

---

## Order to Run Section 0b

```
Step 1:  Read Claude-passphases.md (understand section)
Step 2:  Create PASS-PROGRESS.md from template
Step 3:  Phase 13a - Create coverage reviews for all systems/subsystems
Step 4:  Phase 13b - Create coverage reviews for all integrations
Step 5:  Phase 13c - Complete PASS-0-CHECKLIST.md
Step 6:  Phase 13d - Create INTERNAL-INTEGRATIONS.md
Step 7:  Phase 14 - Create SYSTEM.md + ADMIN.md for each component
Step 8:  Phase 15 - Create CONTRACT.md for each component
Step 9:  Phase 16 - Create DEPENDENCY-MAP.md
         ⚠️ If circular dependencies found, STOP and fix!
Step 10: Phase 17 - Create IMPLEMENTATION-PLAN.md
Step 11: Phase 18 - Complete POST-PASS-CHECKLIST.md
Step 12: Phase 19 - Create PASS-NOTES.md for each component
Step 13: (Recommended) Run 0b audit
Step 14: Proceed to Section 0c
```

---

## What You Should Have When Done

| Document | Location | Count |
|----------|----------|-------|
| PASS-PROGRESS.md | Admin folder | 1 |
| SYSTEM-COVERAGE-*.md | Phase 13a | 1 per system |
| SUBSYSTEM-COVERAGE-*.md | Phase 13a | 1 per subsystem |
| PROVIDER-COVERAGE-*.md | Phase 13b | 1 per provider |
| SERVICE-COVERAGE-*.md | Phase 13b | 1 per service |
| PASS-0-CHECKLIST.md | Phase 13c | 1 |
| INTERNAL-INTEGRATIONS.md | Phase 13d | 1 |
| SYSTEM.md | Each system folder | 1 per system |
| SUBSYSTEM.md | Each subsystem folder | 1 per subsystem |
| EXTERNAL-INTEGRATION.md | Each integration folder | 1 per integration |
| ADMIN.md | Each component folder | 1 per component |
| CONTRACT.md | Each component folder | 1 per component |
| DEPENDENCY-MAP.md | Admin folder | 1 |
| IMPLEMENTATION-PLAN.md | Admin folder | 1 |
| POST-PASS-CHECKLIST.md | Admin folder | 1 |
| PASS-NOTES.md | Each component folder | 1 per component |

---

*Give PASS-X-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
