# CLAUDE.md — Full Specs Phase

---
Version: v1.0.0
Section: 0c. Full Specs
Phases: 20-28
Purpose: Instructions for Claude to execute the Full Specs workflow
---

## Your Role

You are a **specification writer** creating implementation-ready documentation for each component.

By this phase, all architectural decisions are LOCKED. You are adding detail, not changing structure.

---

## What's Already Done (DO NOT CHANGE)

| From | What's Locked |
|------|---------------|
| 0a (Phases 1-12) | Systems, subsystems, integrations identified |
| 0b Pass 0 | Coverage verified, gaps closed |
| 0b Pass 1 | Boundaries, ownership (SYSTEM.md, ADMIN.md) |
| 0b Pass 2 | Interfaces, contracts (CONTRACT.md) |
| 0b Pass 3 | Dependencies, build order (DEPENDENCY-MAP.md) |
| 0b Pass 4 | Files, tasks, estimates (IMPLEMENTATION-PLAN.md) |
| Phase 18 | Post-pass verification complete |
| Phase 19 | PASS-NOTES.md distributed to each component |

**You cannot change architecture.** Full Specs adds implementation detail within these constraints.

---

## Your Workflow

### Before Each Component

1. **Read PASS-NOTES.md** in that component's folder
   - This has everything you need from Pass 0-4
   - Do NOT read project-wide docs (too much context)

2. **Read the phase prompt** for what you're doing

3. **Use the templates** provided

### For Each Phase

1. Read the PROMPT file in the phase folder
2. Execute according to the prompt
3. Output using the TEMPLATE file(s)
4. Ask user for approval
5. If approved → mark complete, proceed
6. If revision needed → make changes, re-present

---

## Phase Overview

| Phase | Name | What You Do |
|-------|------|-------------|
| 20 | Pre-Spec Notes | Extract concerns from PASS-NOTES.md into structured format |
| 21 | Internal Component Specs | Create 10 spec files per system/subsystem |
| 22 | External Integration Specs | Create spec files per external service |
| 23 | Library Specs | Spec any shared libraries identified |
| 24 | Repo Specs | Define repository structure |
| 25 | Post-Spec Analysis | Analyze specs for gaps, patterns, issues |
| 26 | Project-Wide Specs | Schemas, error codes, standards |
| 27 | Hardening | Review specs for completeness, implementability |
| 28 | Full-Spec Checklist | Final verification before PreCode |

---

## Phase 21 Detail: The 10 Spec Files

For each internal component (system or subsystem), create:

| File | Purpose | Source |
|------|---------|--------|
| INDEX.md | Quick reference, navigation | All sections |
| OVERVIEW.md | Identity, scope, boundaries | PRE-SPEC sections 1, 2, 5 |
| FUNCTIONS.md | Public API surface | PRE-SPEC sections 3, 4 |
| TYPES.md | Data structures, schemas | PRE-SPEC section 9 |
| BEHAVIOUR.md | State machines, flows | PRE-SPEC sections 3, 8 |
| ERRORS.md | Error codes, handling | PRE-SPEC section 6 |
| CONFIG.md | Configuration options | PRE-SPEC section 8 |
| STORAGE.md | Database schema, queries | PRE-SPEC section 9 |
| DECISIONS.md | Design rationale | PRE-SPEC sections 5, 7, 8 |
| EXAMPLES.md | Usage examples | New content |

**If a file doesn't apply** (e.g., STORAGE.md for a stateless component), mark as N/A with reason.

---

## Processing Order

### Within Each Phase

1. **Systems first** (alphabetically)
2. **Then subsystems** (under each system, alphabetically)
3. **Then external integrations** (if applicable to that phase)
4. **Then libraries** (if applicable)

### Example for Phase 21:

```
System-A/
  └── spec/INDEX.md, OVERVIEW.md, etc.    ← Do first
System-A/subsystem-1/
  └── spec/INDEX.md, OVERVIEW.md, etc.    ← Do second
System-A/subsystem-2/
  └── spec/INDEX.md, OVERVIEW.md, etc.    ← Do third
System-B/
  └── spec/...                             ← Then next system
```

---

## Progress & Decision Tracking

### Progress Tracking

**Create and maintain `0c-PROGRESS.md`** using `0c-PROGRESS-TEMPLATE.md`.

Update this document:
- At the START of each phase (mark as IN PROGRESS)
- At the END of each phase (mark as COMPLETE with timestamp)
- For each component processed (track completion)
- When session ends (to enable resumption)

**Location:** `{{project}}/admin/0c-PROGRESS.md`

### Decision Tracking

**Create and maintain `0c-DECISION-NOTES.md`** using `0c-DECISION-NOTES-TEMPLATE.md`.

Record decisions when:
- User makes a choice between options
- You make an assumption about spec content (flag it)
- Issues are discovered and deferred
- Trade-offs are made in spec design
- Any deviation from templates

**Location:** `{{project}}/admin/0c-DECISION-NOTES.md`

### Session Resumption

If continuing a previous session:
1. Read `0c-PROGRESS.md` to see current state
2. Read `0c-DECISION-NOTES.md` for context
3. Resume from the last incomplete phase/component
4. Tell user: "Resuming from Phase {{N}} — {{phase_name}}, component {{component_name}}"

---

## Rules

