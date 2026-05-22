# Systems Pre-build Check

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Target system count** | 27 (per FileTree-v2.md, excluding `lib/`) |
| **LOC band** | Low <1500 / Medium 1500–2000 / High >2000 |
| **TS verbosity multiplier** | 1.3–1.5× over Python |
| **Output-boundary rule applied?** | Yes / Mixed / No |

---

## Review Summary

| Total Systems | Approved | Needs Revision | Blocked |
|---------------|----------|-----------------|---------|
| 27 | {{n}} | {{n}} | {{n}} |

---

## System Reviews (alphabetical)

### {{System Name}}

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered / library entry / metadata / mixed |
| **Worker Grouping** | own Worker / `platform` Worker |
| **Library Ownership** | None / {{D1_BINDING_NAME(s)}} |
| **Estimated LOC** | ~{{number}} |
| **Size Band** | Low (<1500) / Medium (1500–2000) / High (>2000) |

Checklist:
- [ ] Purpose is clear and focused (one sentence, one responsibility)
- [ ] Justification directly relates to project intent
- [ ] Scope likely fits under ~2000 LOC (TS runtime, exclusions applied)
- [ ] No overlap with other proposed systems
- [ ] No obvious missing peer systems
- [ ] Output form is clear and consistent
- [ ] Library ownership (if applicable) is clear (D1 binding name declared)
- [ ] Output-boundary direction respected (no rendered web output from JSON-emitters)

**Status:** APPROVED | NEEDS REVISION

**Notes:** (Brief explanation if revision needed; reference PASS-DECISION-NOTES.md entry if logged.)

---

(repeat for each of the 27 systems)

---

## Overall Verdict

**Status:** ALL APPROVED | REVISIONS REQUIRED

**Summary:** (One sentence)

**Action Required:** (What needs to happen next)

---

## Dependency
Requires approved: **Systems Clarification** (Phase 3 output)

## Next Step
- If ALL APPROVED → proceed to: **Phase 5 — Systems Build**
- If REVISIONS REQUIRED → return to Phase 3 with logged issues (but for NCE-V2 this should be rare since FileTree-v2.md is canonical)
