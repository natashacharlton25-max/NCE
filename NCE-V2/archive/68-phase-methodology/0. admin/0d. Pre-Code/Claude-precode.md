# CLAUDE.md — PreCode Phase

---
Version: v1.0.0
Section: 0d. PreCode
Phases: 29-36
Purpose: Instructions for Claude to execute the PreCode workflow
---

## Your Role

You are a **consolidator and infrastructure preparer**. You take the distributed specs from 0c and consolidate them into unified artifacts ready for implementation.

This is the bridge between specification and code. By the end of this section, everything is ready to build.

---

## What's Already Done (DO NOT CHANGE)

| From | What's Locked |
|------|---------------|
| 0a (Phases 1-12) | Systems, subsystems, integrations identified |
| 0b (Phases 13-19) | Passes complete, PASS-NOTES.md distributed |
| 0c (Phases 20-28) | Full specs for every component |
| Phase 25 | POST-SPEC-ANALYSIS (gaps, patterns identified) |
| Phase 26 | PROJECT-WIDE-SPECS (schemas, error codes, standards) |

**You cannot change architecture or specs.** PreCode consolidates and prepares infrastructure.

---

## Critical Rules

### Rule 1: No Fix Forward

If consolidation (Phases 29 or 30) reveals conflicts:

1. **STOP** — Do not attempt to resolve in PreCode
2. **DOCUMENT** — Log the conflict in PRECODE-DECISION-NOTES.md
3. **ROLL BACK** — Return to the relevant spec in 0c
4. **FIX AT SOURCE** — Update the spec, get approval
5. **RESUME** — Continue PreCode from where you stopped

**Why:** Fixing forward creates hidden divergence. Specs are the source of truth.

---

### Rule 2: No Behavioural Inference

During API consolidation (Phase 30):
- **Do NOT infer API behaviour beyond what is explicitly stated in FUNCTIONS.md**
- If behaviour is unclear, flag it as a gap
- Do NOT "helpfully" fill in missing details

**Why:** Inference creates phantom specs. Only documented behaviour exists.

---

### Rule 3: Explicit Naming Convention

During schema consolidation (Phase 29):
- **Default naming:** `{component}_{entity}` (e.g., `auth_users`, `content_posts`)
- **Exceptions must be logged** in PRECODE-DECISION-NOTES.md with justification
- No silent deviations from this pattern

**Why:** Consistent naming prevents collision and confusion across contributors.

---

### Rule 4: File Size Limits

During standards definition (Phase 35):
- **Soft target:** ~300 LOC per file
- **Hard stop:** 500 LOC → refactor required before proceeding
- **Component cap:** ~1,500 LOC total

**Why:** LLM context limits require small, focused files.

---

## Approval Gates

**Approval gates are ABSOLUTE. Do not skip phases.**

| Phase | Gate | Cannot Proceed Without |
|-------|------|------------------------|
| 29 | Database schema approved | User says "APPROVE" |
| 30 | API surface approved | User says "APPROVE" |
| 31 | Library structure approved (or N/A) | User says "APPROVE" or confirms N/A |
| 32 | Repo structure approved | User says "APPROVE" |
| 33 | Tech stack approved | User says "APPROVE" |
| 34 | Environment config approved | User says "APPROVE" |
| 35 | Coding standards approved | User says "APPROVE" |
| 36 | Final checklist approved | User says "APPROVE" |

**If user says "REVISE":** Make changes, re-present, wait for "APPROVE".

---

## Log Every "Why"

Use PRECODE-DECISION-NOTES.md to document:

| What to Log | Why It Matters |
|-------------|----------------|
| Database type choice | Future maintainers need to understand the trade-offs |
| Framework selection | Prevents "why didn't we use X?" debates later |
| Naming convention exceptions | Explains deviations from standard pattern |
| Tech stack alternatives rejected | Documents what was considered |
| Any conflict and its resolution | Creates audit trail |

**Six months from now, these notes are the only way to understand "Why".**

---

## Your Workflow

### Before Starting

1. **Verify 0c is complete** — Phase 28 checklist must be APPROVED
2. **Read Phase 25 outputs** — POST-SPEC-ANALYSIS files show gaps and patterns
3. **Read Phase 26 outputs** — PROJECT-WIDE-SPECS for standards

### For Each Phase

1. Read the PROMPT file in the phase folder
2. Gather required inputs (STORAGE.md files, FUNCTIONS.md files, etc.)
3. Execute according to the prompt
4. Output using the TEMPLATE file(s)
5. Check for conflicts — if found, STOP and roll back
6. Ask user for approval
7. If approved → mark complete, proceed
8. If revision needed → make changes, re-present

---

## Phase Overview

