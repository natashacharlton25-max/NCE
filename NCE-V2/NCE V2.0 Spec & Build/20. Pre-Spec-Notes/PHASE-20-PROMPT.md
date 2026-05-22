# PHASE 20: PRE-SPEC NOTES

---
Phase: 20
Name: Pre-Spec Notes
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/20.Pre-Spec-Notes/
---

## ROLE

You are preparing each component for detailed spec writing by capturing all concerns, dependencies, data flows, and boundaries in a structured format.

Pre-Spec Notes ensure spec writing doesn't start from scratch — all the thinking is done first.

---

## TASK

**For EACH component (systems, subsystems, external integrations):**

1. Read the component's existing docs:
   - PASS-NOTES.md (from Phase 19)
   - SYSTEM.md / SUBSYSTEM.md / EXTERNAL-INTEGRATION.md (from Pass 1)
   - CONTRACT.md (from Pass 2)
   - ADMIN.md

2. Create PRE-SPEC-NOTES.md using the template

3. Ensure all 12 sections are complete

4. Get approval before moving to spec writing

---

## PROCESS ORDER

1. **Systems** — alphabetically (A-Z)
2. **Subsystems** — grouped by parent system
3. **External Integrations** — by provider, then service
4. **Libraries** — alphabetically (after Phase 23 identifies them)

---

## WHAT PRE-SPEC NOTES CAPTURE

| Section | What It Captures |
|---------|------------------|
| 1. Core Identity | Name, purpose, type, status |
| 2. Scope & Boundaries | What it does / doesn't do |
| 3. Data Flow | Inputs → Processing → Outputs |
| 4. Dependencies | What it calls, what calls it |
| 5. Concern Ownership | Who owns validation, retry, logging, etc. |
| 6. Error Handling | Error codes, transient vs permanent |
| 7. Concern Clarifications | Ambiguities resolved |
| 8. Architecture | Performance, concurrency, storage |
| 9. Data Schemas | Input/output shapes (draft) |
| 10. External Systems | APIs, credentials, latency |
| 11. Adjacent Modules | Relationships, boundaries |
| 12. Summary | Ready for spec writing? |

---

## MANDATORY RULES

- Do NOT invent functionality — if missing, use TODO markers
- Do NOT write implementation details — just shapes and boundaries
- Do NOT skip sections — all 12 must be addressed
- If information is missing, state it explicitly
- Reference PASS-NOTES.md for context

---

## OUTPUT LOCATION

```
{{project}}/
├── {{system}}/
│   └── PRE-SPEC-NOTES.md           ← NEW
│
├── {{system}}/{{subsystem}}/
│   └── PRE-SPEC-NOTES.md           ← NEW
│
└── integration-{{provider}}/{{service}}/
    └── PRE-SPEC-NOTES.md           ← NEW
```

---

## CHANGE MANAGEMENT

If PRE-SPEC-NOTES reveals needed changes to parent system:

| Impact Level | Action |
|--------------|--------|
| Minor (isolated) | Add note to System INDEX.md "Build Notes" |
| Major (breaks contract) | Stop, revise System spec, then continue |

**Decision Framework:**
1. Does it break CONTRACT.md? → Revise system
2. Does it affect OTHER subsystems? → Revise system
3. Is it "must have" vs "nice to have"? → Must have = Revise
4. Otherwise → Add note, continue

---

## APPROVAL GATE

Before moving to Phase 21 (spec writing):

- [ ] All PRE-SPEC-NOTES.md created
- [ ] All 12 sections completed (no blanks)
- [ ] Concern ownership clear for every cross-cutting concern
- [ ] Can list 3+ things each component does NOT do
- [ ] Human approves

---

## END CONDITION

Phase 20 is COMPLETE only when:

- [ ] Every system has PRE-SPEC-NOTES.md
- [ ] Every subsystem has PRE-SPEC-NOTES.md
- [ ] Every external integration has PRE-SPEC-NOTES.md
- [ ] All approved by human
- [ ] Ready for spec writing

**Next:** Phase 21 (Internal Component Specs)

---

## TEMPLATES

| Component Type | Template | Checklist |
|----------------|----------|-----------|
| Internal (system, subsystem, library) | PRE-SPEC-NOTES-TEMPLATE.md | PRE-SPEC-NOTES-CHECKLIST.md |
| External Integration | PRE-SPEC-NOTES-EXTERNAL-TEMPLATE.md | PRE-SPEC-NOTES-EXTERNAL-CHECKLIST.md |

---

## INPUTS

From Phase 19:
- All PASS-NOTES.md

From Pass 1:
- All SYSTEM.md, SUBSYSTEM.md, EXTERNAL-INTEGRATION.md
- All ADMIN.md

From Pass 2:
- All CONTRACT.md

---
Generated: {{timestamp}}
---
