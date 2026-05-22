# PHASE 41: STATE MANAGEMENT

---
Phase: 41
Name: State Management (Astro consumer)
Section: 0e. Frontend
Location: NCE-V2/NCE V2.0 Spec & Build/41. State-Management/
Project: NCE-V2 (Astro consumer)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

Server state lives in NCE-V2 (D1, R2, KV). Astro's state is mostly SSG-baked or fetched at request time. This phase covers Astro client-side state only.

---

## ROLE

You document state flow in the Astro consumer.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/frontend-contract/pages/*/PAGE-SPEC.md` (Phase 39)
2. `NCE-V2/specs/frontend-contract/components/*/COMPONENT-SPEC.md` (Phase 40)
3. `NCE-V2/specs/TECH-STACK.md` (Phase 33)
4. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)

---

## TASK

Produce `STATE-MANAGEMENT.md`:

### Categories

| Category | Description | Astro Tool |
|----------|-------------|------------|
| Server state | Data from NCE-V2 | Astro fetch + KV cache via NCE-V2 |
| Client state | UI state, drafts | Nano Stores (or Astro native) |
| URL state | Route params, query | Astro Router |
| Form state | In-progress input | TS state in form components |
| Persisted | Survives refresh | localStorage (minimal) |

### Server State

Per fetched endpoint: cache strategy, stale time, revalidation trigger.

### Client State

What client state exists, where it lives, when it's reset.

### Sync to NCE-V2

Form submissions → NCE-V2 API per Phase 37 contract.

---

## OUTPUT LOCATION

```
NCE-V2/specs/frontend-contract/STATE-MANAGEMENT.md
```

---

## MANDATORY RULES

- Server state stays on NCE-V2
- Client state minimal
- No business logic client-side
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] STATE-MANAGEMENT.md complete
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 42 (Frontend Ready Check)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
