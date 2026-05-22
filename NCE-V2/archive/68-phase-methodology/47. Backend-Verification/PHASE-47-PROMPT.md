# PHASE 47: BACKEND VERIFICATION

---
Phase: 47
Name: Backend Verification (mandatory checkpoint)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/47. Backend-Verification/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Verify NCE-V2 backend is complete and correct before frontend implementation begins.

**This phase is MANDATORY. Do not proceed to frontend until verification passes.**

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/api/API-SURFACE.md` (Phase 30)
2. `NCE-V2/specs/project-wide/ERROR-CODES.md` (Phase 26)
3. Per-system specs (Phase 21)
4. Per-integration specs (Phase 22)
5. Backend code from Phases 45–46
6. Phase 44 D1 migrations applied

---

## TASK

### Per-Endpoint Verification (from API-SURFACE.md)

For each endpoint:
- [ ] Endpoint exists at correct path/method
- [ ] Auth enforced per spec
- [ ] Request validation per TYPES.md
- [ ] Response envelope correct (`{ ok: true, data: T } | { ok: false, error: { code, message } }`)
- [ ] All TS response types match
- [ ] All declared error codes returned correctly
- [ ] Worker CPU+memory within bounds

### Per-Library Verification

For each D1 library:
- [ ] Schema matches `.library.md` spec
- [ ] All access via `library/Librarian` (no direct D1 from outside library/)
- [ ] Versioning works (`{items}` retains history; `{items}_latest` returns current)
- [ ] Drafts table works (where applicable)
- [ ] FTS table works (where applicable)

### Per-Integration Verification

For each external integration service:
- [ ] Direction correct (inbound / outbound / renderer)
- [ ] Auth via Worker secret binding
- [ ] resilience/ patterns active (RateLimiter, RetryPolicyEngine, etc.)
- [ ] Field mapping per FIELD-MAPPING.md
- [ ] Error mapping per ERRORS-EXTERNAL.md

### Output-Boundary Audit

- [ ] `website/` emits JSON only (no HTML rendering)
- [ ] `renderers/` produces final rendered artefacts (PDF, DOCX, Markdown, HTML for non-Astro)
- [ ] `email/` composes rendered HTML → handed to `integrations/EmailitIntegration`
- [ ] No library bypass (direct D1 from outside library/)

### Testing Tools

- `vitest` test suites pass (run all)
- `wrangler dev` for manual smoke tests
- D1 query verification via `wrangler d1 execute`

---

## OUTPUT

```
NCE-V2/admin/BACKEND-VERIFICATION-REPORT.md
```

---

## MANDATORY RULES

- Verify, don't fix — issues logged in IMPLEMENTATION-LOG.md and PASS-DECISION-NOTES.md
- Do **NOT** proceed to frontend until verdict is PASS
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All endpoints verified
- [ ] All libraries verified
- [ ] All integrations verified
- [ ] Output-boundary audit PASS
- [ ] BACKEND-VERIFICATION-REPORT.md complete with verdict
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 48 (Frontend Setup) — Astro side, if in scope

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
