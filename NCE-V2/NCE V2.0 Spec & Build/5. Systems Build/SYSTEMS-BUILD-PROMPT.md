# SYSTEM BUILD — PROMPT

## ROLE
You are materialising approved systems into a real project folder structure.
Your job is to create folders and populate each with a completed SYSTEM-NOTES.md file.

You are NOT designing internals, choosing technologies, or adding new systems.

---

## TASK

For each approved system from the Systems Pre-build Check:
1. Create a folder using the system name (lowercase, hyphens for spaces)
2. Create a SYSTEM-NOTES.md file inside that folder
3. Complete all sections of SYSTEM-NOTES.md based on the approved information

---

## FOLDER STRUCTURE

```
{{project-name}}/
├── {{system-name}}/
│   └── SYSTEM-NOTES.md
├── {{system-name}}/
│   └── SYSTEM-NOTES.md
└── (repeat for each system)
```

**Naming rules:**
- Project folder: lowercase, hyphens (e.g., `task-manager`)
- System folders: lowercase, hyphens (e.g., `user-identity`, `content-storage`)
- No spaces, no special characters

---

## COMPLETING SYSTEM-NOTES.md

For each section, derive content from the approved Systems Clarification and Project Intention:

### Purpose
- Pull from the system's "Purpose" in Systems Clarification
- Keep it to one sentence

### Intent
- Expand slightly on purpose to describe the desired outcome
- Still high-level, no mechanics

### Justification
- Pull from the system's "Justification" in Systems Clarification

### In Scope
- Derive from the system's purpose
- What must this system handle to fulfil its responsibility?
- Be specific but not implementation-level

### Out of Scope
- What does this system NOT handle?
- Reference other systems where appropriate ("Belongs to X system")
- Include obvious temptations ("Will not handle Y even though related")

### Non-Goals
- Things this system will never do
- Different from Out of Scope: these are permanent exclusions, not just "belongs elsewhere"

### Inputs / Outputs / Dependencies
- Derive from the system relationships implied by the project
- Use system names, not technical details
- If unknown, write "TBD — to be determined in Pass 0"

---

## RULES

1. **Use ONLY the approved system names** — no new systems
2. **Do NOT change system names** — use exactly as approved
3. **Do NOT reference technologies, frameworks, or APIs** — responsibility-level only
4. **Do NOT invent scope** — derive only from approved documents
5. **Respect the ~1,500 LOC constraint** — if scope seems too large, note it
6. **Mark unknowns as TBD** — don't guess at inputs/outputs/dependencies

---

## PROCESS

1. Read the approved Systems Clarification and Pre-build Check
2. Create the project root folder
3. For each system:
   - Create the system folder
   - Create SYSTEM-NOTES.md
   - Complete all sections
4. Present the full structure and file contents
5. Ask for approval

---

## OUTPUT FORMAT

Return a single Markdown document showing:

1. The folder structure created
2. The complete contents of each SYSTEM-NOTES.md file

```markdown
## Project Structure

{{project-name}}/
├── {{system-name}}/
│   └── SYSTEM-NOTES.md
├── {{system-name}}/
│   └── SYSTEM-NOTES.md
└── ...

---

## {{system-name}}/SYSTEM-NOTES.md

(complete file contents)

---

## {{system-name}}/SYSTEM-NOTES.md

(complete file contents)

---

(repeat for each system)
```

After presenting, ask:

```
Please review the created system folders and SYSTEM-NOTES.md files.
- Reply APPROVE to accept and proceed to Subsystem Identification
- Reply REVISE with specific changes needed
```

---

## ON APPROVAL

When approved:
- Update each SYSTEM-NOTES.md Status to: **APPROVED**
- Update Last Updated to: **{{current_timestamp}}**
- Confirm next step: **Subsystem Identification**

---

## TEMPLATE: SYSTEM-NOTES.md

```markdown
# {{System Name}}

---
Status: DRAFT | APPROVED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD
---

## Purpose
(One clear sentence describing **why this system exists**.)

## Intent
(What this system is trying to achieve at a high level.)

## Justification
- Why this responsibility is required for the project
- Why it deserves to be its own system

---

## In Scope
- …
- …

## Out of Scope
- …
- …

## Non-Goals
- …
- …

---

## Inputs

| Input | Source System | Description |
|-------|---------------|-------------|
| … | … | … |

## Outputs

| Output | Consuming System | Description |
|--------|------------------|-------------|
| … | … | … |

## Dependencies

| Dependency | Reason |
|------------|--------|
| … | … |

---

## Size Constraint
This system is intended to remain under **~1,500 lines** of implementation code.

---

## Notes

---

## Pass Tracking

| Pass | Status | Owner | Date |
|------|--------|-------|------|
| Build | COMPLETE | Claude | {{timestamp}} |
| Pass 0 | PENDING | — | — |
| Pass 1 | PENDING | — | — |
| Pass 2 | PENDING | — | — |
| Pass 3 | PENDING | — | — |
| Pass 4 | PENDING | — | — |

## Pass Notes
```

---

## INPUTS

Project Name:
{{project_name}}

Approved Systems Clarification:
{{systems_clarification}}

Approved Systems Pre-build Check:
{{systems_prebuild_check}}

Project Intention:
{{project_intention}}

Timestamp:
{{current_timestamp}}