# PHASE 21: INTERNAL COMPONENT SPECS

---
Phase: 21
Name: Internal Component Specs (implementation-ready specs for systems + subsystems)
Location: NCE-V2/NCE V2.0 Spec & Build/21. Internal-Component-Specs/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PASS3PROMPT.MD (A's "full specs/schemas" mapped here)
  - C:/Users/NCE/prompts/SPECPROMPT.MD (A's full-spec writing prompt absorbed)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are writing implementation-ready specifications for NCE-V2 internal components (systems, subsystems). Each component produces 10 focused spec files in a `spec/` folder.

This phase takes PRE-SPEC-NOTES.md (Phase 20 output) and expands it into 10 spec files.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../../FileTree-v2.md)
5. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) — for STORAGE.md when library-touching
6. `NCE-V2/specs/<component>/PRE-SPEC-NOTES.md` (Phase 20 — primary source)
7. `NCE-V2/specs/<component>/PASS-NOTES.md` (Phase 19 — context)
8. `NCE-V2/specs/<component>/SYSTEM.md` + `CONTRACT.md` (Pass 1–2)

---

## RUNTIME CONTEXT

- **TypeScript** strict mode; types live in `*.types.ts` (excluded from runtime LOC count)
- **Cloudflare Workers** runtime; 5-min CPU, 128 MB memory
- **D1** for libraries (no native JSON column — store as TEXT, query with `json_*()`)
- **R2** for binaries; **KV** for cache/config; **DO** for stateful coordination; **Vectorize** for embeddings

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

The 10 spec files must respect:
- A `website/` system's FUNCTIONS.md returns JSON, not HTML strings
- A `renderers/` system's FUNCTIONS.md returns rendered artefact bytes/strings (PDF/DOCX/HTML for non-Astro/Markdown)
- An `email/` system's FUNCTIONS.md composes rendered HTML; handoff to `integrations/EmailitIntegration` for send
- A library-touching system's STORAGE.md is D1 + R2 (not local filesystem); FUNCTIONS.md accesses libraries via `library/Librarian`

---

## TASK

**For EACH internal component (27 systems + each subsystem in FileTree-v2.md, alphabetical A→Z):**

1. Read the component's `PRE-SPEC-NOTES.md` (Phase 20)
2. Create `spec/` folder in `NCE-V2/specs/<component>/`
3. Create the 10 spec files using templates
4. Get approval before moving to next component

> **Logging rule:** Spec gaps logged in each component's `INDEX.md` "Spec Gaps" section; cross-component conflicts logged in `NCE-V2/admin/PASS-DECISION-NOTES.md`.

---

## SPEC FILES (10 per component)

| File | Purpose | Source (PRE-SPEC Section) | Target LOC | NCE-V2 Notes |
|------|---------|---------------------------|------------|---------------|
| INDEX.md | Quick reference, status, spec gaps | 1, 4, 12 | ~80 | Includes Output Form + Library Ownership |
| OVERVIEW.md | Purpose, boundaries, guarantees | 1, 2, 5, 11 | ~150 | State output-boundary direction |
| FUNCTIONS.md | Public API with TS signatures | 3, 4 | ~300 | TypeScript signatures (input/output types) |
| TYPES.md | All TS interface/type definitions | 9 | ~200 | Goes in `*.types.ts` (excluded from runtime LOC) |
| BEHAVIOUR.md | Process flows, state transitions | 3, 8 | ~200 | Worker request/response, async D1 calls |
| ERRORS.md | Error codes, categories, recovery | 6 | ~150 | Categories from Pass 2 + concrete codes here |
| CONFIG.md | Configuration options, env vars, Worker bindings | 8 | ~100 | D1/R2/KV binding names; Worker secret names |
| STORAGE.md | D1 schema, R2 patterns | 9 | ~150 | For library-owning systems: per LIBRARY-TEMPLATE v2.0.0 (D1 tables `_index`, `{items}`, `{items}_latest` view, optional FTS5). Skip if no storage touch. |
| DECISIONS.md | Design rationale | 5, 7, 8 | ~100 | |
| EXAMPLES.md | Full request/response examples | New | ~200 | TypeScript code samples |

---

## PROCESS ORDER

Per component (one at a time):
```
{{system}}/spec/
├── INDEX.md (first — sets context)
├── OVERVIEW.md
├── FUNCTIONS.md
├── TYPES.md
├── BEHAVIOUR.md
├── ERRORS.md
├── CONFIG.md
├── STORAGE.md (skip if no D1/R2 touch)
├── DECISIONS.md
└── EXAMPLES.md (last — proves it works)
```

Then subsystems, then move to next system.

**Full order:** Systems with their subsystems (alphabetical), libraries (Phase 23 after Phase 25 identifies them).

---

## WHEN ISSUES ARISE

### Minor (doesn't break contracts)
- Add to component's `INDEX.md` "Build Notes & Constraints"
- Continue
- Resolve in Phase 25 (Post-Spec Analysis)

### Major (breaks contracts, affects multiple subsystems)
- Stop subsystem work
- Revise system spec files
- Bump version in INDEX.md
- Log in `NCE-V2/admin/PASS-DECISION-NOTES.md`
- Continue

---

## MANDATORY RULES

- Do **NOT** invent functionality not in PRE-SPEC-NOTES.md
- Do **NOT** skip files (mark N/A in INDEX.md if not applicable)
- Do **NOT** exceed ~300 LOC per spec file — split if needed
- Apply the output-boundary rule and storage model context
- TypeScript signatures everywhere (not Python pseudocode)
- Reference PRE-SPEC-NOTES section numbers when porting content
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{system}}/
├── spec/
│   ├── INDEX.md
│   ├── OVERVIEW.md
│   ├── FUNCTIONS.md
│   ├── TYPES.md
│   ├── BEHAVIOUR.md
│   ├── ERRORS.md
│   ├── CONFIG.md
│   ├── STORAGE.md (if applicable)
│   ├── DECISIONS.md
│   └── EXAMPLES.md
│
└── {{subsystem}}/spec/
    └── (same 10 files)
```

---

## APPROVAL GATE

Per component:
- [ ] All 10 spec files created (or marked N/A in INDEX.md)
- [ ] No critical gaps in INDEX.md "Spec Gaps"
- [ ] Output-boundary direction respected
- [ ] Library access patterns correct (`library/Librarian` mediated)
- [ ] Human approves

---

## END CONDITION

Phase 21 is COMPLETE when:
- [ ] All 27 systems have `spec/` folder with all 10 files
- [ ] All subsystems have `spec/` folder with all 10 files
- [ ] All approved
- [ ] All issues logged or resolved
- [ ] PASS-PROGRESS.md updated; Phase 21 marked COMPLETE

**Next:** Phase 22 (External Integration Specs)

---

## TEMPLATES (enriched for NCE-V2)

10 templates in this folder (INDEX-TEMPLATE.md, OVERVIEW-TEMPLATE.md, FUNCTIONS-TEMPLATE.md, TYPES-TEMPLATE.md, BEHAVIOUR-TEMPLATE.md, ERRORS-TEMPLATE.md, CONFIG-TEMPLATE.md, STORAGE-TEMPLATE.md, DECISIONS-TEMPLATE.md, EXAMPLES-TEMPLATE.md).

---

## INPUTS

- `NCE-V2/specs/<component>/PRE-SPEC-NOTES.md` (Phase 20)
- `NCE-V2/specs/<component>/PASS-NOTES.md` (Phase 19)
- `NCE-V2/specs/<component>/SYSTEM.md`, `CONTRACT.md`, `ADMIN.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
