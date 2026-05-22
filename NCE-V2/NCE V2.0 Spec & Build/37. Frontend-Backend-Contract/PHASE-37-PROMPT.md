# PHASE 37: FRONTEND-BACKEND CONTRACT

---
Phase: 37
Name: Frontend-Backend Contract
Section: 0e. Frontend
Location: NCE-V2/NCE V2.0 Spec & Build/37. Frontend-Backend-Contract/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

**NCE-V2 itself has no frontend** — it is a backend (Cloudflare Workers) that emits JSON consumed by the downstream **Astro** website platform. Per the output-boundary rule, web rendering is Astro's job, not NCE-V2's.

Phases 37–42 (and 48–51) apply to either the Astro consumer's needs from NCE-V2's API (in scope of this effort), or a separate sibling project (out of scope but related). Confirm with user.

---

## ROLE

You produce the contract between NCE-V2's JSON API surface and the Astro consumer — projection only, no inventions.

---

## LOCKED CONTEXT (Required Reading)

1. [Project-Intent.md](../../Project-Intent.md)
2. `NCE-V2/specs/api/API-SURFACE.md` (Phase 30)
3. `NCE-V2/specs/project-wide/ERROR-CODES.md` (Phase 26)
4. `NCE-V2/specs/website/spec/*` — the `website/` system that emits Astro's input JSON
5. `NCE-V2/specs/project-wide/API-STANDARDS.md` (Phase 26)

---

## TASK

Produce `FRONTEND-BACKEND-CONTRACT.md` covering:

1. **Authentication** — how Astro authenticates against NCE-V2 (API key / signed request / OAuth)
2. **Endpoints Astro consumes** — list with TS request/response types (from API-SURFACE.md)
3. **Response envelope** — `{ ok: true, data: T } | { ok: false, error: { code, message } }`
4. **Page JSON shape** — Astro's input from `website/PageComposer.ts`
5. **Caching** — KV-backed page JSON caching; invalidation on publish
6. **Error handling** — Astro behaviour per error code
7. **Webhooks** — publish events from `publishing/`

**Projection-only rule:** No new endpoints, params, fields, behaviours, or codes. Gaps → log in FRONTEND-DECISION-NOTES.md.

---

## OUTPUT LOCATION

```
NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md
```

---

## MANDATORY RULES

- Projection only, no inventions
- NCE-V2 emits JSON, Astro renders
- Apply API-STANDARDS envelope
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] FRONTEND-BACKEND-CONTRACT.md complete with TS types
- [ ] All consumed endpoints documented
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 38 (Design System)

---

## TEMPLATES

- [FRONTEND-BACKEND-CONTRACT-TEMPLATE.md](./FRONTEND-BACKEND-CONTRACT-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- Is the Astro consumer in scope of NCE-V2 effort?
- May this draft be promoted to "Approved"?
