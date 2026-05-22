# PHASE 59: ACCESSIBILITY TESTING

---
Phase: 59
Section: 0g. Testing
Name: Accessibility Testing (Astro)
Location: NCE-V2/NCE V2.0 Spec & Build/59. Accessibility-Testing/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Verifies Astro WCAG conformance per Phase 38 design system requirements. Mark N/A if Astro out of scope.

---

## ROLE

Verify Astro meets accessibility requirements through automated (axe-core) and manual testing.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/DESIGN-SYSTEM.md` (Phase 38; WCAG level declared)
2. Per-component COMPONENT-SPEC.md a11y section (Phase 40)
3. Astro implementation (Phase 50)

---

## PASS DEFINITION

Accessibility passes when:
1. No automated critical or serious violations
2. All keyboard navigation works (tab order, focus visible)
3. Screen reader flow usable for primary journeys
4. All design system a11y items verified (colour contrast, focus styles, ARIA, etc.)

---

## TASK

### Automated
- Run axe-core on every page (build CI step)
- Run pa11y on key flows
- Fail build on critical/serious violations

### Manual
- Keyboard-only navigation through primary journeys (no mouse)
- Screen reader pass (NVDA / VoiceOver) on key pages
- Colour contrast verification (per DESIGN-SYSTEM.md tokens)
- Focus management on modals/dialogs

### NCE-V2-Specific
- Verify NCE-V2-emitted JSON includes a11y metadata (alt text, ARIA labels) where applicable
- Verify `verification/AccessibilityValidator` content-level checks aligned with downstream Astro a11y

---

## MANDATORY RULES

- WCAG level per Phase 38 (AA minimum recommended)
- Automated checks in CI (Phase 61)
- Manual checks documented (not silent pass)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] axe-core passes (no critical/serious)
- [ ] Keyboard nav verified
- [ ] Screen reader flow verified
- [ ] All COMPONENT-SPEC.md a11y items checked
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 60 (Test Verification)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
