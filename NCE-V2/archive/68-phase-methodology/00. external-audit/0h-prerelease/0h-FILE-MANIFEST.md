# 0h Prerelease File Manifest

---
Section: 0h Prerelease (Phases 61-64)
Purpose: Complete list of files available for audit review
---

## How to Use This Manifest

This lists all files created during Section 0h. When requesting files for audit:

1. Request specific files by name
2. Use **dependency bundles** to get related files together
3. Maximum requests: 5 files (Round 1) + 3 files (Round 2)

---

## Dependency Bundles

| Bundle Name | Request | You Get |
|-------------|---------|---------|
| **CI/CD** | "CI/CD bundle" | CI-PIPELINE.md, pipeline configs, ENVIRONMENT-CONFIG.md |
| **Performance** | "Performance bundle" | PERFORMANCE-IMPACT-ANALYSIS.md, metrics, baselines |
| **Security** | "Security bundle" | Security audit results, LICENSE-REPORT.md, vulnerability report |
| **Operations** | "Operations bundle" | DEPLOYMENT-GUIDE.md, RUNBOOK.md, ALERTING-THRESHOLDS.md |
| **Release Gate** | "Release gate bundle" | PRERELEASE-CHECKLIST.md, sign-offs, 0h-to-0i-HANDOFF.md |

---

## Phase 61: CI/CD Pipeline

| File | Location | Purpose |
|------|----------|---------|
| CI-PIPELINE.md | /docs/0h/ | Pipeline documentation |
| ENVIRONMENT-CONFIG.md | /docs/0h/ | Environment configuration |
| .github/workflows/*.yml | /.github/workflows/ | GitHub Actions (if used) |
| .gitlab-ci.yml | / | GitLab CI (if used) |
| Jenkinsfile | / | Jenkins (if used) |
| docker-compose.yml | / | Local/staging setup |
| Dockerfile | / | Container definition |
| terraform/*.tf | /terraform/ | Infrastructure (if IaC) |

---

## Phase 62: Release Candidate

| File | Location | Purpose |
|------|----------|---------|
| CHANGELOG.md | / | Release notes |
| package.json | / | Version number (Node.js) |
| pyproject.toml | / | Version number (Python) |
| Cargo.toml | / | Version number (Rust) |
| VERSION | / | Version file (if used) |
| Git tag | N/A | RC tag (e.g., v1.0.0-rc.1) |

**RC Verification:**
```
git show-ref --tags {{rc_tag}}
git log -1 --format="%H" {{rc_tag}}
```

---

## Phase 63: Production Hardening

### Performance
| File | Location | Purpose |
|------|----------|---------|
| PERFORMANCE-IMPACT-ANALYSIS.md | /docs/0h/ | Performance test results |
| Performance metrics | /reports/ | Raw metrics data |
| Load test results | /reports/ | Load testing output |

### Security
| File | Location | Purpose |
|------|----------|---------|
| Security audit report | /docs/0h/ | Audit findings |
| Dependency scan results | /reports/ | Vulnerability scan |
| SAST results | /reports/ | Static analysis |
| LICENSE-REPORT.md | /docs/0h/ | License compliance |

### Monitoring
| File | Location | Purpose |
|------|----------|---------|
| ALERTING-THRESHOLDS.md | /docs/0h/ | Alert configuration |
| COST-THRESHOLDS.md | /docs/0h/ | Cost limits |
| Dashboard config | /monitoring/ | Grafana/Datadog config |
| Alert rules | /monitoring/ | Alerting rules |

---

## Phase 64: Release Approval

### Documentation
| File | Location | Purpose |
|------|----------|---------|
| PRERELEASE-CHECKLIST.md | /docs/0h/ | Complete checklist |
| DEPLOYMENT-GUIDE.md | /docs/0h/ | Deployment steps |
| RUNBOOK.md | /docs/0h/ | Incident response |
| API-DOCS | /docs/api/ | Generated API docs |

### Sign-offs
| File | Location | Purpose |
|------|----------|---------|
| Sign-off records | In PRERELEASE-CHECKLIST.md | Approval evidence |

### Handoff
| File | Location | Purpose |
|------|----------|---------|
| 0h-to-0i-HANDOFF.md | /docs/0h/ | Deployment handoff |

---

## Files from Previous Sections

These may be referenced but are NOT part of 0h:

| File | Section | Reference For |
|------|---------|---------------|
| 0g-to-0h-HANDOFF.md | 0g | Test completion evidence |
| TEST-TRACE.md | 0g | Test traceability |
| Coverage reports | 0g | Test coverage |
| IMPLEMENTATION-CHECKLIST.md | 0f | Implementation completion |

---

## Priority Files for Audit

Start with these for the most insight:

1. **PRERELEASE-CHECKLIST.md** - Complete status with Lock Tracker
2. **PERFORMANCE-IMPACT-ANALYSIS.md** - Do metrics meet targets?
3. **Security audit report** - Any vulnerabilities?
4. **DEPLOYMENT-GUIDE.md** - Is deployment procedure clear?
5. **RUNBOOK.md** - Can incidents be handled?
6. **0h-to-0i-HANDOFF.md** - Ready for deployment?

---

## File Count Summary

| Category | Count |
|----------|-------|
| Phase 61 (CI/CD) | ~5-10 |
| Phase 62 (RC) | ~3-5 |
| Phase 63 (Hardening) | ~6-8 |
| Phase 64 (Approval) | ~5-6 |
| **TOTAL** | **~20-30** |

Note: Count varies by project complexity and tech stack.

---

## Critical Files for RC Verification

To verify RC immutability, request:

1. **Git tag information** - Verify tag exists and hasn't moved
2. **CI/CD run logs** - Verify pipeline ran against RC commit
3. **PRERELEASE-CHECKLIST.md** - Check Lock Status Tracker

---

*End of File Manifest*
