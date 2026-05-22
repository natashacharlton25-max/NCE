# PHASE 1: PROJECT OVERVIEW

---
Phase: 1
Name: Project Overview
Location: NCE-V2/NCE V2.0 Spec & Build/1. Project Overview/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are a product clarification assistant.

For NCE-V2: most of this phase's content already exists in `NCE-V2/Project-Intent.md`. Your job is to **transform** the existing intent doc into the formal Project Overview artefact, not to discover from scratch.

You are NOT designing, architecting, or solving anything yet.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md) — Primary source for NCE-V2's purpose, principles, non-goals
2. [CLAUDE.md](../../../CLAUDE.md) — AI collaboration contract; status vocabulary
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Platform constraint context (TypeScript on Cloudflare Workers)
4. [FileTree-v2.md](../../FileTree-v2.md) — Canonical 27-system list (helpful for "Context" framing)

---

## TASK

For NCE-V2:

1. Read `NCE-V2/Project-Intent.md` as the authoritative source
2. Transform it into a **Project Overview** artefact using the template
3. Where a section in the Overview maps directly to Project-Intent content, copy verbatim or paraphrase tightly — don't reinterpret
4. Where Project-Intent doesn't speak to a section (e.g. Initial Constraints — Time/Budget), write "TBD" or "Not specified"
5. Log any inferred content in `NCE-V2/admin/PASS-DECISION-NOTES.md`

> **Logging rule:** If the transformation requires any interpretation beyond mechanical re-formatting, log it.

---

## RULES

1. Do **NOT** propose solutions — no architecture, no tech stack beyond what Project-Intent already states (TS-on-Workers)
2. Do **NOT** invent requirements not in Project-Intent.md
3. Do **NOT** add features
4. Preserve uncertainty — if something is unknown in Project-Intent, write "Unknown" or "TBD"
5. One target user only — if Project-Intent suggests multiple, ask the user to pick the primary
6. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/admin/PROJECT-OVERVIEW.md
```

Project-wide foundation artefact; lives alongside other admin docs (PASS-PROGRESS, PASS-DECISION-NOTES).

---

## END CONDITION

- [ ] PROJECT-OVERVIEW.md created at `NCE-V2/admin/PROJECT-OVERVIEW.md`
- [ ] All sections filled (TBD/Unknown where Project-Intent doesn't cover it)
- [ ] Status: Draft Complete – Awaiting Review
- [ ] Logged any inferred content in `PASS-DECISION-NOTES.md`

**Next:** Proceed to Phase 2 (Project Intention)

---

## TEMPLATE

See `project-overview.md` in this folder for the template structure. Use it verbatim with NCE-V2 content from Project-Intent.md.

---

## INPUTS

For NCE-V2: the primary input is `NCE-V2/Project-Intent.md` (existing canonical doc). No "raw project input" discovery step needed — the user has already authored the project intention.

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- For NCE-V2, is Project-Intent.md a sufficient stand-alone source for the Project Overview transformation, or do you want a separate "raw project input" pass first?
- May this draft be promoted to "Approved"?
