# PHASE 36: PRECODE READY CHECK

---
Phase: 36
Name: PreCode Ready Check
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are performing the final verification before the project moves to frontend/implementation.

This is a gate check. Everything must be ready.

---

## TASK

1. Verify all PreCode outputs exist and are approved
2. Verify no unresolved conflicts
3. Verify completeness
4. Ask the critical question
5. Get final approval

---

## CHECKLIST

### Output Verification

| Output | Phase | Exists | Approved |
|--------|-------|--------|----------|
| DATABASE-SCHEMA.md | 29 | ☐ | ☐ |
| MIGRATION-STRATEGY.md | 29 | ☐ | ☐ |
| API-SURFACE.md | 30 | ☐ | ☐ |
| LIBRARY-STRUCTURE.md | 31 | ☐ / N/A | ☐ / N/A |
| REPO-STRUCTURE.md | 32 | ☐ | ☐ |
| TECH-STACK.md | 33 | ☐ | ☐ |
| ENVIRONMENT.md | 34 | ☐ | ☐ |
| CODING-STANDARDS.md | 35 | ☐ | ☐ |

### Conflict Verification

| Check | Status |
|-------|--------|
| No schema conflicts | ☐ Pass / ☐ Fail |
| No API conflicts | ☐ Pass / ☐ Fail |
| No unresolved rollbacks | ☐ Pass / ☐ Fail |
| No blocking issues | ☐ Pass / ☐ Fail |

### Completeness Verification

| Check | Status |
|-------|--------|
| All systems have folder location | ☐ Pass / ☐ Fail |
| All subsystems have folder location | ☐ Pass / ☐ Fail |
| All database tables accounted for | ☐ Pass / ☐ Fail |
| All API endpoints accounted for | ☐ Pass / ☐ Fail |
| Tech stack decisions complete | ☐ Pass / ☐ Fail |
| Environment config complete | ☐ Pass / ☐ Fail |

---

## CRITICAL QUESTION

**Ask the user:**

> Is there anything required to start implementation that is not documented?
>
> This includes:
> - Missing technical decisions
> - Undocumented dependencies
> - Unclear requirements
> - Assumed knowledge that isn't written down
> - External services not yet set up
> - Access/permissions not yet arranged

If the answer is YES → document what's missing before proceeding.

---

## THE "DIFFERENT AI" TEST

**Ask yourself (Claude):**

> If I were a completely new AI with NO access to this conversation history, could I build this project using ONLY the 0d PreCode documents?

Check for:
- [ ] No assumed context from earlier conversations
- [ ] No "we discussed this" references without documentation
- [ ] All decisions are in PRECODE-DECISION-NOTES.md
- [ ] All tech choices are in TECH-STACK.md
- [ ] All folder locations are in REPO-STRUCTURE.md
- [ ] All database details are in DATABASE-SCHEMA.md
- [ ] All API details are in API-SURFACE.md

**If any gap is found:** Document it before marking complete.

---

## FINAL APPROVAL

Present the completed PRECODE-CHECKLIST.md to user.

If all checks pass AND critical question is answered → Mark PreCode as COMPLETE.

**PreCode is COMPLETE when user confirms:**
1. All outputs exist and are approved
2. No unresolved conflicts
3. Nothing required is undocumented
4. Ready to proceed to 0e Frontend

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| PRECODE-CHECKLIST.md | Project root | Final verification |

---

## TEMPLATES

- PRECODE-CHECKLIST-TEMPLATE.md

---

## SECTION HANDOFF

**Create `0d-to-0e-HANDOFF.md`** in project admin folder:

```markdown
# 0d → 0e Section Handoff

---
Created: {{timestamp}}
Section Completed: 0d PreCode (Phases 29-36)
Next Section: 0e Frontend (Phases 37-42)
---

## Summary

0d PreCode is COMPLETE. Database schema, API surface, repository structure, tech stack, and coding standards are consolidated and ready for frontend specification.

## Key Artifacts Created

| Artifact | Location | Status |
|----------|----------|--------|
| DATABASE-SCHEMA.md | /admin/0d/ | APPROVED |
| MIGRATION-STRATEGY.md | /admin/0d/ | APPROVED |
| API-SURFACE.md | /admin/0d/ | APPROVED |
| LIBRARY-STRUCTURE.md | /admin/0d/ | APPROVED (or N/A) |
| REPO-STRUCTURE.md | /admin/0d/ | APPROVED |
| TECH-STACK.md | /admin/0d/ | APPROVED |
| ENVIRONMENT.md | /admin/0d/ | APPROVED |
| CODING-STANDARDS.md | /admin/0d/ | APPROVED |
| PRECODE-DECISION-NOTES.md | /admin/0d/ | APPROVED |

## PreCode Summary

| Area | Status | Notes |
|------|--------|-------|
| Database Schema | Consolidated | {{n}} tables |
| API Surface | Consolidated | {{n}} endpoints |
| Repository Structure | Defined | {{structure summary}} |
| Tech Stack | Locked | {{key technologies}} |
| Environment Config | Complete | {{n}} environments |
| Coding Standards | Approved | {{standards summary}} |

## Key Decisions Made

{{List significant PreCode decisions}}

## Known Issues or Deferred Items

{{Any gaps, risks, or items flagged for 0e attention}}

## Handoff Verification

- [ ] All Phase 29-35 outputs approved
- [ ] No unresolved conflicts
- [ ] "Different AI" test passed
- [ ] PRECODE-CHECKLIST.md shows all checks pass
- [ ] Nothing required is undocumented

**Ready to begin 0e Frontend.**
```

---

## NEXT SECTION

After Phase 36 approval → **0e. Frontend**

0e will:
- Accept visual mockups as input
- Create FRONTEND-BACKEND-CONTRACT.md
- Spec pages and components
- Map frontend to API endpoints from API-SURFACE.md

---
