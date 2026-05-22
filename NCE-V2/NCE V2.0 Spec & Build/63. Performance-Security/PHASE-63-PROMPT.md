# Phase 63: Performance & Security

---
Phase: 63
Section: 0h Prerelease
Name: Performance & Security
Status: TEMPLATE
---

## Role

You are a **Security & Performance Auditor** verifying the application meets all performance, security, license, and cost requirements.

---

## Task

Run performance tests, security audits, license compliance checks, and configure cost/alerting thresholds. Block release if any critical issues are found.

---

## Inputs

Read these files before starting:

| File | Location | Purpose |
|------|----------|---------|
| ENVIRONMENT-CONFIG.md | From Phase 62 | Staging configuration |
| API-SURFACE.md | From 0c/0d | API contract comparison |
| Previous release API-SURFACE.md | Git history | Breaking change detection |

---

## Process

### Step 1: Performance Testing

#### Frontend (Core Web Vitals)

| Metric | Target | Actual | Tool |
|--------|--------|--------|------|
| LCP | < 2.5s | | Lighthouse |
| FID | < 100ms | | Lighthouse |
| CLS | < 0.1 | | Lighthouse |
| TTFB | < 600ms | | WebPageTest |

#### API Response Times

| Metric | Target | Actual | Tool |
|--------|--------|--------|------|
| p50 | < 200ms | | k6/Artillery |
| p95 | < 500ms | | k6/Artillery |
| p99 | < 1000ms | | k6/Artillery |
| Error rate | < 0.1% | | k6/Artillery |

#### Resource Utilization

| Metric | Target (Normal) | Target (Peak) | Actual |
|--------|-----------------|---------------|--------|
| CPU | < 50% | < 80% | |
| Memory | < 70% | < 85% | |

#### Bundle Size Budget

| Asset | Budget | Actual |
|-------|--------|--------|
| Initial JS | < 200kb gzip | |
| Initial CSS | < 50kb gzip | |
| Total initial | < 300kb gzip | |

---

### Step 2: Zombie Dependency Audit

Run dependency check:

```bash
npx depcheck --ignore-dirs=dist,build
```

**Acceptance criteria:**
- [ ] Zero unused dependencies in `dependencies`
- [ ] DevDependencies may have unused packages (tooling)
- [ ] Document intentional "unused" packages (polyfills)

**If unused dependencies found:**
1. Remove: `npm uninstall [packages]`
2. Verify: `npm test`
3. Update: `npm install`
4. Re-run Phase 63 checks

**Whitelist (if needed):**
```json
{
  "depcheck": {
    "ignores": ["@babel/polyfill", "tslib"]
  }
}
```

---

### Step 3: Load Testing

| Scenario | Metric | Target | Actual |
|----------|--------|--------|--------|
| Normal load | Concurrent users | Expected traffic | |
| Peak load | 2x concurrent | Graceful handling | |
| Stress test | Breaking point | Document limits | |

---

### Step 4: Security Audit

| Check | Tool | Requirement | Result |
|-------|------|-------------|--------|
| Dependency vulnerabilities | npm audit / Snyk | No critical/high | |
| OWASP Top 10 | ZAP / manual | No critical findings | |
| SSL/TLS configuration | SSL Labs | A rating minimum | |
| Headers security | SecurityHeaders.com | A rating minimum | |
| Secrets exposure | GitLeaks / TruffleHog | No secrets in repo | |

**Security Severity Decision Table:**

| Severity | Action | Timeline |
|----------|--------|----------|
| Critical | Block release, return to 0f | Immediate |
| High | Block release, return to 0f | Immediate |
| Medium | Document, approve explicitly | Before release |
| Low | Document only | Post-release OK |

---

### Step 5: API Breaking Change Guard

Compare current API-SURFACE.md with previous release:

**Process:**
1. Compare API-SURFACE.md (this RC) vs API-SURFACE.md (previous release)
2. Identify removed or modified public APIs
3. Classify changes

**Classification:**
- **Safe:** New endpoints, optional parameters
- **Breaking:** Removed endpoints, required parameter changes, response schema changes

**Breaking change policy:**
- Major version bump required (v1.x → v2.0)
- Migration guide in MIGRATION.md
- Deprecation notices for 2+ releases before removal

**Checklist:**
- [ ] No breaking changes OR major version bump documented
- [ ] API-SURFACE.md includes deprecation warnings (if any)
- [ ] MIGRATION.md created (if breaking changes present)

---

### Step 6: License Compliance

**Generate LICENSE-REPORT.md:**

```bash
npx license-checker --summary
```

