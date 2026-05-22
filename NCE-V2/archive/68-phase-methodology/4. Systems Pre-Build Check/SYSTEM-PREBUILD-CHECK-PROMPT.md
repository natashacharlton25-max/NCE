# PHASE 4: SYSTEMS PRE-BUILD CHECK

---
Phase: 4
Name: Systems Pre-Build Check
Location: NCE-V2/NCE V2.0 Spec & Build/4. Systems Pre-Build Check/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are an architecture reviewer validating the 27 NCE-V2 systems before they are materialised into per-system folders + SYSTEM-NOTES.md files (Phase 5).

You are NOT designing, proposing alternatives, or solving problems — only flagging issues.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md) — pass methodology
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker boundaries (per-system Worker grouping affects LOC budget)
4. [REFINEMENTS.md](../../REFINEMENTS.md)
5. [FileTree-v2.md](../../FileTree-v2.md) — 27-system list
6. `NCE-V2/admin/SYSTEMS-CLARIFICATION.md` (Phase 3 output)

---

## RUNTIME CONTEXT

- **2000 LOC band** per runtime file (per Project-Intent.md)
- TypeScript 1.3–1.5× more verbose than Python — apply when porting Python-era estimates
- Each system gets its own Worker (except `services`/`system`/`state`/`library` grouped into `platform`)

---

## CHECKLIST CRITERIA (NCE-V2 adapted)

For each of the 27 systems, evaluate:

### 1. Purpose is clear and focused
- Can you understand what this system owns in one sentence?
- Is it ONE responsibility, not several?
- **Flag if:** Purpose is vague, compound, or requires multiple sentences

### 2. Justification relates to project intent
- Does this system directly support Project-Intent.md's stated purpose?
- Is it necessary for the stated success criteria?
- **Flag if:** System seems nice-to-have or doesn't trace back to intent

### 3. Scope likely fits under ~2000 LOC
- Based on the responsibility description, is this achievable in ~2000 lines (TS, runtime only)?
- Apply file-exclusion rules (`*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments)
- Apply TS verbosity multiplier when porting prior estimates
- **Flag if:** Responsibility seems too broad, or includes multiple complex concerns

### 4. No overlap with other systems
- Does this system's responsibility clearly differ from all other 27?
- Could there be confusion about which system owns a particular concern?
- Specifically watch for: brand/ vs content/, ai/ vs library/, integrations/ vs services/
- **Flag if:** Two systems seem to share responsibility, or boundaries are unclear

### 5. No obvious missing peer systems
- Given the 27 systems exist, is there an obvious companion that's missing?
- **Flag if:** Pass 0 will likely raise "missing capability" — surface here instead

### 6. Output form is clear and consistent
- Does the system have a coherent output form (JSON / rendered / library entry / metadata)?
- Does the output-boundary rule hold?
- **Flag if:** A system produces rendered web output (boundary violation — that's Astro's job)

### 7. Library ownership (if applicable) is clear
- For library-owning systems (brand, content, ai, template, social, assets, etc.): is the library list explicit?
- D1 binding names declared?
- **Flag if:** Library ownership ambiguous between candidate systems

---

## RULES

1. Do **NOT** propose new systems — only flag that something might be missing
2. Do **NOT** rename systems — only flag that a name is problematic
3. Do **NOT** redesign — only flag issues
4. Be conservative — when in doubt, flag for human review
5. Be specific — "needs work" is useless; explain what's wrong
6. Apply the 2000 LOC band and output-boundary rule throughout
7. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

> **Logging rule:** Each flagged issue goes in `NCE-V2/admin/PASS-DECISION-NOTES.md`.

---

## OUTPUT LOCATION

```
NCE-V2/admin/SYSTEMS-PREBUILD-CHECK.md
```

---

## END CONDITION

- [ ] All 27 systems reviewed against 7 checklist items
- [ ] Each system marked APPROVED or NEEDS REVISION
- [ ] Flagged issues logged in `PASS-DECISION-NOTES.md`
- [ ] Overall verdict: ALL APPROVED / REVISIONS REQUIRED
- [ ] Status: Draft Complete – Awaiting Review

**Next (if APPROVED):** Proceed to Phase 5 (Systems Build)
**Next (if REVISIONS REQUIRED):** Return to Phase 3 (System Identification) — but for NCE-V2 this should be rare since FileTree-v2.md is the canonical list

---

## TEMPLATES (enriched for NCE-V2)

- [SYSTEMS-PREBUILD-CHECK-TEMPLATE.md](./SYSTEMS-PREBUILD-CHECK-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/admin/SYSTEMS-CLARIFICATION.md` (Phase 3 output)
- `NCE-V2/Project-Intent.md`
- `NCE-V2/FileTree-v2.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- 2000 LOC band (vs B's stock 1500) is the right call for TS — confirm?
- Should "missing peer systems" check ever override FileTree-v2.md, or treat FileTree-v2 as final?
- May this draft be promoted to "Approved"?
