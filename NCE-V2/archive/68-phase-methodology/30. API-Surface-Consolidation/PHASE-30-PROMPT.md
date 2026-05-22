# PHASE 30: API SURFACE CONSOLIDATION

---
Phase: 30
Name: API Surface Consolidation
Section: 0d. PreCode
Location: NCE-V2/NCE V2.0 Spec & Build/30. API-Surface-Consolidation/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You consolidate all API surfaces (Worker fetch handlers + cross-Worker service-binding interfaces) into a unified master API surface document.

This is **extraction and consolidation** — NOT design. Surfaces are already in per-component specs.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
2. All component `FUNCTIONS.md` (Phase 21) and `OUR-WRAPPER.md` (Phase 22)
3. All `CONTRACT.md` files (Pass 2)
4. `NCE-V2/specs/project-wide/API-STANDARDS.md` (Phase 26)

---

## TASK

Produce `API-SURFACE.md` covering:

1. **Public HTTP API** (Worker fetch handlers)
   - Per system Worker: routes + methods + request/response TS types
   - Authentication scheme (per API-STANDARDS.md from Phase 26)
   - Response envelope: `{ ok: true, data: T } | { ok: false, error: { code, message } }`

2. **Internal Service Bindings** (Worker → Worker)
   - Per binding pair: which system calls which, function signatures
   - Type contracts (input type → output type)

3. **External-Facing Webhooks**
   - From `integrations/WebhookReceiver.ts` — endpoints, payload shapes, auth verification

4. **D1 Binding Surfaces**
   - Which Workers have which D1 bindings declared in wrangler.toml
   - All access through `library/Librarian` from non-library Workers

5. **R2 / KV / DO / Queue Bindings**
   - Per binding type, per Worker

Check for conflicts:
- Two systems claiming the same route
- Mismatched types across binding boundaries
- Missing handlers for routes referenced by contracts

> **Logging rule:** Conflicts logged in `NCE-V2/admin/PRECODE-DECISION-NOTES.md`.

---

## MANDATORY RULES

- TypeScript types everywhere (no untyped JSON)
- Apply `library/Librarian` mediation rule
- Response envelope per API-STANDARDS.md
- Do **NOT** invent new endpoints
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/api/API-SURFACE.md
```

---

## END CONDITION

- [ ] `API-SURFACE.md` consolidates all surfaces
- [ ] No conflicts
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 31 (Library Structure)

---

## TEMPLATES (enriched for NCE-V2)

- [API-SURFACE-TEMPLATE.md](./API-SURFACE-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
