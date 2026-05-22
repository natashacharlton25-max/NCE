# Subsystem Pre-build Check

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Parent System
{{System Name}}

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **LOC band** | Low <1500 / Medium 1500–2000 / High >2000 |
| **Output-boundary rule applied?** | Yes / Mixed / No |
| **Parent output form** | JSON / rendered / library entry / metadata |

---

## Review Summary

| Total Subsystems | Approved | Needs Revision | Blocked |
|------------------|----------|-----------------|---------|
| {{n}} | {{n}} | {{n}} | {{n}} |

---

## Subsystem Reviews

### {{Subsystem Name}}

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered / library entry / metadata |
| **Storage Touch** | none / D1 / R2 / KV / DO / Queue |
| **Estimated LOC** | ~{{number}} |
| **Size Band** | Low / Medium / High |

Checklist:
- [ ] Purpose is clear and focused (one sentence, one responsibility)
- [ ] Responsibility fits within parent system's In Scope
- [ ] Justification for separation is valid (not "cleaner code")
- [ ] Helps stay under ~2000 LOC
- [ ] No overlap with sibling subsystems
- [ ] Does not expand parent system's scope
- [ ] Output Form coherent and consistent with parent
- [ ] Output-boundary direction respected

**Status:** APPROVED | NEEDS REVISION

**Notes:** (Brief; log details in `PASS-DECISION-NOTES.md` if needed)

---

(repeat for each subsystem)

---

## Overall Verdict

**Status:** ALL APPROVED | REVISIONS REQUIRED

**Summary:** (One sentence)

**Action Required:** (What needs to happen next)

---

## Dependency
Requires approved: **Subsystems Clarification** for `{{System Name}}` (Phase 6 output)

## Next Step
- If ALL APPROVED → proceed to: **Phase 8 — Subsystem Build**
- If REVISIONS REQUIRED → return to Phase 6 with logged issues
