# Section 0a: Complete Document Reference

---
**Section:** 0a Concept to PassPhase
**Phases:** 1-12
**Location:** `0. Admin/0a. Concept-to-PassPhases/` + Phase folders
---

## Admin Folder Documents

Location: `0. Admin/0a. Concept-to-PassPhases/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-prebuild.md** | Instructions for Claude on how to work through this section. Defines Claude's role, rules, and workflow. | Claude reads this |
| **Master-Build-Guide 1to12.md** | Detailed guide explaining each phase, what inputs it needs, what outputs it creates, and the order to follow. | Reference for both |

---

## Phase-by-Phase Documents

### Phase 1: Project Overview

**Folder:** `1. Project Overview/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-1-PROMPT.md** | Instructions for Claude. Tells Claude to gather project information from you and create the overview. | Give to Claude first |
| **PROJECT-OVERVIEW-TEMPLATE.md** | Template Claude fills in. Has sections for project name, description, goals, users, features. | Claude uses this to create PROJECT-OVERVIEW.md |

**Output:** `PROJECT-OVERVIEW.md`

---

### Phase 2: Project Intention

**Folder:** `2. Project Intention/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-2-PROMPT.md** | Instructions for Claude. Tells Claude to define success criteria and project boundaries. | Give to Claude |
| **PROJECT-INTENTION-TEMPLATE.md** | Template with sections for success metrics, scope boundaries, constraints, out-of-scope items. | Claude uses this |

**Output:** `PROJECT-INTENTION.md`

---

### Phase 3: System Identification

**Folder:** `3. System Identification/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-3-PROMPT.md** | Instructions for Claude. Tells Claude to identify the main systems needed for the project. | Give to Claude |
| **SYSTEM-NOTES-TEMPLATE.md** | Template for capturing initial system ideas - name, purpose, rough responsibilities. | Claude uses this |

**Output:** `SYSTEM-NOTES.md`

---

### Phase 4: Systems Pre-Build Check

**Folder:** `4. Systems Pre-Build Check/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-4-PROMPT.md** | Instructions for Claude. Tells Claude to review the systems list and ask clarifying questions. | Give to Claude |
| **SYSTEMS-CLARIFICATION-TEMPLATE.md** | Template for listing questions and getting your answers before building detailed system docs. | Claude fills, you answer |

**Output:** `SYSTEMS-CLARIFICATION.md`

---

### Phase 5: Systems Build

**Folder:** `5. Systems Build/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-5-PROMPT.md** | Instructions for Claude. Tells Claude to create detailed documentation for each system. | Give to Claude |
| **SYSTEM-TEMPLATE.md** | Template for individual system docs - purpose, responsibilities, boundaries, interfaces. | Claude uses this for each system |

**Output:** `SYSTEM-{name}.md` (one per system)

---

### Phase 6: Subsystems Identification

**Folder:** `6. Subsystems Identification/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-6-PROMPT.md** | Instructions for Claude. Tells Claude to break each system into smaller subsystems. | Give to Claude |
| **SUBSYSTEM-NOTES-TEMPLATE.md** | Template for capturing subsystem ideas - which system it belongs to, purpose, scope. | Claude uses this |

**Output:** `SUBSYSTEM-NOTES.md`

---

### Phase 7: Subsystems Pre-Build Check

**Folder:** `7. Subsystems Pre-Build Check/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-7-PROMPT.md** | Instructions for Claude. Tells Claude to review subsystems and ask clarifying questions. | Give to Claude |
| **SUBSYSTEMS-CLARIFICATION-TEMPLATE.md** | Template for questions about subsystem breakdown. | Claude fills, you answer |

**Output:** `SUBSYSTEMS-CLARIFICATION.md`

---

### Phase 8: Subsystem Build

