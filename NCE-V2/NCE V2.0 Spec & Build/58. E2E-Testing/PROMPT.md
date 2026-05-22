# PHASE 58: E2E TESTING

---
Phase: 58
Section: 0g. Testing
Name: E2E Testing (Astro ↔ NCE-V2 staging)
Location: NCE-V2/NCE V2.0 Spec & Build/58. E2E-Testing/
Project: NCE-V2 (Astro consumer ↔ NCE-V2 backend)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Tests complete user flows from Astro through NCE-V2 staging. Mark N/A if Astro out of scope.

---

## ROLE

Write E2E tests (Playwright) simulating real user flows through Astro → NCE-V2 staging.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/pages/*/PAGE-SPEC.md` (Phase 39)
2. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)
3. NCE-V2 staging deployment (per Phase 34 ENVIRONMENT.md)

---

## CRITICAL REQUIREMENTS

1. **Test complete flows** across multiple pages (login → create → edit → publish, etc.)
2. **Include negative flows** — at least one per major feature (failed auth, invalid input, etc.)
3. **Verify D1 state after mutations** (not just UI) via direct D1 read or NCE-V2 read endpoint
4. **Use staging environment** — not dev (test the real deploy path)

---

## TASK

For each user journey:
- Set up test data in NCE-V2 staging (via `library/Librarian` write or seed)
- Run Playwright script: navigate, interact, assert UI + D1 state
- Cleanup test data after

User journey examples (NCE-V2-specific):
- Brand creation → theme generation → outline generation → content draft → publish
- Asset upload → R2 storage → library catalog reference → use in document
- Library entry creation → version bump → archive → ref_status cascade

---

## MANDATORY RULES

- Use Playwright (or equivalent stable framework)
- Use staging environment (not dev)
- Clean up test data after each run
- Tests are deterministic (no time-dependent flakiness)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All major user journeys covered
- [ ] All negative flows covered
- [ ] D1 state verified after mutations
- [ ] E2E suite passes against staging
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 59 (Accessibility Testing)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
