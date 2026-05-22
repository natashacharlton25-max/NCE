# PHASE 3: SYSTEM IDENTIFICATION

---
Phase: 3
Name: System Identification
Location: NCE-V2/NCE V2.0 Spec & Build/3. System Identification/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are formalising the project's system list into a Systems Clarification artefact.

For NCE-V2: **`NCE-V2/FileTree-v2.md` already contains the canonical 27-system list** (plus `lib/` utilities). This phase's job is to transform that into the formal SYSTEMS-CLARIFICATION.md artefact with per-system purpose + justification.

You are NOT inventing systems. The system list has been finalised.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md) — AI collaboration contract
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker topology, storage primitives
4. [REFINEMENTS.md](../../REFINEMENTS.md) — Active architectural refinements (e.g. brand/ absorbing colours/typography/visual)
5. [FileTree-v2.md](../../FileTree-v2.md) — **The canonical 27-system list (authoritative)**
6. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) — D1-based library template

---

## RUNTIME CONTEXT

- **Language:** TypeScript on Cloudflare Workers
- **Per-system organisation:** one Worker per system; `services`/`system`/`state`/`library` grouped into `platform` Worker
- **Storage primitives:** D1 (libraries), R2 (assets), KV, DO, Vectorize

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**

When stating a system's purpose, include its output form (JSON / rendered artefact / library entry / metadata / utility) so Pass 0 can apply the boundary rule cleanly.

---

## TASK

For NCE-V2:

1. Read `FileTree-v2.md` — extract the 27 top-level systems (excluding `lib/` which is utilities, no Pass 0)
2. For each system, write a Systems Clarification entry containing:
   - System name (verbatim from FileTree-v2)
   - Purpose: one sentence "Owns responsibility for [doing what]"
   - Output Form: JSON / rendered / library entry / metadata / mixed
   - Justification: why this responsibility deserves a separate system; expected size (within ~2000 LOC band)
3. Also identify external integration providers from FileTree-v2's `integrations/` subsystems → produce an EXTERNAL-INTEGRATIONS-REGISTER.md

> **Logging rule:** Any system whose purpose is ambiguous from FileTree-v2 alone — log in `PASS-DECISION-NOTES.md` and flag for review.

---

## RULES

1. Do **NOT** invent systems — use only the 27 in FileTree-v2.md
2. Do **NOT** rename systems — use exact folder names from FileTree-v2
3. Do **NOT** propose changes to the system list
4. For `lib/`, skip — it's utilities, not a Pass 0 target
5. For library-owning systems, note which library/libraries they own (per LIBRARY-TEMPLATE.md)
6. Apply the output-boundary rule when stating output form
7. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/admin/SYSTEMS-CLARIFICATION.md          # 27-system list with purpose + justification
NCE-V2/admin/EXTERNAL-INTEGRATIONS-REGISTER.md # 15 providers from integrations/ system
```

Project-wide foundation artefacts.

---

## END CONDITION

- [ ] SYSTEMS-CLARIFICATION.md created with all 27 systems
- [ ] EXTERNAL-INTEGRATIONS-REGISTER.md created with all 15 providers
- [ ] Each system has Output Form declared
- [ ] Each library-owning system notes its libraries
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 4 (Systems Pre-Build Check)

---

## TEMPLATES (enriched for NCE-V2)

- [SYSTEM-CLARIFICATIONS-TEMPLATE.md](./SYSTEM-CLARIFICATIONS-TEMPLATE.md)
- [external-integrations-register.md](./external-integrations-register.md)

---

## INPUTS

- `NCE-V2/FileTree-v2.md` (canonical system list — primary input)
- `NCE-V2/Project-Intent.md`
- `NCE-V2/admin/PROJECT-OVERVIEW.md`, `PROJECT-INTENTION-CHECK.md` (Phase 1+2 outputs)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- For NCE-V2, FileTree-v2.md is the authoritative system list. Should this phase ever propose system list changes, or strictly transform-only?
- May this draft be promoted to "Approved"?
