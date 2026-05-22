# PHASE 21: INTERNAL COMPONENT SPECS

---
Phase: 21
Name: Internal Component Specs
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/21.Internal-Component-Specs/
---

## ROLE

You are writing implementation-ready specifications for internal components (systems, subsystems, libraries).

This phase takes PRE-SPEC-NOTES.md and expands it into 10 focused spec files.

---

## TASK

**For EACH internal component (systems, subsystems):**

1. Read the component's PRE-SPEC-NOTES.md (from Phase 20)
2. Create `spec/` folder in the component directory
3. Create each spec file using the templates
4. Get approval before moving to next component

---

## PROCESS ORDER

**Per Component (one at a time):**
```
System-A/
├── Read PRE-SPEC-NOTES.md
├── Create spec/INDEX.md         ← First (sets context)
├── Create spec/OVERVIEW.md
├── Create spec/FUNCTIONS.md
├── Create spec/TYPES.md
├── Create spec/BEHAVIOUR.md
├── Create spec/ERRORS.md
├── Create spec/CONFIG.md
├── Create spec/STORAGE.md       ← Skip if no database
├── Create spec/DECISIONS.md
├── Create spec/EXAMPLES.md      ← Last (proves it works)
└── Get approval
    │
    └── Then do subsystems:
        ├── System-A/subsystem-1/spec/*.md
        └── System-A/subsystem-2/spec/*.md

Then move to System-B/
```

**Full Order:**
1. Systems with their subsystems (alphabetically)
2. Libraries (after Phase 23 identifies them)

---

## SPEC FILES

| File | Purpose | Source (PRE-SPEC Section) | Target LOC |
|------|---------|---------------------------|------------|
| INDEX.md | Quick reference, status, notes | 1, 4, 12 | ~80 |
| OVERVIEW.md | Purpose, boundaries, guarantees | 1, 2, 5, 11 | ~150 |
| FUNCTIONS.md | Public API with full signatures | 3, 4 | ~300 |
| TYPES.md | All data structures | 9 | ~200 |
| BEHAVIOUR.md | Process flows, state transitions | 3, 8 | ~200 |
| ERRORS.md | Error codes, categories, recovery | 6 | ~150 |
| CONFIG.md | Configuration options | 8 | ~100 |
| STORAGE.md | Database schema, indexes | 9 | ~150 |
| DECISIONS.md | Design rationale | 5, 7, 8 | ~100 |
| EXAMPLES.md | Full request/response examples | New | ~200 |

---

## WHEN ISSUES ARISE

If writing a subsystem reveals a need to change the system:

### Option A: Add Note (Minor Issue)
- Add to System's `spec/INDEX.md` → "Build Notes & Constraints"
- Continue with subsystem
- Resolve during Phase 25 (Post-Spec Analysis)

**Use when:**
- Doesn't break contracts
- Doesn't affect other subsystems
- Nice-to-have, not must-have

### Option B: Revise System (Major Issue)
- Stop subsystem work
- Revise System spec files
- Bump version in INDEX.md
- Log in PASS-DECISION-NOTES.md
- Continue

**Use when:**
- Breaks CONTRACT.md
- Affects multiple subsystems
- Must-have for subsystem to work

---

## MANDATORY RULES

- Do NOT invent functionality not in PRE-SPEC-NOTES.md
- Do NOT skip files (mark N/A if not applicable)
- Do NOT exceed ~300 LOC per file — split if needed
- If information is missing, add to INDEX.md "Spec Gaps"
- Reference PRE-SPEC-NOTES.md section numbers

---

## OUTPUT LOCATION

```
{{project}}/
├── {{system}}/
│   └── spec/
│       ├── INDEX.md
│       ├── OVERVIEW.md
│       ├── FUNCTIONS.md
│       ├── TYPES.md
│       ├── BEHAVIOUR.md
│       ├── ERRORS.md
│       ├── CONFIG.md
│       ├── STORAGE.md
│       ├── DECISIONS.md
│       └── EXAMPLES.md
│
└── {{system}}/{{subsystem}}/
    └── spec/
        └── (same structure)
```

---

## APPROVAL GATE

Per component:
- [ ] All 10 spec files created (or marked N/A)
- [ ] INDEX.md shows all files status
- [ ] No critical gaps in "Spec Gaps" section
- [ ] Human approves

---

## END CONDITION

Phase 21 is COMPLETE only when:
- [ ] Every system has spec/ folder with all files
- [ ] Every subsystem has spec/ folder with all files
- [ ] All approved by human
- [ ] All issues logged in INDEX.md or resolved

**Next:** Phase 22 (External Integration Specs)

---

## TEMPLATES

Internal Component Templates (10 files):
- INDEX-TEMPLATE.md
- OVERVIEW-TEMPLATE.md
- FUNCTIONS-TEMPLATE.md
- TYPES-TEMPLATE.md
- BEHAVIOUR-TEMPLATE.md
- ERRORS-TEMPLATE.md
- CONFIG-TEMPLATE.md
- STORAGE-TEMPLATE.md
- DECISIONS-TEMPLATE.md
- EXAMPLES-TEMPLATE.md

---

## INPUTS

From Phase 20:
- PRE-SPEC-NOTES.md (primary source)

From Phase 19:
- PASS-NOTES.md (context)

From Pass 1-2:
- SYSTEM.md / SUBSYSTEM.md
- CONTRACT.md

---
Generated: {{timestamp}}
---