# PHASE 32: REPO SETUP

---
Phase: 32
Name: Repo Setup
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are defining the complete repository folder structure for implementation.

Every component must have a clear home. No orphaned specs.

---

## TASK

1. Gather all component INDEX.md files
2. Define root folder structure
3. Map every component to a folder location
4. Define file naming conventions
5. Verify completeness
6. Get approval

---

## STEP 1: Gather Components

List all components from 0c specs:

**Systems:**
- {{system-a}}
- {{system-b}}

**Subsystems:**
- {{system-a}}/{{subsystem-1}}
- {{system-a}}/{{subsystem-2}}

**Libraries:**
- lib-{{name}}

**Integrations:**
- int-{{name}}

---

## STEP 2: Define Root Structure

Standard structure:

```
{{project}}/
├── src/                    ← Source code
├── tests/                  ← Test files
├── config/                 ← Configuration
├── migrations/             ← Database migrations
├── docs/                   ← Documentation
├── scripts/                ← Build/deploy scripts
└── ...
```

Adapt to tech stack and team conventions.

---

## STEP 3: Map Components to Folders

Every component needs:
- Source location
- Test location
- Spec reference

| Component | Source Path | Test Path | Spec Path |
|-----------|-------------|-----------|-----------|
| {{system}} | `src/{{system}}` | `tests/{{system}}` | `{{system}}/spec/` |
| {{subsystem}} | `src/{{system}}/{{sub}}` | `tests/{{system}}/{{sub}}` | `{{system}}/{{sub}}/spec/` |

---

## STEP 4: File Naming Conventions

Define conventions for:

| File Type | Convention | Example |
|-----------|------------|---------|
| Source files | {{convention}} | `{{example}}` |
| Test files | {{convention}} | `{{example}}` |
| Config files | {{convention}} | `{{example}}` |
| Type files | {{convention}} | `{{example}}` |

---

## STEP 5: Verify Completeness

**ORPHANED SPEC AUDIT:** Every spec MUST have a home.

Check:
- [ ] Every system INDEX.md has a source folder mapped
- [ ] Every subsystem INDEX.md has a source folder mapped
- [ ] Every library INDEX.md has a source folder mapped
- [ ] Every integration INDEX.md has a source folder mapped
- [ ] Every STORAGE.md has corresponding migration location
- [ ] Every FUNCTIONS.md has corresponding source location
- [ ] Test locations defined for all components
- [ ] Config locations defined

**If ANY spec doesn't have a folder:** Stop and resolve. A spec without a home shouldn't exist.

---

## STEP 6: Get Approval

Present REPO-STRUCTURE.md to user.

Wait for APPROVE or REVISE.

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| INDEX.md files | `{{component}}/spec/INDEX.md` | Component list |
| IMPLEMENTATION-PLAN.md | From Pass 4 | Build order |
| DATABASE-SCHEMA.md | Phase 29 | Migration location |
| LIBRARY-STRUCTURE.md | Phase 31 | Library locations |

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| REPO-STRUCTURE.md | Project root | Folder structure |

---

## TEMPLATES

- REPO-STRUCTURE-TEMPLATE.md

---

## RULES

- Every component must have a home
- No orphaned specs
- Follow consistent naming conventions
- Adapt to tech stack (refined in Phase 33)

---
