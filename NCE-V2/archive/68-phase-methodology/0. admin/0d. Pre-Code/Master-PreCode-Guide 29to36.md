# Master PreCode Guide — Phases 29-36

---
Version: v1.0.0
Section: 0d. PreCode
Purpose: Complete workflow guide for the PreCode phase
---

## Overview

The PreCode phase consolidates distributed specs into unified artifacts and prepares infrastructure for implementation.

**Input:** Full specs from 0c (STORAGE.md, FUNCTIONS.md, etc.)
**Output:** Consolidated database schema, API surface, repo structure, tech stack, environment config, coding standards

---

## Prerequisites

Before starting PreCode, verify:

- [ ] Phase 28 (Full-Spec Checklist) is APPROVED
- [ ] All component specs are complete
- [ ] Phase 25 (Post-Spec Analysis) outputs available
- [ ] Phase 26 (Project-Wide Specs) outputs available
- [ ] No blocking issues from 0c

---

## Critical Rule: No Fix Forward

If any phase reveals conflicts between specs:

1. **STOP** immediately
2. **DOCUMENT** in PRECODE-DECISION-NOTES.md
3. **ROLL BACK** to the relevant spec in 0c
4. **FIX** the spec at source
5. **GET APPROVAL** for the fix
6. **RESUME** PreCode

**Never resolve conflicts by changing PreCode outputs.** Specs are the source of truth.

---

## Phase Summary

| Phase | Name | Output | Inputs |
|-------|------|--------|--------|
| 29 | Database Schema Consolidation | DATABASE-SCHEMA.md, MIGRATION-STRATEGY.md | All STORAGE.md files |
| 30 | API Surface Consolidation | API-SURFACE.md | All FUNCTIONS.md files |
| 31 | Library Structure | LIBRARY-STRUCTURE.md | Phase 23 library specs |
| 32 | Repo Setup | REPO-STRUCTURE.md | All INDEX.md, IMPLEMENTATION-PLAN.md |
| 33 | Tech Stack | TECH-STACK.md | Project requirements |
| 34 | Environment | ENVIRONMENT.md | Tech stack decisions |
| 35 | Coding Standards | CODING-STANDARDS.md | Tech stack, LLM constraints |
| 36 | PreCode Ready Check | PRECODE-CHECKLIST.md | All PreCode outputs |

---

## Phase 29: Database Schema Consolidation

**Purpose:** Extract all database definitions from component STORAGE.md files into a unified master schema.

**Input:**
- All `{{component}}/spec/STORAGE.md` files
- `standards/SCHEMAS.md` from Phase 26

**Output:**
- `DATABASE-SCHEMA.md` — Master schema with all tables/collections
- `MIGRATION-STRATEGY.md` — How to handle future schema changes

**Process:**

1. **Determine database type:**
   - Ask user or derive from project requirements
   - SQL (relational) / NoSQL (document) / Key-Value / Graph

2. **Gather all STORAGE.md files:**
   - List every component that has storage
   - Extract table/collection definitions

3. **Consolidate into master schema:**
   - Merge all definitions
   - Resolve naming (prefix with component if needed)
   - Define relationships/references between tables
   - Define indexes

4. **Check for conflicts:**
   - Naming collisions
   - Type mismatches
   - Missing references
   - If conflicts found → STOP, roll back

5. **Define migration strategy:**
   - How will schema changes be handled?
   - Versioning approach
   - Rollback procedures

6. **Get approval**

**Files:**
- `29. Database-Schema-Consolidation/PHASE-29-PROMPT.md`
- `29. Database-Schema-Consolidation/DATABASE-SCHEMA-TEMPLATE.md`
- `29. Database-Schema-Consolidation/MIGRATION-STRATEGY-TEMPLATE.md`

---

## Phase 30: API Surface Consolidation

**Purpose:** Extract all API definitions from component FUNCTIONS.md files into a unified API specification.

**Input:**
- All `{{component}}/spec/FUNCTIONS.md` files
- `standards/API-STANDARDS.md` from Phase 26
- `standards/ERROR-CODES.md` from Phase 26

**Output:**
- `API-SURFACE.md` — Complete API specification

**Process:**

1. **Gather all FUNCTIONS.md files:**
   - List every component that exposes APIs
   - Extract function/endpoint definitions