**Folder:** `8. Subsystem Build/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-8-PROMPT.md** | Instructions for Claude. Tells Claude to create detailed documentation for each subsystem. | Give to Claude |
| **SUBSYSTEM-TEMPLATE.md** | Template for individual subsystem docs - purpose, parent system, responsibilities. | Claude uses this for each subsystem |

**Output:** `SUBSYSTEM-{name}.md` (one per subsystem)

---

### Phase 9: External Integration Provider Build

**Folder:** `9. External Integration Provider Build/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-9-PROMPT.md** | Instructions for Claude. Tells Claude to identify external services you'll connect to (Stripe, AWS, etc.). | Give to Claude |
| **PROVIDER-NOTES-TEMPLATE.md** | Template for listing providers - name, what you'll use them for, why this provider. | Claude uses this |

**Output:** `PROVIDER-NOTES.md`

---

### Phase 10: External Integration Service Scope

**Folder:** `10. External Integration Service Scope/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-10-PROMPT.md** | Instructions for Claude. Tells Claude to define exactly what services you need from each provider. | Give to Claude |
| **SERVICE-SCOPE-TEMPLATE.md** | Template for scoping services - what features you'll use, what you won't use, limits. | Claude uses this |

**Output:** `SERVICE-SCOPE.md`

---

### Phase 11: External Integration Service Build

**Folder:** `11. External Integration Service Build/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-11-PROMPT.md** | Instructions for Claude. Tells Claude to document each external service integration in detail. | Give to Claude |
| **SERVICE-NOTES-TEMPLATE.md** | Template for service details - authentication, endpoints you'll use, data flow. | Claude uses this |

**Output:** `SERVICE-NOTES.md`

---

### Phase 12: PrePass Check

**Folder:** `12. PrePass Check/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-12-PROMPT.md** | Instructions for Claude. Tells Claude to verify everything is ready for the next section. | Give to Claude |
| **PREPASS-CHECKLIST-TEMPLATE.md** | Checklist template - all systems documented? All subsystems? All integrations? Any gaps? | Claude fills this |

**Output:** `PREPASS-CHECKLIST.md`

---

## Order to Run Section 0a

```
Step 1:  Read Claude-prebuild.md (understand section)
Step 2:  Phase 1 - Create PROJECT-OVERVIEW.md
Step 3:  Phase 2 - Create PROJECT-INTENTION.md
Step 4:  Phase 3 - Create SYSTEM-NOTES.md
Step 5:  Phase 4 - Review and clarify systems
Step 6:  Phase 5 - Create SYSTEM-*.md files
Step 7:  Phase 6 - Create SUBSYSTEM-NOTES.md
Step 8:  Phase 7 - Review and clarify subsystems
Step 9:  Phase 8 - Create SUBSYSTEM-*.md files
Step 10: Phase 9 - Create PROVIDER-NOTES.md
Step 11: Phase 10 - Create SERVICE-SCOPE.md
Step 12: Phase 11 - Create SERVICE-NOTES.md
Step 13: Phase 12 - Complete PREPASS-CHECKLIST.md
Step 14: (Optional) Run 0a audit
Step 15: Proceed to Section 0b
```

---

## What You Should Have When Done

| Document | Location | Count |
|----------|----------|-------|
| PROJECT-OVERVIEW.md | Phase 1 folder | 1 |
| PROJECT-INTENTION.md | Phase 2 folder | 1 |
| SYSTEM-NOTES.md | Phase 3 folder | 1 |
| SYSTEMS-CLARIFICATION.md | Phase 4 folder | 1 |
| SYSTEM-*.md | Phase 5 folder | 1 per system |
| SUBSYSTEM-NOTES.md | Phase 6 folder | 1 |
| SUBSYSTEMS-CLARIFICATION.md | Phase 7 folder | 1 |
| SUBSYSTEM-*.md | Phase 8 folder | 1 per subsystem |
| PROVIDER-NOTES.md | Phase 9 folder | 1 |
| SERVICE-SCOPE.md | Phase 10 folder | 1 |
| SERVICE-NOTES.md | Phase 11 folder | 1 |
| PREPASS-CHECKLIST.md | Phase 12 folder | 1 |

---

*Give PHASE-X-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
