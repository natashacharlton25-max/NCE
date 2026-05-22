# 0h Audit Handoff Prompt

---
Section: 0h Prerelease (Phases 61-64)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 64, before external audit
---

## Instructions for Claude

You have completed Section 0h (Prerelease). Prepare a handoff package for external audit review.

**Critical Context:** 0h is the last gate before production. This audit is about confirming production readiness, not adding features.

---

## Create: 0h-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- Release Candidate (RC) identifier and status
- CI/CD pipeline status
- Performance and security verification results
- Overall confidence that this is production-ready

### 2. Self-Assessment Scores

Score your own work honestly (1-5 scale):

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | Are all prerelease tasks done? |
| Consistency | /5 | Do all configs align? |
| Accuracy | /5 | Are metrics and reports accurate? |
| Methodology | /5 | Was 0h process followed? |
| Quality | /5 | Is documentation complete? |
| Readiness | /5 | Ready for production deployment? |

### 3. Release Candidate Status

| Field | Value |
|-------|-------|
| RC Tag | {{e.g., v1.0.0-rc.1}} |
| RC Commit SHA | {{full SHA}} |
| RC Created | {{timestamp}} |
| RC Immutable | Yes |
| Previous RC(s) | {{list if any, e.g., rc.1 failed → rc.2}} |

**If RC changed during 0h:** Explain why a new RC was needed.

### 4. Lock Status Tracker

| Item | Locked At | Status | Notes |
|------|-----------|--------|-------|
| RC Commit | Phase 61 | 🔒 LOCKED | |
| CI/CD Pipeline | Phase 61 | 🔒 LOCKED | |
| Performance Baselines | Phase 63 | 🔒 LOCKED | |
| Security Requirements | Phase 63 | 🔒 LOCKED | |
| Alerting Thresholds | Phase 63 | 🔒 LOCKED | |
| Cost Thresholds | Phase 63 | 🔒 LOCKED | |

**Any locked item modified = New RC required**

### 5. Concerns & Uncertainties

List anything you're not 100% confident about:
- Performance concerns
- Security items that need human review
- Configuration that might need adjustment
- External dependencies that could fail

### 6. Phase Completion Summary

| Phase | Status | Key Outputs |
|-------|--------|-------------|
| 61: CI/CD Pipeline | ✅/⚠️/❌ | CI-PIPELINE.md, pipeline configs |
| 62: Release Candidate | ✅/⚠️/❌ | RC tag, CHANGELOG.md |
| 63: Production Hardening | ✅/⚠️/❌ | Performance results, security audit |
| 64: Release Approval | ✅/⚠️/❌ | Sign-offs, GO decision |

### 7. Key Metrics

**Performance:**
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| p50 Response Time | {{target}} | {{actual}} | ✅/❌ |
| p95 Response Time | {{target}} | {{actual}} | ✅/❌ |
| p99 Response Time | {{target}} | {{actual}} | ✅/❌ |
| Error Rate | {{target}} | {{actual}} | ✅/❌ |
| Throughput | {{target}} | {{actual}} | ✅/❌ |

**Security:**
| Check | Status |
|-------|--------|
| Dependency vulnerabilities | ✅ None / ⚠️ {{count}} found |
| SAST scan | ✅ Pass / ❌ Fail |
| Secret scan | ✅ Pass / ❌ Fail |
| OWASP compliance | ✅ Pass / ❌ Fail |

### 8. Sign-Off Status

| Role | Required | Status | Name | Date |
|------|----------|--------|------|------|
| Developer | Yes | ✅/⏳ | | |
| QA | Yes | ✅/⏳ | | |
| Security | Yes | ✅/⏳ | | |
| Product | {{Yes/No}} | ✅/⏳/N/A | | |
| Operations | {{Yes/No}} | ✅/⏳/N/A | | |

### 9. File Manifest

**Phase 61 Files:**
| File | Status |
|------|--------|
| CI-PIPELINE.md | |
| ENVIRONMENT-CONFIG.md | |
| Pipeline configuration files | |

**Phase 62 Files:**
| File | Status |
|------|--------|
| CHANGELOG.md | |
| Version files (package.json, etc.) | |
| RC tag in git | |

**Phase 63 Files:**
| File | Status |
|------|--------|
| PERFORMANCE-IMPACT-ANALYSIS.md | |
| Security audit results | |
| ALERTING-THRESHOLDS.md | |
| COST-THRESHOLDS.md | |
| LICENSE-REPORT.md | |

**Phase 64 Files:**
| File | Status |
|------|--------|
| PRERELEASE-CHECKLIST.md | |
| DEPLOYMENT-GUIDE.md | |
| RUNBOOK.md | |
| 0h-to-0i-HANDOFF.md | |

### 10. Dependency Bundles

| Bundle Name | Primary File | Related Files |
|-------------|--------------|---------------|
| CI/CD | CI-PIPELINE.md | Pipeline configs, ENVIRONMENT-CONFIG.md |
| Performance | PERFORMANCE-IMPACT-ANALYSIS.md | Metrics, baselines |
| Security | Security audit results | LICENSE-REPORT.md, dep scan |
| Operations | DEPLOYMENT-GUIDE.md | RUNBOOK.md, ALERTING-THRESHOLDS.md |
| Release Gate | PRERELEASE-CHECKLIST.md | All sign-offs, 0h-to-0i-HANDOFF.md |

### 11. Condensed Context

```
PROJECT: {{name}}
VERSION: {{version}}
RC: {{rc_tag}}

DEPLOYMENT TARGET:
- Environment: {{production / staging}}
- Platform: {{cloud provider, platform}}
- Method: {{containerized / serverless / etc.}}

KEY THRESHOLDS:
- Max response time (p99): {{value}}
- Max error rate: {{value}}
- Rollback target: {{time}}
```

### 12. Recommended Files for Review

1. **PRERELEASE-CHECKLIST.md** - Complete status with Lock Tracker
2. **PERFORMANCE-IMPACT-ANALYSIS.md** - Performance verification
3. **Security audit results** - Vulnerability status
4. **DEPLOYMENT-GUIDE.md** - Deployment procedure
5. **RUNBOOK.md** - Incident response
6. **0h-to-0i-HANDOFF.md** - Ready for deployment?
7. **CHANGELOG.md** - What's in this release

---

## Pre-Handoff Verification

Before creating this handoff, verify:

- [ ] RC tag is created and immutable
- [ ] CI/CD pipeline passes on RC
- [ ] Performance metrics meet targets
- [ ] Security audit complete
- [ ] All required sign-offs obtained
- [ ] PRERELEASE-CHECKLIST.md complete
- [ ] 0h-to-0i-HANDOFF.md complete
- [ ] GO decision made

If any check fails, address it first, then create the handoff.

---

## Output Location

Save as: `0h-AUDIT-HANDOFF.md` in the project's audit folder or 0h admin folder.

---

*End of Handoff Prompt*
