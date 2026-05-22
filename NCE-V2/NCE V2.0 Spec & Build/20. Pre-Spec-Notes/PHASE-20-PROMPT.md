# PHASE 20: PRE-SPEC NOTES

---
Phase: 20
Name: Pre-Spec Notes (consolidated brief per component before full spec)
Location: NCE-V2/NCE V2.0 Spec & Build/20. Pre-Spec-Notes/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PRE-SPEC-NOTES-TEMPLATE.md (A's pre-spec notes template, sections folded in)
  - C:/Users/NCE/prompts/PRE-SPEC-NOTES-CHECKLIST.md (A's pre-spec checklist, sections folded in)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You produce a focused **Pre-Spec Notes** brief per component that consolidates Pass 0–4 findings, grounding/contract/dependency content, and project-wide constraints into a single document that the spec writer (Phase 21/22/23) can use as its primary input.

You are NOT writing full specs — only the brief that feeds full spec writing.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../../FileTree-v2.md)
5. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md)
6. `NCE-V2/specs/<component>/PASS-NOTES.md` (Phase 19 output, primary)
7. `NCE-V2/specs/<component>/SYSTEM.md` / `SUBSYSTEM.md` / `EXTERNAL-INTEGRATION.md` (Pass 1)
8. `NCE-V2/specs/<component>/CONTRACT.md` (Pass 2)

---

## TASK

For each component (27 systems + subsystems + integrations + library entities identified in Phase 25):

1. Read PASS-NOTES.md + Pass 1/2 outputs
2. Produce a Pre-Spec Notes brief covering 12 sections (per template):
   1. Identity (name, type, parent, output form)
   2. Purpose (1-paragraph)
   3. Functions (public API surface, names + intent)
   4. Behaviour notes
   5. Constraints + design rationale
   6. Error categories (no codes yet)
   7. Open decisions (need spec-phase resolution)
   8. Configuration / runtime knobs
   9. Data types + schemas
   10. Dependencies (with binding types)
   11. Guarantees
   12. Examples / scenarios

Internal/external split:
- Internal components (systems, subsystems, libraries) use `PRE-SPEC-NOTES-TEMPLATE.md`
- External integrations use `PRE-SPEC-NOTES-EXTERNAL-TEMPLATE.md`

Use `PRE-SPEC-NOTES-CHECKLIST.md` / `PRE-SPEC-NOTES-EXTERNAL-CHECKLIST.md` to verify completeness before approval.

> **Logging rule:** Anything inferred (vs lifted verbatim from source) goes in `PASS-DECISION-NOTES.md`.

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{component}}/PRE-SPEC-NOTES.md
NCE-V2/specs/integrations/{{provider}}/{{service}}/PRE-SPEC-NOTES.md
```

---

## MANDATORY RULES

- Do **NOT** invent functionality
- Do **NOT** advance into full spec territory (keep at "notes" level — concrete enough to drive specs, abstract enough to leave room for spec-writer decisions)
- Apply output-boundary rule
- Respect 2000 LOC band for any subsystem-implied file sizes
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Every component has a `PRE-SPEC-NOTES.md`
- [ ] Checklist verified per component
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 21 (Internal Component Specs) for systems/subsystems; Phase 22 for integrations; Phase 23 for libraries

---

## TEMPLATES

- [PRE-SPEC-NOTES-TEMPLATE.md](./PRE-SPEC-NOTES-TEMPLATE.md) (internal components)
- [PRE-SPEC-NOTES-EXTERNAL-TEMPLATE.md](./PRE-SPEC-NOTES-EXTERNAL-TEMPLATE.md) (external integrations)
- [PRE-SPEC-NOTES-CHECKLIST.md](./PRE-SPEC-NOTES-CHECKLIST.md)
- [PRE-SPEC-NOTES-EXTERNAL-CHECKLIST.md](./PRE-SPEC-NOTES-EXTERNAL-CHECKLIST.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
