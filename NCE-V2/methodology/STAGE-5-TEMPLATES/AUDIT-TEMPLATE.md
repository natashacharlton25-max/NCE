# {{Audit Type}} Audit

---
Audit Type: Performance | Security | License | Cost | Alerting
Status: DRAFT | PASS | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Methodology Stage: 5b
RC tag: `v{{X.Y.Z}}-rc{{N}}`
---

## Purpose

Verify NCE-V2 meets {{audit type}} thresholds before production deployment. Block release if critical findings.

---

## Thresholds

(Audit-type-specific table — populate per audit.)

### Performance baselines

| Metric | Target | Actual | Status |
|---|---|---|---|
| Worker cold start | <100ms p99 | | ☐ Pass / ☐ Fail |
| Per-request CPU | <500ms p99 | | ☐ Pass / ☐ Fail |
| Memory per request | <50 MB p99 | | ☐ Pass / ☐ Fail |
| D1 query latency | <50ms p99 | | ☐ Pass / ☐ Fail |
| Sustained throughput | {{target}} req/s | | ☐ Pass / ☐ Fail |

### Security checks

| Check | Status | Notes |
|---|---|---|
| `pnpm audit` clean | ☐ | |
| No secrets in repo | ☐ | |
| D1 queries parameterized | ☐ | |
| Worker secrets bound correctly | ☐ | |
| CORS policy correct | ☐ | |
| R2 access patterns safe | ☐ | |
| `access/` system enforces auth | ☐ | |

### License compliance

| Dependency | License | Acceptable? |
|---|---|---|
| | | ☐ |

### Cost thresholds

| Resource | Threshold | Current | Alert |
|---|---|---|---|
| Workers requests/day | | | ☐ Configured |
| D1 reads/writes | | | ☐ Configured |
| R2 storage/egress | | | ☐ Configured |
| KV reads/writes | | | ☐ Configured |

### Alerting

| Alert | Threshold | Channel | Configured |
|---|---|---|---|
| Worker error rate | >2% over 5min | PagerDuty | ☐ |
| Worker CPU p99 | >70% of 5min limit | Slack | ☐ |
| D1 latency p99 | >100ms | Slack | ☐ |
| Cost overrun | >120% baseline | Email | ☐ |
| R2 storage near limit | >80% plan | Email | ☐ |

---

## Critical Findings (block release)

| Finding | Severity | Action | Status |
|---|---|---|---|
| | Critical / High | | Open / Resolved / Accepted |

---

## Non-Critical Findings

| Finding | Severity | Action | Status |
|---|---|---|---|
| | Medium / Low | | Open / Resolved / Accepted |

---

## Verdict

**Status:** PASS / FAIL / PASS WITH ACCEPTED RISKS

If FAIL → return to relevant implementation stage.
If PASS WITH ACCEPTED RISKS → human signs off on accepted risks.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Auditor | | |
| Tech lead (accept risks) | | |

---