2. **Consolidate into master API spec:**
   - Group by domain/resource
   - Define routes/endpoints
   - Document request/response formats
   - Link to error codes

3. **Check for conflicts:**
   - Endpoint collisions (same path, different behavior)
   - Type mismatches in shared types
   - Missing dependencies
   - If conflicts found → STOP, roll back

4. **Verify completeness:**
   - Every component's public functions are represented
   - All error codes are documented
   - All types are defined

5. **Get approval**

**Files:**
- `30. API-Surface-Consolidation/PHASE-30-PROMPT.md`
- `30. API-Surface-Consolidation/API-SURFACE-TEMPLATE.md`

---

## Phase 31: Library Structure

**Purpose:** Define the folder structure for shared libraries identified in Phase 23.

**Input:**
- `lib-{{library}}/spec/` files from Phase 23
- DEPENDENCY-MAP.md from Pass 3

**Output:**
- `LIBRARY-STRUCTURE.md` — Library organization

**Process:**

1. **List all libraries** from Phase 23

2. **Define folder structure** for each:
   - Source files location
   - Test files location
   - Export structure

3. **Define dependencies:**
   - Which libraries depend on which
   - Build order

4. **If no libraries identified:** Mark phase as N/A

5. **Get approval**

**Files:**
- `31. Library-Structure/PHASE-31-PROMPT.md`
- `31. Library-Structure/LIBRARY-STRUCTURE-TEMPLATE.md`

---

## Phase 32: Repo Setup

**Purpose:** Define the complete repository folder structure.

**Input:**
- All component INDEX.md files
- IMPLEMENTATION-PLAN.md from Pass 4
- DATABASE-SCHEMA.md from Phase 29
- API-SURFACE.md from Phase 30
- LIBRARY-STRUCTURE.md from Phase 31

**Output:**
- `REPO-STRUCTURE.md` — Complete folder tree

**Process:**

1. **Define root structure:**
   - Source folders
   - Config folders
   - Test folders
   - Documentation folders

2. **Map components to folders:**
   - Where does each system live?
   - Where does each subsystem live?
   - Where do integrations live?
   - Where do libraries live?

3. **Define file naming conventions:**
   - Source files
   - Test files
   - Config files

4. **Verify completeness:**
   - Every component has a home
   - No orphaned specs

5. **Get approval**

**Files:**
- `32. Repo-Setup/PHASE-32-PROMPT.md`
- `32. Repo-Setup/REPO-STRUCTURE-TEMPLATE.md`

---

## Phase 33: Tech Stack

**Purpose:** Document the specific technologies for this project.

**Input:**
- Project requirements
- Team preferences/constraints
- DATABASE-SCHEMA.md (confirms database choice)

**Output:**
- `TECH-STACK.md` — Technology decisions

**Process:**

1. **Document decisions for:**
   - Language(s)
   - Framework(s)
   - Database(s)
   - Infrastructure/hosting
   - Build tools
   - Testing tools

2. **Justify each choice:**
   - Why this technology?
   - What alternatives were considered?
   - Any constraints or limitations?

3. **Note versions:**
   - Specific versions to use
   - Version constraints

4. **Get approval**

**Files:**
- `33. Tech-Stack/PHASE-33-PROMPT.md`
- `33. Tech-Stack/TECH-STACK-TEMPLATE.md`

---

## Phase 34: Environment

**Purpose:** Define environment configuration for all stages.

**Input:**
- TECH-STACK.md from Phase 33
- Security requirements

**Output:**
- `ENVIRONMENT.md` — Environment configuration

**Process:**

1. **Define environments:**
   - Development (local)
   - Staging/Testing
   - Production

2. **For each environment, document:**
   - Environment variables
   - Configuration values
   - Secrets (references only, not actual values)
   - URLs/endpoints

3. **Define environment-specific behavior:**
   - What differs between environments?
   - Feature flags
   - Debug settings

4. **Get approval**

**Files:**
- `34. Environment/PHASE-34-PROMPT.md`
- `34. Environment/ENVIRONMENT-TEMPLATE.md`

---

## Phase 35: Coding Standards

**Purpose:** Establish coding standards for implementation.

