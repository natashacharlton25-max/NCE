# PHASE 48: FRONTEND SETUP

---
Phase: 48
Name: Frontend Setup (Astro consumer)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/48. Frontend-Setup/
Project: NCE-V2 (Astro consumer; not NCE-V2 backend)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## NCE-V2 SCOPE NOTE

This phase applies to the **Astro consumer**, not NCE-V2 itself. Apply only if Astro is in scope of this effort.

---

## ROLE

Initialize the Astro consumer app: framework setup, routing per Phase 39 page specs, base layout structure.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/TECH-STACK.md` (Phase 33; Astro decision)
2. `NCE-V2/specs/REPO-STRUCTURE.md` (Phase 32)
3. `NCE-V2/specs/frontend-contract/pages/*/PAGE-SPEC.md` (Phase 39)
4. `NCE-V2/specs/frontend-contract/FRONTEND-BACKEND-CONTRACT.md` (Phase 37)

---

## TASK

1. **Initialize Astro app** (sibling project or subdirectory per user direction):
   - `npm create astro@latest`
   - Configure TypeScript strict mode
   - Configure deployment target (Cloudflare Pages or static)

2. **Set up routing** per Phase 39 page specs:
   - File-based routing under `src/pages/<route>/index.astro`
   - Dynamic routes per PAGE-SPEC.md (e.g. `src/pages/blog/[slug].astro`)

3. **Base layout** `src/layouts/BaseLayout.astro`:
   - HTML shell
   - Imports design tokens (from Phase 49)
   - Provides slot for page content

4. **API client** for NCE-V2:
   - Wrapper around `fetch()` to NCE-V2 endpoints
   - Honour response envelope from FRONTEND-BACKEND-CONTRACT.md
   - Typed responses using shared TS types

5. **Environment config**:
   - `.env` for NCE-V2 endpoint base URL per environment
   - Cloudflare Pages env vars or static rebuild on env change

---

## MANDATORY RULES

- Astro consumes NCE-V2 JSON only — no business logic
- TypeScript strict
- No secrets in repo
- Apply CODING-STANDARDS.md where applicable (TypeScript style)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Astro app initialised
- [ ] Routing structure matches Phase 39 specs
- [ ] BaseLayout + API client in place
- [ ] `astro dev` serves locally and can fetch from NCE-V2 staging
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 49 (Design System Implementation)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
