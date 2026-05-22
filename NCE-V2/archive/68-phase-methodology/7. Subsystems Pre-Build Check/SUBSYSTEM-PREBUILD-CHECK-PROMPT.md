# PHASE 7: SUBSYSTEMS PRE-BUILD CHECK

---
Phase: 7
Name: Subsystems Pre-Build Check (per parent system)
Location: NCE-V2/NCE V2.0 Spec & Build/7. Subsystems Pre-Build Check/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are an architecture reviewer validating each parent system's subsystem set before they are materialised into per-subsystem folders + SUBSYSTEM-NOTES.md (Phase 8).

You are NOT designing, proposing alternatives, or solving problems — only flagging issues.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [FileTree-v2.md](../../FileTree-v2.md) — canonical subsystem list
4. `NCE-V2/specs/<parent>/SYSTEM-NOTES.md` and `SUBSYSTEMS-CLARIFICATION.md` (Phases 5+6 outputs)

---

## RUNTIME CONTEXT

- **2000 LOC band** per subsystem
- TypeScript 1.3–1.5× verbosity over Python

---

## CHECKLIST CRITERIA (NCE-V2 adapted)

For each subsystem in a parent system, evaluate:

### 1. Purpose is clear and focused
- One sentence ownership statement?
- One responsibility, not several?
- **Flag if:** Vague or compound

### 2. Responsibility fits within parent system
- Within the parent's In Scope per SYSTEM-NOTES.md?
- **Flag if:** Belongs to a different system or expands parent's scope

### 3. Justification for separation is valid
- Real reason for this split (not "cleaner code")?
- **Flag if:** Justification is arbitrary

### 4. Helps stay under ~2000 LOC
- Meaningfully reduces complexity?
- Subsystem itself stays under ~2000 LOC?
- **Flag if:** Subsystem implies >2000 LOC — log exception or note future split

### 5. No overlap with sibling subsystems
- Distinct from siblings?
- **Flag if:** Two subsystems could claim the same responsibility

### 6. Does not expand parent system's scope
- Stays within what the parent already owns?
- **Flag if:** Introduces responsibilities not in parent's In Scope

### 7. Output Form coherent (NCE-V2-specific)
- Does the subsystem have a clear output form (JSON / rendered / library entry / metadata)?
- Does it align with the parent's output form?
- **Flag if:** Subsystem produces rendered web output but parent is JSON-emitting (boundary violation)

---

## RULES

1. Do **NOT** propose new subsystems — only flag potential missing
2. Do **NOT** rename — only flag name issues
3. Do **NOT** redesign — only flag
4. Be conservative; when in doubt, flag
5. Apply 2000 LOC band and output-boundary rule
6. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

> **Logging rule:** Each flag goes in `PASS-DECISION-NOTES.md`.

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{parent-system}}/SUBSYSTEMS-PREBUILD-CHECK.md
```

One file per parent system (27 files total).

---

## END CONDITION

- [ ] All 27 systems' subsystems reviewed against 7 criteria
- [ ] Each subsystem marked APPROVED or NEEDS REVISION
- [ ] Flagged issues logged in `PASS-DECISION-NOTES.md`
- [ ] Status: Draft Complete – Awaiting Review

**Next (if APPROVED):** Proceed to Phase 8 (Subsystem Build)

---

## TEMPLATES (enriched for NCE-V2)

- [SUBSYSTEMS-PREBUILD-CHECK-TEMPLATE.md](./SUBSYSTEMS-PREBUILD-CHECK-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/specs/<parent>/SYSTEM-NOTES.md` (Phase 5)
- `NCE-V2/specs/<parent>/SUBSYSTEMS-CLARIFICATION.md` (Phase 6)
- `NCE-V2/FileTree-v2.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Output-form check at subsystem level — keep or simplify to parent-level only?
- May this draft be promoted to "Approved"?
