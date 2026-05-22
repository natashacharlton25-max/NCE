# PHASE 6: SUBSYSTEMS IDENTIFICATION

---
Phase: 6
Name: Subsystems Identification (per parent system)
Location: NCE-V2/NCE V2.0 Spec & Build/6. Subsystems Identification/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are formalising the subsystem decomposition for each NCE-V2 system.

For NCE-V2: **`FileTree-v2.md` already lists subsystems for each of the 27 systems** (e.g. `brand/` has 31 subsystems, `ai/` has 14, etc.). This phase's job is to transform that into formal Subsystems Clarification artefacts per parent system.

You are NOT inventing subsystems. The subsystem list is already finalised in FileTree-v2.md.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [FileTree-v2.md](../../FileTree-v2.md) — **The canonical subsystem list for every system**
4. `NCE-V2/specs/<system>/SYSTEM-NOTES.md` (Phase 5 output, per system)

---

## RUNTIME CONTEXT

- **2000 LOC band** per subsystem
- TypeScript verbosity multiplier 1.3–1.5×
- Subsystem files live alongside parent system in `NCE-V2/specs/<system>/<subsystem-name>/` directory

---

## TASK

For each of the 27 parent systems (alphabetical A→Z):

1. Read FileTree-v2.md's subsystem list for the parent
2. Note: some "subsystems" in FileTree-v2 are nested groupings (e.g. `brand/core/`, `brand/identity/`, `brand/audience/` — these are subsystem-categories containing further subsystems)
3. For each subsystem:
   - Name (verbatim from FileTree-v2 — typically `BrandManager.ts` → "Brand Manager")
   - Purpose: one sentence
   - Justification: why this responsibility deserves separation; expected LOC band

> **Logging rule:** If FileTree-v2 doesn't clearly imply a subsystem's purpose, log "TBD — needs clarification" and surface it.

---

## SYSTEMS WITH ZERO SUBSYSTEMS

For NCE-V2, all 27 systems have subsystems per FileTree-v2.md. There are no zero-subsystem systems in this tree.

---

## RULES

1. **Use ONLY the subsystems listed in FileTree-v2.md** — no inventions, no merges, no splits
2. Do **NOT** rename — use exact names from FileTree-v2 (transform CamelCase to "Title Case" for human-readable names if needed, but file names stay as in FileTree-v2)
3. Do **NOT** propose changes to the subsystem list
4. **Watch the 2000 LOC band** — if a subsystem in FileTree-v2 implies >2000 LOC, log a flag for Pass 0
5. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{parent-system}}/SUBSYSTEMS-CLARIFICATION.md
```

One file per parent system (27 files total).

---

## END CONDITION

- [ ] All 27 systems have `SUBSYSTEMS-CLARIFICATION.md`
- [ ] Each subsystem from FileTree-v2 is listed with purpose + justification
- [ ] Any LOC-band flags logged in `PASS-DECISION-NOTES.md`
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 7 (Subsystems Pre-Build Check)

---

## TEMPLATES (enriched for NCE-V2)

- [SUBSYSTEMS-CLARIFICATION-TEMPLATE.md](./SUBSYSTEMS-CLARIFICATION-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/FileTree-v2.md` (canonical subsystem list — primary input)
- `NCE-V2/specs/<system>/SYSTEM-NOTES.md` (Phase 5 outputs)
- `NCE-V2/Project-Intent.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- For NCE-V2, FileTree-v2.md is the authoritative subsystem source. This phase is transform-only — confirm?
- Should `brand/`'s nested groupings (core/, identity/, audience/, etc.) be treated as a single "sub-subsystem level" requiring its own template variant, or merged into a flat per-system list?
- May this draft be promoted to "Approved"?
