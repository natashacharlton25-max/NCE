# PHASE 63: PERFORMANCE & SECURITY

---
Phase: 63
Section: 0h. Prerelease
Name: Performance & Security Audit
Location: NCE-V2/NCE V2.0 Spec & Build/63. Performance-Security/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Run performance tests, security audits, license compliance, cost/alerting threshold configuration. Block release on critical issues.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/ENVIRONMENT-CONFIG.md` (Phase 62)
2. `NCE-V2/specs/api/API-SURFACE.md` (Phase 30)
3. `NCE-V2/specs/project-wide/SECURITY-STANDARDS.md` (Phase 26)
4. Previous release API surface (git history; if first release, skip breaking-change detection)

---

## TASK

### Performance Testing (Worker-specific)

| Metric | Target | Tool |
|--------|--------|------|
| Worker cold start | <100ms p99 | Cloudflare analytics |
| Per-request CPU | <500ms p99 (well under 5-min limit) | wrangler tail + analytics |
| Memory per request | <50 MB p99 (well under 128 MB) | wrangler tail |
| D1 query latency | <50ms p99 per query | Per-Worker observability/MetricsCollector |
| Throughput | Sustained {{target}} req/s | Load test |

Load testing tools: `k6`, `artillery`, or Cloudflare's own tooling. Run against staging.

### Security Audit
- [ ] Dependency vulnerabilities (`pnpm audit`)
- [ ] No secrets in repo (re-verify)
- [ ] D1 queries parameterized (no SQL injection vectors)
- [ ] Auth enforcement per access/ system
- [ ] CORS policy correct per API-STANDARDS.md
- [ ] Worker secrets correctly bound per ENVIRONMENT.md
- [ ] R2 access patterns safe (no public buckets unless intended)

### Breaking Change Detection
- Compare current API-SURFACE.md to previous release
- Flag any removed endpoints, renamed fields, changed types
- Document migration path for Astro consumer

### Cost / Alerting Thresholds
- Configure per-Worker billing alerts (Cloudflare Workers usage)
- D1 read/write quotas
- R2 storage + egress thresholds
- Configure alerts to observability/MetricsCollector or Cloudflare native

### License Compliance
- `pnpm licenses list` — verify all dependency licenses acceptable
- Flag any GPL / restrictive licenses

---

## OUTPUT

```
NCE-V2/admin/PERFORMANCE-BASELINE.md
NCE-V2/admin/SECURITY-AUDIT.md
NCE-V2/admin/LICENSE-REPORT.md
NCE-V2/admin/COST-THRESHOLDS.md
NCE-V2/admin/ALERTING-THRESHOLDS.md
```

---

## MANDATORY RULES

- Block release on critical security or performance findings
- All findings logged; not silently accepted
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All 5 audit artefacts produced
- [ ] No unaddressed critical findings
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 64 (Documentation & Final Review)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
