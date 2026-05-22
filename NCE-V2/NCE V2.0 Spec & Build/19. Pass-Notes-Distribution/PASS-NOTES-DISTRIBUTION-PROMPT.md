# PHASE 19: PASS NOTES DISTRIBUTION

---
Phase: 19
Name: Pass Notes Distribution
Location: 0. Admin/0b. PassPhases 0-4/19. Pass-Notes-Distribution/
---

## ROLE

You are distributing relevant pass phase information into each component folder.

This creates a single PASS-NOTES.md per component that consolidates all relevant insights from Pass 0-4, so Claude has context when writing Full Specs without needing to remember 2000+ lines.

---

## TASK

**Update PASS-PROGRESS.md:** Mark Phase 19 as ACTIVE

**Then for EACH component:**

1. **Extract relevant info** from project-wide pass documents
2. **Create PASS-NOTES.md** in the component folder
3. **Include only what's relevant** to that specific component

---

## SOURCES TO EXTRACT FROM

| Source Document | Extract For Each Component |
|-----------------|---------------------------|
| `pass-0/SYSTEM-COVERAGE-*.md` | Coverage verdict, gaps, risks |
| `pass-0/SUBSYSTEM-COVERAGE-*.md` | Coverage verdict, gaps, risks |
| `pass-0/PROVIDER-COVERAGE-*.md` | Coverage verdict, vendor risks |
| `pass-0/SERVICE-COVERAGE-*.md` | Coverage verdict, constraints |
| `pass-0/INTERNAL-INTEGRATIONS.md` | What this component sends/receives |
| `admin/DEPENDENCY-MAP.md` | This component's dependencies, dependents, shared deps |
| `admin/IMPLEMENTATION-PLAN.md` | This component's files, LOC, tasks |
| `admin/PASS-DECISION-NOTES.md` | Decisions relevant to this component |

---

## OUTPUT LOCATION

Create PASS-NOTES.md in each component folder:

```
{{project}}/
├── {{system}}/
│   └── PASS-NOTES.md                    ← NEW
│
├── {{system}}/{{subsystem}}/
│   └── PASS-NOTES.md                    ← NEW
│
└── integration-{{provider}}/{{service}}/
    └── PASS-NOTES.md                    ← NEW
```

---

## PROCESS

### For Each System:
1. Read SYSTEM-COVERAGE-{{system}}.md → extract verdict, gaps, risks
2. Read INTERNAL-INTEGRATIONS.md → extract sends/receives for this system
3. Read DEPENDENCY-MAP.md → extract dependencies, dependents, shared deps
4. Read IMPLEMENTATION-PLAN.md → extract files, LOC, tasks for this system
5. Read PASS-DECISION-NOTES.md → extract relevant decisions
6. Create `{{system}}/PASS-NOTES.md`

### For Each Subsystem:
1. Read SUBSYSTEM-COVERAGE-{{system}}-{{subsystem}}.md → extract verdict, gaps, risks
2. Read DEPENDENCY-MAP.md → extract dependencies for this subsystem
3. Read IMPLEMENTATION-PLAN.md → extract files, LOC, tasks for this subsystem
4. Create `{{system}}/{{subsystem}}/PASS-NOTES.md`

### For Each External Integration Service:
1. Read SERVICE-COVERAGE-{{provider}}-{{service}}.md → extract verdict, constraints
2. Read PROVIDER-COVERAGE-{{provider}}.md → extract vendor risks
3. Read DEPENDENCY-MAP.md → extract what depends on this service
4. Read IMPLEMENTATION-PLAN.md → extract files, LOC, tasks for this service
5. Create `integration-{{provider}}/{{service}}/PASS-NOTES.md`

---

## MANDATORY RULES

- Do NOT invent information — only extract what exists
- Do NOT copy entire documents — extract relevant portions only
- If a section has no relevant info, mark as "None identified"
- Keep PASS-NOTES.md focused and concise
- This is extraction, not analysis

---

## END CONDITION

Phase 19 is COMPLETE only when:
- [ ] Every system has PASS-NOTES.md
- [ ] Every subsystem has PASS-NOTES.md
- [ ] Every external integration service has PASS-NOTES.md
- [ ] All PASS-NOTES.md approved
- [ ] PASS-PROGRESS.md updated: Phase 19 marked COMPLETE
- [ ] **0b-to-0c-HANDOFF.md created**

**Next:** Proceed to `0. Admin/0c. Full Specs/` (Phase 20)

---

## SECTION HANDOFF

**Create `0b-to-0c-HANDOFF.md`** in the project admin folder with:

```markdown
# 0b → 0c Section Handoff

---
Created: {{current_timestamp}}
Section Completed: 0b Pass Phases (Phases 13-19)
Next Section: 0c Full Specs (Phases 20-28)
---

## Summary

0b Pass Phases is COMPLETE. Coverage reviews (Pass 0), dependency analysis (Pass 3), and implementation planning (Pass 4) are done. PASS-NOTES.md distributed to all components.

## Key Artifacts Created

| Artifact | Location | Status |
|----------|----------|--------|
| SYSTEM-COVERAGE-*.md | /pass-0/ | APPROVED |
| SUBSYSTEM-COVERAGE-*.md | /pass-0/ | APPROVED |
| PROVIDER-COVERAGE-*.md | /pass-0/ | APPROVED |
| SERVICE-COVERAGE-*.md | /pass-0/ | APPROVED |
| INTERNAL-INTEGRATIONS.md | /pass-0/ | APPROVED |
| DEPENDENCY-MAP.md | /admin/ | APPROVED |
| IMPLEMENTATION-PLAN.md | /admin/ | APPROVED |
| PASS-NOTES.md (per component) | /{{component}}/ | APPROVED |

## Coverage Summary

| Component Type | Covered | Gaps Identified |
|---------------|---------|-----------------|
| Systems | {{n}}/{{n}} | {{summary}} |
| Subsystems | {{n}}/{{n}} | {{summary}} |
| Providers | {{n}}/{{n}} | {{summary}} |
| Services | {{n}}/{{n}} | {{summary}} |

## Key Decisions Made

{{List significant decisions from PASS-DECISION-NOTES.md}}

## Known Gaps or Risks

{{Any coverage gaps, risks, or items flagged for 0c attention}}

## Handoff Verification

- [ ] All coverage reviews approved
- [ ] INTERNAL-INTEGRATIONS.md approved
- [ ] DEPENDENCY-MAP.md approved
- [ ] IMPLEMENTATION-PLAN.md approved
- [ ] PASS-NOTES.md distributed to all components
- [ ] PASS-PROGRESS.md shows Phase 19 COMPLETE

**Ready to begin 0c Full Specs.**
```

---

## TEMPLATE

- PASS-NOTES-TEMPLATE.md

---

## INPUTS

From Pass 0:
- All coverage reviews in `{{project}}/pass-0/`
- INTERNAL-INTEGRATIONS.md

From Pass 3-4:
- `{{project}}/admin/DEPENDENCY-MAP.md`
- `{{project}}/admin/IMPLEMENTATION-PLAN.md`
- `{{project}}/admin/PASS-DECISION-NOTES.md`
