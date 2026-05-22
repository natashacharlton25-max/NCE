# PHASE 50: COMPONENT IMPLEMENTATION

---
Phase: 50
Name: Component Implementation (Astro)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/50. Component-Implementation/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Astro-side implementation of components per Phase 40 specs.

---

## ROLE

Build every Astro component per Phase 40 COMPONENT-SPEC.md files.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/components/*/COMPONENT-SPEC.md` (Phase 40)
2. `NCE-V2/specs/frontend-contract/DESIGN-SYSTEM.md` (Phase 38)
3. Phase 49 design system implementation
4. `NCE-V2/specs/CODING-STANDARDS.md` (Phase 35; TS rules apply)

---

## TASK

For each Astro component:

1. Create `src/components/<ComponentName>.astro`
2. TypeScript prop interface in `src/components/<ComponentName>.types.ts`
3. Implement HTML structure per COMPONENT-SPEC.md
4. Apply design tokens from Phase 49
5. Handle all states (loading, error, empty)
6. WCAG accessibility (ARIA, keyboard, focus management)
7. Responsive behaviour per breakpoints
8. Component tests (Astro testing via @astro/test or similar)

---

## MANDATORY RULES

- Presentation-only — no direct NCE-V2 API calls (data comes via props)
- TypeScript props
- WCAG conformance
- Apply design tokens (not hardcoded styles)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All components implemented
- [ ] All component tests pass
- [ ] WCAG checks pass
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 51 (Page Implementation)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
