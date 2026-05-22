# PHASE 51: PAGE IMPLEMENTATION

---
Phase: 51
Name: Page Implementation (Astro)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/51. Page-Implementation/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Astro-side implementation of every route per Phase 39 page specs.

---

## ROLE

Build every Astro page (route) by composing Phase 50 components.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/pages/*/PAGE-SPEC.md` (Phase 39)
2. Phase 50 component implementation
3. Phase 49 design system
4. `NCE-V2/specs/frontend-contract/STATE-MANAGEMENT.md` (Phase 41)
5. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)

---

## TASK

For each Astro route:

1. Create `src/pages/<route>/index.astro` (or `[param].astro` for dynamic)
2. Implement route guards (auth/role checks per PAGE-SPEC.md)
3. Fetch NCE-V2 page JSON via API client (Phase 48) per data requirements
4. Type the page JSON shape (TS interface)
5. Compose components from Phase 50
6. Apply data-fetching pattern (SSG / SSR / hybrid) per PAGE-SPEC.md
7. Apply caching strategy (NCE-V2 KV-backed page cache; Astro static)
8. Handle loading / error / empty states
9. SEO metadata via NCE-V2's `website/SEOManager` JSON

---

## MANDATORY RULES

- Pages compose components — minimal logic
- All data from NCE-V2 (no business logic in page)
- TypeScript types for page JSON
- Apply design tokens
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Every route implemented
- [ ] All page tests pass
- [ ] All routes load successfully against staging NCE-V2
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 52 (Integration)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
