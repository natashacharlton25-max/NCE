# STAGE 1: PROJECT FRAME

---
Stage: 1 of 5
Name: Project Frame
Methodology: NCE-V2 Reduced Methodology (see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You validate that NCE-V2's project foundation is locked and internally consistent before any spec work begins.

This is a **VALIDATION** stage. You are NOT producing new content. You are confirming that the three foundation docs are current, approved, and mutually consistent.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../CLAUDE.md) §10:

1. [Project-Intent.md](../Project-Intent.md) — Project intention, design principles, non-goals
2. [CLAUDE.md](../../CLAUDE.md) — AI collaboration contract, status vocabulary
3. [FileTree-v2.md](../FileTree-v2.md) — Canonical 27-system list + lib/ utilities
4. [STACK-AND-RUNTIME.md](../STACK-AND-RUNTIME.md) — TypeScript on Cloudflare Workers; D1/R2/KV/DO/Vectorize
5. [REFINEMENTS.md](../REFINEMENTS.md) — Active architectural refinements
6. [docs/templates/LIBRARY-TEMPLATE.md v2.0.0](../docs/templates/LIBRARY-TEMPLATE.md) — D1-based library template

---

## TASK

Produce `NCE-V2/admin/PROJECT-FRAME.md` — a single-page validation record covering three checks:

### Check 1 — Per-doc validity

For each of the three foundation docs (Project-Intent.md, FileTree-v2.md, STACK-AND-RUNTIME.md):
- [ ] Exists at expected path
- [ ] Status field reads APPROVED or equivalent
- [ ] Last updated date is recent enough that content is current
- [ ] Owner is human (per CLAUDE.md §7)

### Check 2 — Cross-doc consistency

- [ ] System list in `FileTree-v2.md` matches concept stated in `Project-Intent.md`
- [ ] Runtime + storage primitives in `STACK-AND-RUNTIME.md` match assumptions in `FileTree-v2.md` (e.g. D1 for libraries, R2 for assets)
- [ ] Output-boundary rule (web=JSON via Astro; PDF/DOCX/email=rendered by NCE-V2) is stated or implied across all three
- [ ] 2000 LOC band per runtime file is stated in `Project-Intent.md`
- [ ] `library/` system in FileTree-v2 matches access patterns described in LIBRARY-TEMPLATE v2.0.0
- [ ] `lib/svg/` is correctly classified as utilities (no Pass 0, no spec required as a system) but as a `lib/` utility group

### Check 3 — Methodology readiness

- [ ] `NCE-V2/methodology/` exists with Stage 1–5 prompts (this file + others)
- [ ] `NCE-V2/admin/` exists for tracking docs
- [ ] `NCE-V2/specs/<27 systems>/` skeleton folders exist
- [ ] `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 exists

---

## OUTPUT LOCATION

```
NCE-V2/admin/PROJECT-FRAME.md
```

Use [PROJECT-FRAME-TEMPLATE.md](./PROJECT-FRAME-TEMPLATE.md) in this folder.

---

## MANDATORY RULES

- Do **NOT** modify the foundation docs in this stage — only validate
- If any check fails, log it in `PROJECT-FRAME.md` and pause; resolve before proceeding to Stage 2
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All Check 1 items pass
- [ ] All Check 2 items pass
- [ ] All Check 3 items pass
- [ ] PROJECT-FRAME.md complete with verdict (FRAME LOCKED / FRAME NEEDS WORK)
- [ ] Human signs off

**Next (if FRAME LOCKED):** Stage 2 — Component Specs

**Next (if FRAME NEEDS WORK):** address the failing check(s), then re-run Stage 1.

---

## TEMPLATE

- [PROJECT-FRAME-TEMPLATE.md](./PROJECT-FRAME-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Does the validation cover what you want checked at frame-lock time?
- May this draft be promoted to "Approved"?
