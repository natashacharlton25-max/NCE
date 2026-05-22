# PHASE 62: ENVIRONMENT CONFIGURATION

---
Phase: 62
Section: 0h. Prerelease
Name: Environment Configuration (staging readiness)
Location: NCE-V2/NCE V2.0 Spec & Build/62. Environment-Configuration/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Configure the staging environment, verify D1 migrations are backward-compatible, test rollback procedures, audit feature flags.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/CI-PIPELINE.md` (Phase 61; RC tag + commit SHA)
2. `NCE-V2/specs/ENVIRONMENT.md` (Phase 34)
3. D1 migrations under `migrations/<library>/`

---

## TASK

### Staging Environment Verification
- [ ] All Workers deployed to staging via `wrangler deploy --env staging`
- [ ] All D1 staging databases have latest migrations applied
- [ ] All R2 staging buckets exist
- [ ] All KV staging namespaces exist
- [ ] All DO bindings present
- [ ] All staging secrets set (verify via `wrangler secret list --env staging`)
- [ ] Staging matches production topology (same Worker grouping, same bindings)

### Migration Verification
- [ ] D1 migrations are forward-compatible (older Worker can still read newly migrated D1)
- [ ] Each library has documented rollback strategy (since D1 has no down migrations, rollback = data preservation + Worker version pin)
- [ ] Test rollback procedure on staging: deploy old Worker version, verify it still reads new D1 schema

### Secrets Audit
- [ ] All required Worker secrets are present per ENVIRONMENT.md inventory
- [ ] No staging secrets leaked in repo
- [ ] Secret rotation procedure documented

### Feature Flags
- [ ] Any feature flags in `orchestration/FeatureFlags` audited
- [ ] Default values appropriate for production launch
- [ ] Flag changes can be made without redeploy (via D1 or KV)

---

## OUTPUT

```
NCE-V2/admin/ENVIRONMENT-CONFIG.md
```

---

## MANDATORY RULES

- Staging must match production topology
- D1 migrations must be tested on staging before prod
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Staging fully configured + tested
- [ ] Migration + rollback procedures verified
- [ ] Secrets audit clean
- [ ] ENVIRONMENT-CONFIG.md complete
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 63 (Performance & Security)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
