# Prerelease Checklist

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
RC Commit SHA: {{COMMIT_SHA}}
---

## Release Summary

| Field | Value |
|-------|-------|
| Release Candidate | {{RC_TAG}} |
| Commit SHA | {{COMMIT_SHA}} |
| Target Release Date | {{DATE}} |
| Release Owner | {{NAME}} |

---

## Lock Status Tracker (v4.2)

| Phase | Item | Locked At | Last Modified | Modified By | Status |
|-------|------|-----------|---------------|-------------|--------|
| 61 | RC commit | | | | ☐ Valid |
| 61 | Branch protection | | | | ☐ Valid |
| 62 | CI/CD pipeline | | | | ☐ Valid |
| 62 | Deployment config | | | | ☐ Valid |
| 63 | Performance baselines | | | | ☐ Valid |
| 63 | Security requirements | | | | ☐ Valid |
| 63 | Alerting thresholds | | | | ☐ Valid |
| 63 | Cost thresholds | | | | ☐ Valid |

**Validation Rule:** If "Last Modified" > "Locked At", status = ❌ INVALID

---

## RC History

| RC Tag | Created | Status | Failure Reason |
|--------|---------|--------|----------------|
| | | ☐ Passed / ☐ Failed | |

---

## Phase 61: CI/CD Pipeline

| Check | Status | Evidence |
|-------|--------|----------|
| Branch protection enabled | ☐ | |
| All CI/CD gates configured | ☐ | |
| CI reproducible locally | ☐ | |
| RC tag created | ☐ | |
| Notifications configured | ☐ | |

**Phase 61 Complete:** ☐ Date: ___

---

## Phase 62: Environment Configuration

| Check | Status | Evidence |
|-------|--------|----------|
| Staging runs RC commit | ☐ | |
| Staging is IaC-reproducible | ☐ | |
| Staging data fresh (< 24h) | ☐ | |
| Secrets rotation tested | ☐ | |
| Migrations backward-compatible | ☐ | |
| Rollback tested (< 10 min) | ☐ | Time: ___ |
| Feature flags audited | ☐ | |

**Phase 62 Complete:** ☐ Date: ___

---

## Phase 63: Performance & Security

### Performance

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| LCP | < 2.5s | | ☐ |
| FID | < 100ms | | ☐ |
| CLS | < 0.1 | | ☐ |
| API p95 | < 500ms | | ☐ |
| Bundle size | < 300kb | | ☐ |

### Security

| Check | Result | Status |
|-------|--------|--------|
| Dependency vulnerabilities | | ☐ |
| SSL rating | | ☐ |
| No secrets in repo | | ☐ |
| License compliance | | ☐ |

### Audits

| Check | Status | Evidence |
|-------|--------|----------|
| Zombie dependency audit | ☐ | |
| API breaking change guard | ☐ | |
| Cost thresholds defined | ☐ | |
| Alerting thresholds defined | ☐ | |

**Phase 63 Complete:** ☐ Date: ___

---

## Phase 64: Documentation & Final Review

### Documentation

| Document | Status | Location |
|----------|--------|----------|
| DEPLOYMENT-GUIDE.md | ☐ | |
| RUNBOOK.md | ☐ | |
| API-DOCS | ☐ | |
| CHANGELOG.md | ☐ | |

### Final Checks

| Check | Status |
|-------|--------|
| No TODO/FIXME in production paths | ☐ |
| Logs captured by aggregator | ☐ |
| Error tracking configured | ☐ |
| Runbook linked from alerts | ☐ |
| Full flow works on staging | ☐ |

**Phase 64 Complete:** ☐ Date: ___

---

## Sign-Off Collection

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Tech Lead | | | ☐ |
| Security Owner | | | ☐ |
| Ops / SRE | | | ☐ |
| Product Owner | | | ☐ (optional) |
| **Release Owner** | | | ☐ **GO/NO-GO** |

### Fallback Authority (if used)

| Field | Value |
|-------|-------|
| Release Owner (unavailable) | |
| Fallback Authority Used | |
| Reason | |

---

## Final Decision

| Decision | Selected |
|----------|----------|
| **GO** - Ready for production | ☐ |
| **NO-GO** - Issues must be resolved | ☐ |

**If NO-GO:**

| Blocker | Required Action |
|---------|-----------------|
| | |

---

## Templates Used (Enterprise)

| Template | Version |
|----------|---------|
| PRERELEASE-CHECKLIST-TEMPLATE.md | v1.0 |

---
Generated: {{timestamp}}
Phase: 64 - Documentation & Final Review
---
