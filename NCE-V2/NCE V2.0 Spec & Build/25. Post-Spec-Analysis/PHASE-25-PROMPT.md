# PHASE 25: POST-SPEC ANALYSIS

---
Phase: 25
Name: Post-Spec Analysis (cross-cutting analysis after Phase 21–24 specs)
Location: NCE-V2/NCE V2.0 Spec & Build/25. Post-Spec-Analysis/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You analyze the completed component specs (Phases 21–24) for cross-cutting patterns, gaps, dependency issues, type duplication, and error-code conflicts. Output is 5 analysis docs.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
3. [FileTree-v2.md](../../FileTree-v2.md)
4. All component specs (Phases 21–24) under `NCE-V2/specs/`

---

## TASK

Produce 5 analysis documents:

### 1. PATTERN-ANALYSIS.md
Identify recurring patterns across component specs that should become `lib/` utilities. Candidates:
- Same code in 3+ component specs (likely lib/ candidates)
- Cross-cutting concerns (logging, validation, error formatting, date handling)
- For NCE-V2: D1 query patterns may be candidates for `lib/d1-query/`; SVG drawing already in `lib/svg/`

### 2. TYPE-ANALYSIS.md
Find type duplication across components:
- Same interface defined in multiple TYPES.md files → candidate for shared types module
- Inconsistent naming (e.g. `BrandID` in one, `brand_id` in another)
- For NCE-V2: Each library's entry types — should they be shared across consumers via `lib/types/`?

### 3. ERROR-CODE-ANALYSIS.md
Aggregate all error codes from per-component ERRORS.md files:
- Detect conflicts (two systems claiming same error code)
- Detect gaps (failure mode in CONTRACT.md without a code in ERRORS.md)
- Produce input for Phase 26 (`ERROR-CODES.md` project-wide registry)

### 4. DEPENDENCY-ANALYSIS.md
Re-validate dependencies from per-component specs against Pass 3 DEPENDENCY-MAP.md:
- New dependencies introduced by spec work?
- Removed dependencies?
- Circular dependencies introduced?
- Library access bypasses (direct D1 from outside `library/`)?

### 5. GAP-ANALYSIS.md
Aggregate spec gaps from each component's INDEX.md "Spec Gaps":
- Critical gaps blocking implementation
- Nice-to-have gaps for follow-up
- Cross-component gaps (multiple components missing the same info)

---

## OUTPUT LOCATION

```
NCE-V2/specs/analysis/
├── PATTERN-ANALYSIS.md
├── TYPE-ANALYSIS.md
├── ERROR-CODE-ANALYSIS.md
├── DEPENDENCY-ANALYSIS.md
└── GAP-ANALYSIS.md
```

---

## MANDATORY RULES

- Do **NOT** propose fixes inline — surface for resolution in Phase 26 (project-wide specs) or via spec revision
- Do **NOT** silently merge duplicates — flag each
- Apply output-boundary rule when assessing patterns
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All 5 analysis docs created
- [ ] Library candidates surfaced (input to Phase 23 if new libraries identified post-spec)
- [ ] Error code conflicts surfaced (input to Phase 26)
- [ ] PASS-PROGRESS.md updated

**Next:** Phase 26 (Project-Wide Specs)

---

## TEMPLATES (enriched for NCE-V2)

- [PATTERN-ANALYSIS-TEMPLATE.md](./PATTERN-ANALYSIS-TEMPLATE.md)
- [TYPE-ANALYSIS-TEMPLATE.md](./TYPE-ANALYSIS-TEMPLATE.md)
- [ERROR-CODE-ANALYSIS-TEMPLATE.md](./ERROR-CODE-ANALYSIS-TEMPLATE.md)
- [DEPENDENCY-ANALYSIS-TEMPLATE.md](./DEPENDENCY-ANALYSIS-TEMPLATE.md)
- [GAP-ANALYSIS-TEMPLATE.md](./GAP-ANALYSIS-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
