# PHASE 38: DESIGN SYSTEM

---
Phase: 38
Name: Design System (Astro consumer)
Section: 0e. Frontend
Location: NCE-V2/NCE V2.0 Spec & Build/38. Design-System/
Project: NCE-V2 (TypeScript on Cloudflare Workers; Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

NCE-V2 has no design system — visual brand intent lives in `brand/` (as JSON / D1 entries). Design tokens, components, styles are consumed and rendered by Astro. This phase produces the Astro-side design system spec.

---

## ROLE

You document the design system for the Astro consumer, derived from NCE-V2's brand data + user-provided mockups.

---

## PRE-REQUISITE: Phase 38a

Run Phase 38a (Design Request) first to produce `frontend-contract/DESIGN-REQUEST.md` and gather mockups under `/mockups/`.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/brand/spec/*`
2. `NCE-V2/specs/brand/colour/spec/*`, `brand/typography/spec/*`, `brand/visual/spec/*`
3. `NCE-V2/specs/brand/libraries/brands/*.library.md` (brand library D1 schema)
4. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)
5. `/mockups/` (Phase 38a delivery)

---

## TASK

Produce `DESIGN-SYSTEM.md`:

1. **Design tokens** from brand/colour, brand/typography, brand/visual (palettes, font stacks, spacing)
2. **Components inventory** for Astro (Layout, Header, ContentBlock, etc.)
3. **Theming** — how Astro reads brand tokens from NCE-V2 JSON
4. **Responsive breakpoints**
5. **Accessibility baseline** (WCAG level)
6. **State styles** (loading, error, empty)

---

## OUTPUT LOCATION

```
NCE-V2/specs/frontend-contract/DESIGN-SYSTEM.md
```

---

## MANDATORY RULES

- Design tokens come from NCE-V2 brand data (not hardcoded)
- Components presentation-only — no business logic
- NCE-V2 owns design *intent*; Astro owns *implementation*
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] DESIGN-SYSTEM.md complete with tokens, components, theming
- [ ] All tokens trace to NCE-V2 brand data
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 39 (Page Specs)

---

## TEMPLATES

- [DESIGN-SYSTEM-TEMPLATE.md](./DESIGN-SYSTEM-TEMPLATE.md)
- [PHASE-38a-DESIGN-REQUEST-PROMPT.md](./PHASE-38a-DESIGN-REQUEST-PROMPT.md)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
