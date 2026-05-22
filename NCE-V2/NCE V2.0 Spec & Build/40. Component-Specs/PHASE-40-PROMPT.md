# PHASE 40: COMPONENT SPECS (Astro)

---
Phase: 40
Name: Component Specs (Astro components)
Section: 0e. Frontend
Location: NCE-V2/NCE V2.0 Spec & Build/40. Component-Specs/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

These are Astro presentation components — distinct from Phase 21 (NCE-V2 backend subsystems).

---

## ROLE

You produce per-component specs for each Astro component.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/DESIGN-SYSTEM.md` (Phase 38)
2. `NCE-V2/specs/frontend-contract/pages/*/PAGE-SPEC.md` (Phase 39)
3. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)

---

## TASK

For each Astro component (collect from DESIGN-SYSTEM inventory + PAGE-SPEC references):

1. Name + type (atomic / composite)
2. Props (TS interface)
3. Rendered output (HTML structure)
4. Design tokens consumed
5. Data shape (from NCE-V2 page JSON)
6. Accessibility requirements
7. Responsive behaviour
8. States (loading, error, empty)

Verify no orphans (every component used by ≥1 page).

---

## OUTPUT LOCATION

```
NCE-V2/specs/frontend-contract/components/{{component-name}}/COMPONENT-SPEC.md
```

---

## MANDATORY RULES

- Presentation-only; no business logic; no direct NCE-V2 API calls
- TS prop types
- WCAG conformance per Phase 38
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Every component has COMPONENT-SPEC.md
- [ ] No orphans
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 41 (State Management)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
