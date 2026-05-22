# PHASE 34: ENVIRONMENT

---
Phase: 34
Name: Environment
Section: 0d. PreCode
Location: NCE-V2/NCE V2.0 Spec & Build/34. Environment/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You document NCE-V2's environment configuration — dev / staging / prod separation, Worker secrets, D1 environments, R2 buckets per environment.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
2. `NCE-V2/specs/TECH-STACK.md` (Phase 33)
3. `NCE-V2/specs/project-wide/SECURITY-STANDARDS.md` (Phase 26)

---

## TASK

Produce `ENVIRONMENT.md` covering:

### 1. Environment Tiers
- **local** — `wrangler dev` + miniflare; local D1 + R2 simulated
- **staging** — Cloudflare; separate D1 + R2 + KV instances; reduced rate limits
- **production** — Cloudflare; production D1 + R2 + KV; full rate limits

### 2. Per-Worker Wrangler Configuration
- Per-Worker `wrangler.toml` includes per-environment overrides (`[env.staging]`, `[env.production]`)
- D1 binding `database_id` differs per environment
- R2 bucket names per environment
- KV namespace IDs per environment

### 3. Worker Secrets
- Inventory all required secrets per integration provider (from Phase 22 OUR-WRAPPER.md / PROVIDER.md)
- Set via `wrangler secret put` per environment
- Never committed to repo
- Naming convention: `{PROVIDER}_{PURPOSE}` (e.g. `GOOGLE_OAUTH_CLIENT_SECRET`, `EMAILIT_API_KEY`)

### 4. Environment Variables (non-secret)
- Public config in `[vars]` block of wrangler.toml
- Examples: `ENVIRONMENT`, `LOG_LEVEL`, `ASTRO_PUBLIC_URL`

### 5. D1 Per-Environment Provisioning
- `wrangler d1 create <library>-dev` / `-staging` / `-prod`
- Migration application: `wrangler d1 migrations apply <library>-{env}`

### 6. R2 Per-Environment Provisioning
- `wrangler r2 bucket create assets-dev` / `-staging` / `-prod`

### 7. Local Dev Setup
- `pnpm install`
- `wrangler login`
- `wrangler dev` per Worker (or use `wrangler dev --env=local`)
- Local D1 SQLite file under `.wrangler/state/` (miniflare default)

### 8. Promotion Path
- Local → staging → prod
- Gates: tests pass, D1 migrations applied, secrets set

---

## OUTPUT LOCATION

```
NCE-V2/specs/ENVIRONMENT.md
```

---

## MANDATORY RULES

- Never commit secrets (`.env` is .gitignored)
- Apply SECURITY-STANDARDS.md (Worker secret bindings only)
- Document each environment explicitly
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] `ENVIRONMENT.md` documents all 3 tiers + secrets + bindings
- [ ] Secret inventory complete
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 35 (Coding Standards)

---

## TEMPLATES (enriched for NCE-V2)

- [ENVIRONMENT-TEMPLATE.md](./ENVIRONMENT-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
