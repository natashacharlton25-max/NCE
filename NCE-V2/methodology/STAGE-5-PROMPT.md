# STAGE 5: RELEASE

---
Stage: 5 of 5
Name: Release (CI/CD → audit → deploy → stabilise)
Methodology: NCE-V2 Reduced Methodology (see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Ship NCE-V2 to production with CI/CD, security + performance audits, controlled deployment, post-deploy verification, and operations handoff.

This is the last stage. After Stage 5, NCE-V2 v1.0 is live and operationally owned.

---

## PREREQUISITE

Stage 4 must be COMPLETE:
- All Workers implemented + tests pass
- IMPLEMENTATION-LOG.md complete
- No unaddressed flags

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/PROJECT-SPEC.md` (Stage 3)
2. `NCE-V2/specs/platform/WORKER.md` (Stage 3)
3. `NCE-V2/admin/IMPLEMENTATION-LOG.md` (Stage 4)
4. All Worker code in `src/`
5. All wrangler configs in `wrangler/`

---

## TASK — Six gates within Stage 5

### Gate 5a — CI/CD Pipeline

Set up GitHub Actions:

1. **Branch protection on `main`:** require PR + 1 review + CI green + linear history
2. **CI workflow** (`.github/workflows/ci.yml`):
   - typecheck (`tsc --noEmit`)
   - lint (`biome check`)
   - unit + integration tests (`pnpm vitest run`)
   - build dry-run (`wrangler deploy --dry-run` per Worker)
   - coverage report (fail below 80% line)
   - D1 migration validation
3. **CD workflow** (`.github/workflows/cd.yml`):
   - on merge to `main`: deploy to staging per Worker (`wrangler deploy --env staging`)
   - apply D1 migrations to staging
   - run E2E tests against staging
   - tag Release Candidate `vX.Y.Z-rcN`
   - hold for human approval before prod

**Output:** `NCE-V2/admin/CI-PIPELINE.md` documenting pipeline + RC commit SHA.

### Gate 5b — Performance + Security Audit

Run on RC tag:

**Performance** (Worker-specific):
- Cold start <100ms p99
- Per-request CPU <500ms p99
- Memory per request <50 MB p99
- D1 query latency <50ms p99
- Load test against staging

**Security:**
- `pnpm audit` clean (or risks accepted)
- D1 queries parameterized (no SQL injection)
- Worker secrets correctly bound
- CORS policy per API-STANDARDS
- R2 access patterns safe

**License:**
- `pnpm licenses list` — all acceptable; flag GPL or restrictive

**Cost + Alerting:**
- Worker billing alerts configured
- D1 read/write quotas configured
- R2 storage + egress thresholds
- Alerts wired to `observability/MetricsCollector`

**Outputs:**
- `NCE-V2/admin/PERFORMANCE-BASELINE.md`
- `NCE-V2/admin/SECURITY-AUDIT.md`
- `NCE-V2/admin/LICENSE-REPORT.md`
- `NCE-V2/admin/COST-THRESHOLDS.md`
- `NCE-V2/admin/ALERTING-THRESHOLDS.md`

### Gate 5c — Final Review (no code changes)

- Documentation current (README, DEPLOYMENT-GUIDE, RUNBOOK)
- Sign-offs collected (technical lead, security, performance, product)
- 0h-to-0i handoff document created
- No outstanding bug reports

**Output:** `NCE-V2/admin/FINAL-REVIEW-CHECKLIST.md` and `NCE-V2/admin/0h-to-0i-HANDOFF.md`.

### Gate 5d — Pre-Deploy Verification (0i begins)

- 15-min cross-functional readiness review (Release Owner, Deployer, On-call, SRE)
- Sign "GO to begin 0i"
- Pre-deploy snapshot of staging + production metrics
- Rollback readiness: prior Worker version available, D1 migrations backward-compatible

**Outputs:**
- `NCE-V2/admin/PRE-DEPLOY-CHECKLIST.md`
- `NCE-V2/admin/PRE-DEPLOY-SNAPSHOT.md`
- `NCE-V2/admin/ROLLBACK-READY.md`

### Gate 5e — Production Deployment (non-collapsible)

1. Apply D1 migrations to production: `wrangler d1 migrations apply <library>-prod` per library, in dependency order
2. Deploy `platform` Worker first: `wrangler deploy --env production`
3. Verify with `wrangler deployments list` + smoke endpoint
4. Deploy remaining 23 Workers (parallel where dependencies allow)
5. Verify each
6. Start Golden Hour: 60-min observation; any critical alert → roll back

**Output:** `NCE-V2/admin/DEPLOYMENT-LOG.md`

### Gate 5f — Post-Deploy + Stabilisation

- Smoke tests per Worker (200 responses, correct envelopes)
- Metrics vs baseline (error rate, CPU, memory, D1 latency, throughput, cost)
- Enable feature flags for internal users; broaden if clean
- Astro consumer verification (if in scope): E2E smoke from Astro
- Configure production alerts (PagerDuty / Slack)
- Stabilization period (24–48 hours close monitoring, no non-hotfix changes)
- Operations handoff (on-call briefed, dashboards bookmarked, runbook walkthrough)
- Release closure: mark PASS-PROGRESS.md as PRODUCTION

**Outputs:**
- `NCE-V2/admin/LAUNCH-VERIFICATION.md`
- `NCE-V2/admin/RELEASE-SUMMARY.md`
- `NCE-V2/admin/OPERATIONS-HANDOFF.md`

---

## MANDATORY RULES

- Never skip CI hooks
- Never force-push to main
- Never deploy without human GO at each gate (5d Pre-Deploy + 5e Production)
- Any code change during 5f stabilisation requires hotfix path or return to 5a
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../CLAUDE.md) §7

---

## END CONDITION

Stage 5 is COMPLETE when:
- [ ] CI/CD pipeline operational + branch protection enabled
- [ ] All 5 audit reports produced (Performance, Security, License, Cost, Alerting)
- [ ] All sign-offs collected
- [ ] 0i readiness review: GO
- [ ] Production deployment: SUCCESS
- [ ] Golden Hour: clean
- [ ] Post-deploy verification: PASS
- [ ] Stabilization period: clean
- [ ] Operations handoff complete
- [ ] `RELEASE-SUMMARY.md` shipped
- [ ] PASS-PROGRESS.md marked PRODUCTION

**Next:** Hotfix path for issues, or new release cycle. NCE-V2 v1.0 is live.

---

## SUB-TEMPLATES

- [STAGE-5-TEMPLATES/CI-PIPELINE-TEMPLATE.md](./STAGE-5-TEMPLATES/CI-PIPELINE-TEMPLATE.md)
- [STAGE-5-TEMPLATES/AUDIT-TEMPLATE.md](./STAGE-5-TEMPLATES/AUDIT-TEMPLATE.md) (used for perf, security, license, cost, alerting)
- [STAGE-5-TEMPLATES/DEPLOY-CHECKLIST-TEMPLATE.md](./STAGE-5-TEMPLATES/DEPLOY-CHECKLIST-TEMPLATE.md)
- [STAGE-5-TEMPLATES/RELEASE-SUMMARY-TEMPLATE.md](./STAGE-5-TEMPLATES/RELEASE-SUMMARY-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
