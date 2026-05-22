# PHASE 52: INTEGRATION

---
Phase: 52
Name: Integration (Astro ↔ NCE-V2 wiring)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/52. Integration/
Project: NCE-V2 (Astro consumer ↔ NCE-V2 backend)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Wire the Astro consumer to the verified NCE-V2 backend (Phase 47).

---

## ROLE

Connect Astro client → NCE-V2 API completely: data fetching, mutations, state management, error handling.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)
2. `NCE-V2/specs/frontend-contract/STATE-MANAGEMENT.md` (Phase 41)
3. NCE-V2 backend (Phase 47 verified) running on staging
4. Astro frontend (Phases 48–51)

---

## TASK

### API Client Completion (from Phase 48)

- Implement all endpoints listed in FRONTEND-BACKEND-CONTRACT.md
- Auth header injection per spec
- Response envelope handling (`{ ok: true, data }` vs `{ ok: false, error }`)
- Error code → user-facing message mapping (per ERROR-CODES.md)
- Retry policy for transient failures

### Server State Wiring

- Per fetched endpoint, implement cache strategy from STATE-MANAGEMENT.md:
  - Build-time SSG fetch
  - Per-request SSR fetch
  - Stale-while-revalidate
- Pass data into components via props

### Mutation Wiring

- Form submissions → POST/PUT/DELETE endpoints
- Optimistic UI where applicable
- Error display via component error states

### Client State

- Implement client-state stores (Nano Stores or equivalent) per Phase 41
- Form state, modal state, navigation state

### Webhook Receivers (if applicable)

- If Astro receives push events from NCE-V2 `publishing/`, implement receiver endpoints

---

## MANDATORY RULES

- All NCE-V2 calls go through the central API client (no scattered fetch())
- TypeScript types for every request + response
- All errors handled (no unhandled promise rejections)
- Apply STATE-MANAGEMENT.md patterns
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All endpoints wired
- [ ] All mutations wired
- [ ] All states (loading/error/empty) display correctly
- [ ] End-to-end smoke test from Astro → NCE-V2 staging works
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 53 (Implementation Verification)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