| Phase | Name | What You Do |
|-------|------|-------------|
| 29 | Database Schema Consolidation | Extract all STORAGE.md → master DATABASE-SCHEMA.md |
| 30 | API Surface Consolidation | Extract all FUNCTIONS.md → master API-SURFACE.md |
| 31 | Library Structure | Set up shared library folders from Phase 23 specs |
| 32 | Repo Setup | Define complete folder structure for implementation |
| 33 | Tech Stack | Document project-specific tech choices |
| 34 | Environment | Define dev/staging/prod configuration |
| 35 | Coding Standards | Establish style guides, naming, LLM-friendly patterns |
| 36 | PreCode Ready Check | Final verification before frontend/implementation |

---

## Phase 29: Database-Type Awareness

Phase 29 must account for database type:

1. **Ask first:** "What database type will this project use?"
   - Relational (SQL): PostgreSQL, MySQL, SQLite, D1
   - Document (NoSQL): MongoDB, Firestore
   - Key-Value: Redis, DynamoDB
   - Graph: Neo4j

2. **Adapt schema format** based on answer:
   - SQL → Tables, columns, relationships, indexes
   - Document → Collections, document schemas, indexes
   - Key-Value → Key patterns, value schemas
   - Graph → Nodes, relationships, properties

3. **If unknown:** Produce schema in neutral format, flag for Tech Stack decision

---

## Inputs You Need

| Phase | Primary Inputs |
|-------|----------------|
| 29 | All `{{component}}/spec/STORAGE.md` files |
| 30 | All `{{component}}/spec/FUNCTIONS.md` files |
| 31 | `lib-{{library}}/spec/` files from Phase 23 |
| 32 | All spec INDEX.md files, IMPLEMENTATION-PLAN.md |
| 33 | Project requirements, team preferences |
| 34 | Tech stack decisions from Phase 33 |
| 35 | Tech stack, team preferences, LLM constraints |
| 36 | All PreCode outputs |

---

## Conflict Detection

When consolidating, watch for:

| Conflict Type | Example | Action |
|---------------|---------|--------|
| Naming collision | Two components use same table name | STOP, roll back |
| Type mismatch | Component A says string, B says number | STOP, roll back |
| Missing reference | Component A references table from B, but B doesn't define it | STOP, roll back |
| Circular dependency | A needs B's table, B needs A's table | STOP, roll back |
| Scope overlap | Two components claim same API endpoint | STOP, roll back |

**Document ALL conflicts before rolling back.** This helps fix at source.

---

## Conflict Resolution

If schema or API conflicts are discovered:
- See `FAILURE-RECOVERY-RULES.md` for rollback procedures
- Conflicts in Phase 29/30 require returning to 0c to fix source specs
- Document in PRECODE-DECISION-NOTES.md before rolling back
- Re-audit 0c is required after fixing spec conflicts

---

## Output Locations

```
{{project}}/
├── DATABASE-SCHEMA.md          ← Phase 29
├── MIGRATION-STRATEGY.md       ← Phase 29
├── API-SURFACE.md              ← Phase 30
├── LIBRARY-STRUCTURE.md        ← Phase 31
├── REPO-STRUCTURE.md           ← Phase 32
├── TECH-STACK.md               ← Phase 33
├── ENVIRONMENT.md              ← Phase 34
├── CODING-STANDARDS.md         ← Phase 35
├── PRECODE-CHECKLIST.md        ← Phase 36
│
└── 0. Admin/0d. PreCode/
    ├── PRECODE-PROGRESS.md     ← Track progress
    └── PRECODE-DECISION-NOTES.md ← Log decisions
```

---

## Approval Gates

| Phase | What's Verified |
|-------|-----------------|
| 29 | Database schema complete, no conflicts, migration strategy defined |
| 30 | API surface complete, no conflicts, all endpoints documented |
| 31 | Library structure defined (or N/A if no libraries) |
| 32 | Repo structure complete, all components have locations |
| 33 | Tech stack documented, justified |
| 34 | All environments configured |
| 35 | Coding standards complete, LLM-friendly rules included |
| 36 | All phases complete, ready for frontend/implementation |

**Do not proceed to next phase without user approval.**

---

## End Condition

0d PreCode is COMPLETE when:

- [ ] Phase 36 checklist passed
- [ ] DATABASE-SCHEMA.md approved
- [ ] API-SURFACE.md approved
- [ ] All infrastructure docs complete
- [ ] No unresolved conflicts
- [ ] User approves moving to 0e Frontend

---

## Reference

| Document | Location |
|----------|----------|
| Master Guide | `0. Admin/0d. PreCode/Master-PreCode-Guide 29to36.md` |
| Phase Prompts | `29. Database-Schema-Consolidation/PHASE-29-PROMPT.md` etc. |
| Templates | `29. Database-Schema-Consolidation/*-TEMPLATE.md` etc. |
| Progress | `0. Admin/0d. PreCode/PRECODE-PROGRESS.md` |
| Decisions | `0. Admin/0d. PreCode/PRECODE-DECISION-NOTES.md` |

---
