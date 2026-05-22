# PHASE 49: DESIGN SYSTEM IMPLEMENTATION

---
Phase: 49
Name: Design System Implementation (Astro)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/49. Design-System-Implementation/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Astro-side implementation of design tokens per Phase 38 DESIGN-SYSTEM.md. Tokens originate from NCE-V2's `brand/` data — Astro reads them at build (or runtime) and applies styles.

---

## ROLE

Translate Phase 38 DESIGN-SYSTEM.md into working Astro design tokens + base styles.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/DESIGN-SYSTEM.md` (Phase 38)
2. `NCE-V2/specs/TECH-STACK.md` (styling approach choice)
3. `/mockups/` (visual reference)
4. NCE-V2's brand library D1 schema (for tokens fetched at build)

---

## TASK

1. **Styling approach** (from TECH-STACK.md):
   - Likely: CSS variables + Tailwind, or plain CSS modules. Pick per spec.

2. **Fetch design tokens** from NCE-V2 at build time (preferred for SSG) or runtime:
   - Brand colours, typography, spacing, visual tokens
   - Static asset for build-time: query NCE-V2's `brand/` at build, write to a tokens.css or tokens.ts file

3. **Implement tokens**:
   - CSS variables in `src/styles/tokens.css`
   - Or Tailwind config theme
   - Or theme object in TS

4. **Base styles**: typography defaults, reset, root font sizes, container widths, breakpoints

5. **Theme switching** (if multi-brand): mechanism for switching tokens per brand

---

## MANDATORY RULES

- Tokens come from NCE-V2 brand data (not hardcoded)
- No business logic in styles
- Apply WCAG colour contrast per Phase 38
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All tokens from DESIGN-SYSTEM.md implemented
- [ ] Tokens source from NCE-V2 brand data
- [ ] Base styles applied
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 50 (Component Implementation)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