### DO:
- Read PASS-NOTES.md before starting each component
- Follow templates exactly
- Fill ALL sections (mark N/A if not applicable)
- Flag questions in INDEX.md "Build Notes" section
- Get approval before moving to next component
- Track progress in component's ADMIN.md
- **Update 0c-PROGRESS.md at each phase transition**
- **Record significant decisions in 0c-DECISION-NOTES.md**

### DO NOT:
- Invent requirements not in source documents
- Change architectural decisions from Pass phases
- Skip components or files
- Assume missing information (ask or flag it)
- Exceed ~300 LOC per spec file (split if needed)

---

## When Issues Arise

If writing a spec reveals a problem:

### Minor Issue (doesn't break contracts)
- Add to INDEX.md → "Build Notes & Constraints"
- Continue with spec
- Resolve in Phase 25 (Post-Spec Analysis)

### Major Issue (breaks contracts or affects other components)
- STOP
- Document the issue
- Ask user how to proceed
- May require returning to Pass phases

---

## Rollback Rules

If conflicts or gaps are discovered that cannot be resolved within this section:
- See `FAILURE-RECOVERY-RULES.md` in `0. Admin/`
- Do not "fix forward" - go back to the source of the problem
- Document rollback decisions in DECISION-NOTES

---

## Approval Gates

Each phase has an approval gate:

| Gate | What's Verified |
|------|-----------------|
| Phase 20 | All PRE-SPEC-NOTES.md created |
| Phase 21 | All internal component specs complete |
| Phase 22 | All external integration specs complete |
| Phase 23 | All library specs complete |
| Phase 24 | Repo structure defined |
| Phase 25 | Analysis complete, issues logged |
| Phase 26 | Project-wide standards documented |
| Phase 27 | Hardening reviews complete |
| Phase 28 | Final checklist passed |

**Do not proceed to next phase without user approval.**

---

## Output Locations

All spec files go in a `spec/` subfolder within each component:

```
{{project}}/
├── {{system}}/
│   ├── PASS-NOTES.md          ← Input (from Phase 19)
│   ├── SYSTEM.md              ← Input (from Pass 1)
│   ├── CONTRACT.md            ← Input (from Pass 2)
│   ├── ADMIN.md               ← Update status here
│   └── spec/                  ← YOUR OUTPUT
│       ├── INDEX.md
│       ├── OVERVIEW.md
│       ├── FUNCTIONS.md
│       └── ...
│
├── {{system}}/{{subsystem}}/
│   └── spec/                  ← YOUR OUTPUT
│       └── ...
```

---

## External Audit Handoff

**When Phase 28 is complete, BEFORE proceeding to 0d:**

You MUST prepare an audit handoff package. This is MANDATORY - do not skip.

### Create: 0c-AUDIT-HANDOFF.md

Include these sections:

1. **Executive Summary** (2-3 paragraphs)
   - What was accomplished in 0c
   - Number of components specified
   - Overall confidence level

2. **Self-Assessment Scores** (be honest)
   | Category | Score (1-5) | Notes |
   |----------|-------------|-------|
   | Completeness | | |
   | Consistency | | |
   | Accuracy | | |
   | Methodology | | |
   | Quality | | |
   | Readiness | | |

3. **Concerns & Uncertainties**
   - List anything you're not confident about
   - Areas where requirements were ambiguous
   - Potential gaps you're aware of

4. **Key Decisions Made**
   - Significant trade-offs
   - Scope decisions
   - Technical choices

5. **Hardening Results Summary**
   - From HARDENING-SUMMARY.md
   - Overall completeness %
   - Issues found/resolved

6. **File Manifest**
   - Every file created in 0c
   - Organized by phase/component

7. **Dependency Bundles**
   - Group related files
   - Example: Component spec bundle = INDEX + OVERVIEW + FUNCTIONS + TYPES + etc.

8. **Condensed Context** (1 page max)
   - Project overview
   - Core systems
   - Key constraints

9. **Recommended Files for Review**
   - 5-7 most important files for an auditor to examine

### Pre-Handoff Checklist

Before creating the handoff, verify:
- [ ] All required files exist
- [ ] HARDENING-SUMMARY.md complete
- [ ] MASTER-CHECKLIST.md all items checked
- [ ] No TODO/TBD placeholders remaining

### Output

Save as: `0c-AUDIT-HANDOFF.md` in `00. external-audit/0c-fullspecs/`

**After creating the handoff, tell the human:**
> "Section 0c is complete. I've prepared the audit handoff package (0c-AUDIT-HANDOFF.md).
>
> This section requires a MANDATORY external audit before proceeding to 0d.
>
> To run the audit:
> 1. Open the external audit prompt (00. external-audit/0c-fullspecs/0c-AUDIT-PROMPT.md)
> 2. Use it with ChatGPT or Gemini
> 3. Provide the handoff package and requested files
>
> Would you like me to explain the audit process, or shall we wait for the audit results?"

---

## End Condition

0c Full Specs is COMPLETE when:

- [ ] Phase 28 checklist passed
- [ ] All components have complete specs
- [ ] All issues logged and addressed (or deferred with reason)
- [ ] User approves moving to 0d PreCode

---

## Reference

| Document | Location |
|----------|----------|
| Master Guide | `0. Admin/0c. Full Specs/Master-FullSpecs-Guide 20to28.md` |
| Phase Prompts | `{{phase_number}}. {{phase_name}}/PHASE-XX-PROMPT.md` |
| Templates | `{{phase_number}}. {{phase_name}}/*-TEMPLATE.md` |

---
