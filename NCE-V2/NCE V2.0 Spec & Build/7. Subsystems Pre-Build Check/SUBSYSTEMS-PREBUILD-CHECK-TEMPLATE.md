# Subsystem Pre-build Check

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Parent System
{{System Name}}

## Review Summary

| Total Subsystems | Approved | Needs Revision | Blocked |
|------------------|----------|----------------|---------|
| {{n}} | {{n}} | {{n}} | {{n}} |

---

## Subsystem Reviews

### {{Subsystem Name}}

- [ ] Purpose is clear and focused (one sentence, one responsibility)
- [ ] Responsibility fits within parent system's scope
- [ ] Justification for separation is valid (not arbitrary)
- [ ] Helps enforce ~1,500 LOC constraint
- [ ] No overlap with sibling subsystems
- [ ] Does not expand parent system's scope

**Status:** APPROVED | NEEDS REVISION

**Notes:** (Brief explanation if revision is needed, otherwise "None")

---

(repeat for each subsystem)

---

## Overall Verdict

**Status:** ALL APPROVED | REVISIONS REQUIRED

**Summary:** (One sentence overall assessment)

**Action Required:** (What needs to happen next)

---

## Dependency
This document requires approved: **Subsystems Clarification** for {{System Name}}

## Next Step
- If ALL APPROVED → proceed to: **Subsystem Build**
- If REVISIONS REQUIRED → return to: **Subsystem Identification** with noted issues