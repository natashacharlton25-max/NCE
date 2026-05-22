# PHASE 43: ENVIRONMENT SETUP

---
Phase: 43
Name: Environment Setup (NCE-V2 backend implementation begins)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/43. Environment-Setup/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Scaffold NCE-V2's repo + provision Cloudflare resources + set secrets, per Phase 34 ENVIRONMENT.md and Phase 32 REPO-STRUCTURE.md.

First implementation phase — code begins here.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/TECH-STACK.md` (Phase 33)
2. `NCE-V2/specs/ENVIRONMENT.md` (Phase 34)
3. `NCE-V2/specs/REPO-STRUCTURE.md` (Phase 32)
4. `NCE-V2/specs/CODING-STANDARDS.md` (Phase 35)
5. `NCE-V2/admin/PRECODE-READY-CHECKLIST.md` (Phase 36 — must be GO)

---

## TASK

1. **Scaffold repo** per REPO-STRUCTURE.md:
   - `package.json`, `pnpm-workspace.yaml`, `tsconfig.base.json`
   - `src/<system>/` for all 27 systems + `src/lib/svg/`
   - `wrangler/` per-Worker configs
   - `migrations/`, `tests/`, `docs/`

2. **Install dependencies** (from TECH-STACK.md): TypeScript, wrangler, vitest, miniflare, biome, etc., pinned per spec.

3. **Provision Cloudflare resources** per ENVIRONMENT.md:
   - Per library: `wrangler d1 create <library>-{dev,staging,prod}`
   - Per R2 bucket: `wrangler r2 bucket create <bucket>-{env}`
   - KV namespaces: `wrangler kv:namespace create <ns>`
   - DO bindings in each Worker's wrangler.toml

4. **Set secrets** per ENVIRONMENT.md inventory: `wrangler secret put <NAME>` per env per Worker.

5. **Generate shared TypeScript types** from project-wide SCHEMAS.md → `src/lib/types/`.

6. **Verify local dev**:
   - `wrangler dev` boots per Worker
   - `vitest` runs against miniflare

---

## MANDATORY RULES

- No code yet — only scaffolding + deps + resources + types
- No secrets in repo (.env gitignored; use `wrangler secret`)
- Follow REPO-STRUCTURE.md exactly
- Apply CODING-STANDARDS.md from this point forward
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT

- Repo with full scaffolding
- All Cloudflare resources provisioned per env
- All secrets set per env
- `IMPLEMENTATION-LOG.md` started at `NCE-V2/admin/`

---

## END CONDITION

- [ ] Repo scaffolded per REPO-STRUCTURE.md
- [ ] All resources provisioned (verify via `wrangler d1 list`, `wrangler r2 bucket list`, etc.)
- [ ] All secrets set
- [ ] `wrangler dev` + `vitest` work locally
- [ ] IMPLEMENTATION-LOG.md created
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 44 (Database Implementation)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
