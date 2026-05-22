# PHASE 2: PROJECT INTENTION

---
Phase: 2
Name: Project Intention
Location: NCE-V2/NCE V2.0 Spec & Build/2. Project Intention/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are formalising the project's intention into a structured artefact.

For NCE-V2: **`NCE-V2/Project-Intent.md` already exists and IS the project intention**. This phase's job is to either (a) place it correctly into the methodology's admin folder for cross-reference, or (b) extract from it any structural gaps and surface them.

You are NOT redesigning intent. The user has already authored it.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md) — IS the project intention; canonical source
2. [CLAUDE.md](../../../CLAUDE.md) — AI collaboration contract; status vocabulary
3. [Project-overview.md](../../admin/PROJECT-OVERVIEW.md) — Phase 1 output (transformed from Project-Intent.md)

---

## TASK

For NCE-V2:

1. Verify `NCE-V2/Project-Intent.md` exists and is APPROVED
2. Run the **Project Intention checklist** against it:
   - Purpose stated (fundamental why)
   - Intended outcome described (end-state, not features)
   - Success criteria verifiable
   - Non-goals explicit
   - Guiding principles documented
   - Trade-offs accepted
   - Assumptions stated
3. For any missing section, flag it as a gap in `NCE-V2/admin/PASS-DECISION-NOTES.md`
4. Produce a **Project Intention check artefact** at `NCE-V2/admin/PROJECT-INTENTION-CHECK.md` confirming the validation

The existing `Project-intention.md` template in this folder may guide what sections are expected.

> **Logging rule:** Gaps found in Project-Intent.md (missing trade-offs, missing guiding principles, etc.) go in `PASS-DECISION-NOTES.md`.

---

## RULES

1. Do **NOT** rewrite Project-Intent.md — the user owns it
2. Do **NOT** propose changes to intent — only validate completeness against the checklist
3. Do **NOT** add features or assumptions
4. If a section is missing, flag the gap; don't fill it
5. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/admin/PROJECT-INTENTION-CHECK.md
```

A verification artefact (not a replacement for Project-Intent.md).

---

## END CONDITION

- [ ] PROJECT-INTENTION-CHECK.md created at `NCE-V2/admin/PROJECT-INTENTION-CHECK.md`
- [ ] All Project Intention checklist items reviewed against Project-Intent.md
- [ ] Any gaps logged in `PASS-DECISION-NOTES.md`
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 3 (System Identification)

---

## TEMPLATE

See `Project-intention.md` in this folder for the canonical intention structure (used for the checklist).

---

## INPUTS

For NCE-V2:
- `NCE-V2/Project-Intent.md` (the actual intention, already authored)
- `NCE-V2/admin/PROJECT-OVERVIEW.md` (Phase 1 output)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- For NCE-V2, is Project-Intent.md the final intention source, or do you want a parallel PROJECT-INTENTION.md in admin? (Default: keep Project-Intent.md at NCE-V2/ root, generate only a CHECK artefact in admin.)
- May this draft be promoted to "Approved"?