**Input:**
- TECH-STACK.md from Phase 33
- LLM constraints from CLAUDE.MD

**Output:**
- `CODING-STANDARDS.md` — Style guide and standards

**Process:**

1. **Define code style:**
   - Formatting rules
   - Naming conventions
   - File organization

2. **Define LLM-friendly patterns:**
   - Composition over inheritance
   - Explicit over implicit
   - Small files (~300 LOC target)
   - Clear naming
   - Minimal boilerplate
   - Comments for "why" not "what"

3. **Define linting/formatting tools:**
   - Which tools to use
   - Configuration

4. **Define review standards:**
   - What must be checked
   - Approval requirements

5. **Get approval**

**Files:**
- `35. Coding-Standards/PHASE-35-PROMPT.md`
- `35. Coding-Standards/CODING-STANDARDS-TEMPLATE.md`

---

## Phase 36: PreCode Ready Check

**Purpose:** Final verification that everything is ready for frontend/implementation.

**Input:**
- All PreCode outputs (Phases 29-35)

**Output:**
- `PRECODE-CHECKLIST.md` — Final verification

**Process:**

1. **Verify all outputs exist:**
   - DATABASE-SCHEMA.md
   - MIGRATION-STRATEGY.md
   - API-SURFACE.md
   - LIBRARY-STRUCTURE.md (or N/A)
   - REPO-STRUCTURE.md
   - TECH-STACK.md
   - ENVIRONMENT.md
   - CODING-STANDARDS.md

2. **Verify no conflicts:**
   - Schema is consistent
   - API is consistent
   - No blocking issues

3. **Verify completeness:**
   - All components accounted for
   - All decisions documented

4. **Get final approval**

**Files:**
- `36. PreCode-Ready-Check/PHASE-36-PROMPT.md`
- `36. PreCode-Ready-Check/PRECODE-CHECKLIST-TEMPLATE.md`

---

## Folder Structure

After PreCode is complete:

```
{{project}}/
│
├── 0. Admin/
│   └── 0d. PreCode/
│       ├── Claude-precode.md
│       ├── Master-PreCode-Guide 29to36.md
│       ├── PRECODE-PROGRESS.md
│       └── PRECODE-DECISION-NOTES.md
│
├── DATABASE-SCHEMA.md              ← Phase 29
├── MIGRATION-STRATEGY.md           ← Phase 29
├── API-SURFACE.md                  ← Phase 30
├── LIBRARY-STRUCTURE.md            ← Phase 31 (or N/A)
├── REPO-STRUCTURE.md               ← Phase 32
├── TECH-STACK.md                   ← Phase 33
├── ENVIRONMENT.md                  ← Phase 34
├── CODING-STANDARDS.md             ← Phase 35
├── PRECODE-CHECKLIST.md            ← Phase 36
│
├── {{system}}/
│   └── spec/                       ← From 0c (unchanged)
│
└── lib-{{library}}/                ← From Phase 23 (unchanged)
    └── spec/
```

---

## Completion Criteria

0d PreCode is COMPLETE when:

1. **All phases approved:**
   - [ ] Phase 29 — Database Schema Consolidation
   - [ ] Phase 30 — API Surface Consolidation
   - [ ] Phase 31 — Library Structure (or N/A)
   - [ ] Phase 32 — Repo Setup
   - [ ] Phase 33 — Tech Stack
   - [ ] Phase 34 — Environment
   - [ ] Phase 35 — Coding Standards
   - [ ] Phase 36 — PreCode Ready Check

2. **No conflicts unresolved**

3. **All outputs exist and are approved**

4. **User approves moving to 0e Frontend**

---

## Next Section

After 0d PreCode → **0e. Frontend**

0e will:
- Accept visual mockups as input
- Create FRONTEND-BACKEND-CONTRACT.md
- Spec pages and components
- Map frontend to API endpoints

---

## Quick Reference

| Need | Go To |
|------|-------|
| Claude instructions | Claude-precode.md |
| Conflict resolution | Roll back to 0c, fix spec |
| Phase 29 details | `29. Database-Schema-Consolidation/PHASE-29-PROMPT.md` |
| Progress tracking | PRECODE-PROGRESS.md |
| Decision logging | PRECODE-DECISION-NOTES.md |

---
