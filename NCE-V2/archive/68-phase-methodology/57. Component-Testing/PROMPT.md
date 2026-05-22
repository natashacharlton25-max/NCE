# PHASE 57: COMPONENT TESTING

---
Phase: 57
Section: 0g. Testing
Name: Component Testing (Astro)
Location: NCE-V2/NCE V2.0 Spec & Build/57. Component-Testing/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Astro-side. Mark N/A if Astro consumer is out of scope.

---

## ROLE

Test all Astro UI components in isolation with mocked data providers.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/components/*/COMPONENT-SPEC.md` (Phase 40)
2. `NCE-V2/specs/frontend-contract/DESIGN-SYSTEM.md` (Phase 38)
3. Astro component implementation (Phase 50)

---

## CRITICAL RULE: MOCK ALL DATA

- Component tests must pass even if NCE-V2 backend is unreachable
- Mock the `fetch()` calls to NCE-V2
- Pass mock data via props
- Don't hit real endpoints

---

## TASK

For each Astro component (per COMPONENT-SPEC.md):

- Render with mocked props (happy path)
- Render with mocked data states: loading, error, empty
- Test prop validation (required vs optional)
- Test events / interactions
- Test accessibility (basic axe checks)
- Test responsive behaviour (breakpoint snapshots)
- Test visual states from DESIGN-SYSTEM.md

---

## MANDATORY RULES

- Mock NCE-V2 fetch — never hit real backend
- TS strict
- Apply WCAG checks
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All components have tests (happy + states + events + a11y)
- [ ] Component test suite passes
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 58 (E2E Testing)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
