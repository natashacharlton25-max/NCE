# PHASE 39: PAGE SPECS

---
Phase: 39
Name: Page Specs (Astro routes)
Section: 0e. Frontend
Location: NCE-V2/NCE V2.0 Spec & Build/39. Page-Specs/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Astro routes consume page JSON from NCE-V2 (`website/PageComposer.ts`). This phase specs each Astro route. NCE-V2 itself has no pages.

---

## ROLE

You produce per-route specs for the Astro consumer.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/website/spec/*`
2. `NCE-V2/specs/website/PageComposer/spec/*`
3. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)
4. `NCE-V2/specs/frontend-contract/DESIGN-SYSTEM.md` (Phase 38)

---

## TASK

For each Astro route:

1. Route path + parameters
2. Auth required (yes/no)
3. Page JSON shape consumed (TS type from website/)
4. Design system components used
5. Data-fetching pattern (SSG / SSR / hybrid)
6. Caching strategy (KV-backed via WebPersonalizer / Astro static)
7. State per route (per Phase 41)

---

## OUTPUT LOCATION

```
NCE-V2/specs/frontend-contract/pages/{{route-slug}}/PAGE-SPEC.md
```

One per route.

---

## MANDATORY RULES

- Pages consume JSON; no business logic in Astro
- TS types for every page JSON shape
- Apply Phase 38 design tokens
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Every Astro route has PAGE-SPEC.md
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 40 (Component Specs)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