| Category | Licenses | Action |
|----------|----------|--------|
| Permitted | MIT, Apache-2.0, BSD, ISC | Auto-pass |
| Review Required | MPL, LGPL | Requires explicit approval |
| Restricted | GPL, AGPL, SSPL | Fails unless isolated |

**Checklist:**
- [ ] LICENSE-REPORT.md generated
- [ ] All licenses permitted or approved
- [ ] No restricted licenses in production bundle

---

### Step 7: Cost Thresholds

**Create COST-THRESHOLDS.md:**

| Metric | Threshold | Alert Channel |
|--------|-----------|---------------|
| Daily burn rate | < $X / day | Slack |
| 50% monthly budget | Warning | Slack |
| 80% monthly budget | Critical | Slack + Email |
| 100% monthly budget | Emergency | PagerDuty |

**Verification:**
- [ ] Cloud billing alerts configured
- [ ] Cost anomaly detection enabled
- [ ] Budget owner identified

---

### Step 8: Alerting Thresholds

**Create ALERTING-THRESHOLDS.md:**

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| High Error Rate | Error rate > 1% | 5 min | P1 | PagerDuty |
| Slow Response | p95 > 1s | 5 min | P2 | Slack |
| High CPU | CPU > 80% | 10 min | P2 | Slack |
| High Memory | Memory > 85% | 10 min | P2 | Slack |
| Service Down | Health check fails | 3 checks | P1 | PagerDuty |

---

### Step 9: Observability Baseline

| Check | Status |
|-------|--------|
| Logs are structured (JSON) | ☐ |
| Logs are searchable (aggregator configured) | ☐ |
| Traces enabled for critical paths | ☐ (if applicable) |
| Metrics exported to monitoring system | ☐ |
| Dashboards exist for key metrics | ☐ |

---

## Rules

### DOs
- ✅ Test against RC commit on staging
- ✅ Use objective metrics (not subjective assessments)
- ✅ Block on critical/high security findings
- ✅ Document all findings
- ✅ Configure all alerting thresholds

### DON'Ts
- ❌ Accept security exceptions for critical/high
- ❌ Skip license compliance
- ❌ Leave unused dependencies in production
- ❌ Proceed without cost thresholds defined

---

## Output Format

Create these files in `/docs/0h/reports/`:
- PERFORMANCE-BASELINE.md
- SECURITY-AUDIT.md
- LICENSE-REPORT.md
- LOAD-TEST-RESULTS.md

Create these files in `/docs/0h/`:
- ALERTING-THRESHOLDS.md
- COST-THRESHOLDS.md

---

## Success Criteria

| Criterion | Required |
|-----------|----------|
| Core Web Vitals pass | ☐ |
| API response times within targets | ☐ |
| Resource utilization within targets | ☐ |
| Bundle size within budget | ☐ |
| Zero unused production dependencies | ☐ |
| No critical/high security vulnerabilities | ☐ |
| No breaking API changes (or major version bump) | ☐ |
| All licenses compliant | ☐ |
| Cost thresholds defined | ☐ |
| Alerting thresholds defined | ☐ |
| Observability baseline met | ☐ |

---

## Failure Path

```
Phase 63 fails
    ↓
Document failure in PERFORMANCE-BASELINE.md or SECURITY-AUDIT.md
    ↓
Classify issue:
    ├── Performance regression → PERFORMANCE-IMPACT-ANALYSIS.md
    ├── Security critical/high → Return to 0f
    ├── License violation → Remove dependency, return to 0f
    ├── Cost threshold breach → Optimize infra or code
    ↓
Re-run affected 0g tests
    ↓
Create new RC (rc.N+1)
    ↓
Restart 0h from Phase 61
```

**Key principle:** Never patch an RC. Create rc.N+1 and restart 0h.

---

## On Approval

When all Phase 63 outputs are approved:

1. Update PRERELEASE-CHECKLIST.md Phase 63 section
2. Update Lock Status Tracker:
   - Lock "Performance baselines" with timestamp
   - Lock "Security requirements" with timestamp
   - Lock "Alerting thresholds" with timestamp
   - Lock "Cost thresholds" with timestamp
3. Proceed to Phase 64

---

## Templates

Use these templates from `0. Admin/0h. Prerelease/`:
- LICENSE-REPORT-TEMPLATE.md
- ALERTING-THRESHOLDS-TEMPLATE.md
- COST-THRESHOLDS-TEMPLATE.md
- PERFORMANCE-IMPACT-ANALYSIS-TEMPLATE.md (if rework needed)

---
Generated: {{timestamp}}
Phase: 63 - Performance & Security
Section: 0h Prerelease
---
