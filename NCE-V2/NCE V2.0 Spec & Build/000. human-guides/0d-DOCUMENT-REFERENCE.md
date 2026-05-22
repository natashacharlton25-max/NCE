# Section 0d: Complete Document Reference

---
**Section:** 0d PreCode
**Phases:** 29-36
**Location:** `0. Admin/0d. PreCode/` + Phase folders
---

## Admin Folder Documents

Location: `0. Admin/0d. PreCode/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-precode.md** | Instructions for Claude. Defines rules (no fix forward, no inference), consolidation process. | Claude reads this |
| **Master-PreCode-Guide 29to36.md** | Detailed guide for all phases. Explains inputs, outputs, conflict handling. | Reference for both |
| **PRECODE-PROGRESS-TEMPLATE.md** | Template to track progress. Copy and fill in as you complete phases. | You fill this in |
| **PRECODE-DECISION-NOTES-TEMPLATE.md** | Template to log decisions. Records tech choices, conflicts, rationale. | You fill this in |

---

## Phase-by-Phase Documents

### Phase 29: Database Schema Consolidation

**Folder:** `29. Database-Schema-Consolidation/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-29-PROMPT.md** | Instructions for Claude. Tells Claude to gather all STORAGE.md files and combine into one master schema. | Give to Claude |
| **DATABASE-SCHEMA-TEMPLATE.md** | Template for master database schema - tables, columns, relationships, indexes. | Claude creates DATABASE-SCHEMA.md |
| **MIGRATION-STRATEGY-TEMPLATE.md** | Template for how to handle schema changes over time. | Claude creates MIGRATION-STRATEGY.md |

**Outputs:**
- `DATABASE-SCHEMA.md` — All tables/collections in one place
- `MIGRATION-STRATEGY.md` — How to update schema later

**⚠️ If conflicts found:** STOP, go back to 0c, fix the specs, return.

---

### Phase 30: API Surface Consolidation

**Folder:** `30. API-Surface-Consolidation/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-30-PROMPT.md** | Instructions for Claude. Tells Claude to gather all FUNCTIONS.md files and combine into one API spec. | Give to Claude |
| **API-SURFACE-TEMPLATE.md** | Template for master API - all endpoints, request/response formats, error codes. | Claude creates API-SURFACE.md |

**Output:** `API-SURFACE.md` — All API endpoints in one place

**⚠️ If conflicts found:** STOP, go back to 0c, fix the specs, return.

---

### Phase 31: Library Structure

**Folder:** `31. Library-Structure/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-31-PROMPT.md** | Instructions for Claude. Tells Claude to define folder structure for shared libraries. | Give to Claude |
| **LIBRARY-STRUCTURE-TEMPLATE.md** | Template for library organization - folders, exports, dependencies between libraries. | Claude creates LIBRARY-STRUCTURE.md |

**Output:** `LIBRARY-STRUCTURE.md` — How shared code is organized

**Note:** May be N/A if no shared libraries were identified in Phase 23.

---

### Phase 32: Repo Setup

**Folder:** `32. Repo-Setup/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-32-PROMPT.md** | Instructions for Claude. Tells Claude to define the complete folder structure for the repository. | Give to Claude |
| **REPO-STRUCTURE-TEMPLATE.md** | Template for repo layout - where each component goes, naming conventions. | Claude creates REPO-STRUCTURE.md |

**Output:** `REPO-STRUCTURE.md` — Complete folder tree for implementation

---

### Phase 33: Tech Stack

**Folder:** `33. Tech-Stack/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-33-PROMPT.md** | Instructions for Claude. Tells Claude to document the technology choices for this project. | Give to Claude |
| **TECH-STACK-TEMPLATE.md** | Template for tech decisions - language, framework, database, hosting, why each was chosen. | Claude creates TECH-STACK.md |

**Output:** `TECH-STACK.md` — What technologies you're using and why

---

### Phase 34: Environment

**Folder:** `34. Environment/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-34-PROMPT.md** | Instructions for Claude. Tells Claude to define environment configurations. | Give to Claude |
| **ENVIRONMENT-TEMPLATE.md** | Template for environments - dev, staging, production settings, environment variables. | Claude creates ENVIRONMENT.md |

**Output:** `ENVIRONMENT.md` — Configuration for each environment

---

### Phase 35: Coding Standards

**Folder:** `35. Coding-Standards/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-35-PROMPT.md** | Instructions for Claude. Tells Claude to define coding conventions and rules. | Give to Claude |
| **CODING-STANDARDS-TEMPLATE.md** | Template for code rules - naming, formatting, file size limits, LLM-friendly patterns. | Claude creates CODING-STANDARDS.md |

**Output:** `CODING-STANDARDS.md` — How code should be written

**Key rules included:**
- ~300 lines per file (soft limit)
- ~500 lines per file (hard limit)
- ~1,500 lines per component (cap)

---

### Phase 36: PreCode Ready Check

**Folder:** `36. PreCode-Ready-Check/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-36-PROMPT.md** | Instructions for Claude. Tells Claude to verify everything is ready to start coding. | Give to Claude |
| **PRECODE-CHECKLIST-TEMPLATE.md** | Final checklist - all phases done? All conflicts resolved? Anything missing? | Claude creates PRECODE-CHECKLIST.md |

**Output:** `PRECODE-CHECKLIST.md` — Final verification

**Critical question:** "Is there anything required to start implementation that is not documented?"

---

## Order to Run Section 0d

```
Step 1:  Read Claude-precode.md (understand section)
Step 2:  Create PRECODE-PROGRESS.md from template
Step 3:  Phase 29 - Create DATABASE-SCHEMA.md + MIGRATION-STRATEGY.md
         ⚠️ If conflicts → STOP, go to 0c, fix, return
Step 4:  Phase 30 - Create API-SURFACE.md
         ⚠️ If conflicts → STOP, go to 0c, fix, return
Step 5:  Phase 31 - Create LIBRARY-STRUCTURE.md (or mark N/A)
Step 6:  Phase 32 - Create REPO-STRUCTURE.md
Step 7:  Phase 33 - Create TECH-STACK.md
Step 8:  Phase 34 - Create ENVIRONMENT.md
Step 9:  Phase 35 - Create CODING-STANDARDS.md
Step 10: Phase 36 - Create PRECODE-CHECKLIST.md
Step 11: (Optional) Run 0d audit
Step 12: Proceed to Section 0e
```

---

## Critical Rule: No Fix Forward

If Phase 29 or 30 finds conflicts between specs:

| Step | What To Do |
|------|------------|
| 1 | STOP immediately |
| 2 | Document conflict in PRECODE-DECISION-NOTES.md |
| 3 | Go back to Section 0c |
| 4 | Fix the conflicting spec at the source |
| 5 | Get approval for the fix |
| 6 | Return to 0d and continue |

**Why?** Specs are the source of truth. Fixing here without updating specs creates hidden divergence.

---

## What You Should Have When Done

| Document | Location |
|----------|----------|
| PRECODE-PROGRESS.md | Admin folder |
| DATABASE-SCHEMA.md | Project root |
| MIGRATION-STRATEGY.md | Project root |
| API-SURFACE.md | Project root |
| LIBRARY-STRUCTURE.md | Project root (or N/A) |
| REPO-STRUCTURE.md | Project root |
| TECH-STACK.md | Project root |
| ENVIRONMENT.md | Project root |
| CODING-STANDARDS.md | Project root |
| PRECODE-CHECKLIST.md | Project root |

---

*Give PHASE-XX-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
