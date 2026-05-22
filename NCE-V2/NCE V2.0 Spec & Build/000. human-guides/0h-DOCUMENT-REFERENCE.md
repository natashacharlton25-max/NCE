# Section 0h: Complete Document Reference

---
**Section:** 0h Prerelease
**Phases:** 61-64
**Location:** `0. Admin/0h. Prerelease/` + Phase folders
**Audit:** ⭐ MANDATORY after Phase 64
---

## Admin Folder Documents

Location: `0. Admin/0h. Prerelease/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-prerelease.md** | Instructions for Claude. Defines RC rules, no-changes policy, hardening approach. | Claude reads this |
| **Master-Prerelease-Guide 61to64.md** | Detailed guide for prerelease phases. Explains RC lifecycle, gates, lockdowns. | Reference for both |
| **CI-PIPELINE-TEMPLATE.md** | Template for CI/CD pipeline configuration. | Claude uses this |
| **DEPLOYMENT-GUIDE-TEMPLATE.md** | Template for deployment documentation. | Claude uses this |
| **RUNBOOK-TEMPLATE.md** | Template for operational runbook. | Claude uses this |
| **PRERELEASE-CHECKLIST-TEMPLATE.md** | Template for final verification. | Claude uses this |
| **0h-to-0i-HANDOFF.md** | Handoff contract to rollout section. | Created at end |

---

## Phase-by-Phase Documents

### Phase 61: CI/CD Pipeline

**Folder:** `61. CI-CD-Pipeline/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-61-PROMPT.md** | Instructions for Claude. Tells Claude to set up automated build, test, and deploy pipeline. | Give to Claude |
| **CI-PIPELINE-TEMPLATE.md** | Template for pipeline configuration. | Claude creates CI-PIPELINE.md |

**Outputs:**
- `CI-PIPELINE.md` — Pipeline configuration
- Working CI/CD pipeline
- Release Candidate tag created (v1.0.0-rc.1)

**🔒 After Phase 61:** RC commit is LOCKED. Cannot change.

---

### Phase 62: Environment Configuration

**Folder:** `62. Environment-Configuration/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-62-PROMPT.md** | Instructions for Claude. Tells Claude to configure staging and production environments. | Give to Claude |
| **ENVIRONMENT-CONFIG-TEMPLATE.md** | Template for environment settings. | Claude creates ENVIRONMENT-CONFIG.md |

**Outputs:**
- `ENVIRONMENT-CONFIG.md` — Environment configurations
- Staging environment set up
- Secrets managed
- Database migrations ready

---

### Phase 63: Performance & Security

**Folder:** `63. Performance-Security/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-63-PROMPT.md** | Instructions for Claude. Tells Claude to run performance benchmarks and security audits. | Give to Claude |
| **PERFORMANCE-BASELINE-TEMPLATE.md** | Template for performance metrics. | Claude creates PERFORMANCE-BASELINE.md |
| **SECURITY-AUDIT-TEMPLATE.md** | Template for security audit results. | Claude creates SECURITY-AUDIT.md |
| **LOAD-TEST-RESULTS-TEMPLATE.md** | Template for load testing. | Claude creates LOAD-TEST-RESULTS.md |
| **ALERTING-THRESHOLDS-TEMPLATE.md** | Template for monitoring alerts. | Claude creates ALERTING-THRESHOLDS.md |

**Outputs:**
- `PERFORMANCE-BASELINE.md` — Speed metrics (p50, p95, p99)
- `SECURITY-AUDIT.md` — Vulnerability scan results
- `LOAD-TEST-RESULTS.md` — Capacity benchmarks
- `ALERTING-THRESHOLDS.md` — When to alert

**🔒 After Phase 63:** Performance baselines and security requirements are LOCKED.

---

### Phase 64: Documentation & Final Review

**Folder:** `64. Documentation-Final-Review/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-64-PROMPT.md** | Instructions for Claude. Tells Claude to complete all documentation and get sign-offs. | Give to Claude |
| **DEPLOYMENT-GUIDE-TEMPLATE.md** | Template for how to deploy. | Claude creates DEPLOYMENT-GUIDE.md |
| **RUNBOOK-TEMPLATE.md** | Template for operations procedures. | Claude creates RUNBOOK.md |
| **CHANGELOG-TEMPLATE.md** | Template for release notes. | Claude creates CHANGELOG.md |
| **LICENSE-REPORT-TEMPLATE.md** | Template for dependency licenses. | Claude creates LICENSE-REPORT.md |
| **PRERELEASE-CHECKLIST-TEMPLATE.md** | Final checklist. | Claude creates PRERELEASE-CHECKLIST.md |

**Outputs:**
- `DEPLOYMENT-GUIDE.md` — How to deploy and rollback
- `RUNBOOK.md` — What to do when things go wrong
- `CHANGELOG.md` — What's in this release
- `LICENSE-REPORT.md` — Dependency licenses
- `PRERELEASE-CHECKLIST.md` — Final verification
- Sign-offs obtained

**⚠️ Phase 64 Rule:** NO changes allowed. If issues found, go back to earlier phases.

---

## Order to Run Section 0h

```
Step 1:  Read Claude-prerelease.md (understand section)
Step 2:  Phase 61 - Set up CI/CD, create Release Candidate
         🔒 RC commit is now LOCKED
Step 3:  Phase 62 - Configure environments
Step 4:  Phase 63 - Run performance and security checks
         🔒 Baselines are now LOCKED
Step 5:  Phase 64 - Complete documentation, get sign-offs
         ⚠️ NO CHANGES allowed in Phase 64
Step 6:  ⭐ MANDATORY: Run 0h audit before proceeding!
Step 7:  Address audit findings (may require new RC)
Step 8:  Proceed to Section 0i
```

---

## Release Candidate (RC) Rules

| Rule | What It Means |
|------|---------------|
| **RC tags are immutable** | Once tagged, never change it |
| **Only RC deploys** | Production only gets signed-off RC |
| **Failed RC → new RC** | If rc.1 fails, create rc.2 |
| **RC links to CHANGELOG** | Each RC documents what's included |

### RC Naming

```
v1.0.0-rc.1  — First release candidate for v1.0.0
v1.0.0-rc.2  — Second RC (after rc.1 failed)
```

---

## What Gets Locked

| Item | Locked After | Cannot Change In 0i |
|------|--------------|---------------------|
| RC commit | Phase 61 | Must deploy this exact commit |
| CI/CD pipeline | Phase 61 | No restructuring |
| Environment config | Phase 62 | No changes |
| Performance baselines | Phase 63 | Cannot lower targets |
| Security requirements | Phase 63 | Cannot relax |
| Alerting thresholds | Phase 63 | Cannot raise |

---

## If Something Fails

### Performance fails targets
```
Document issue → Return to 0f → Fix → Re-test → New RC → Restart 0h
```

### Security audit finds critical/high
```
Document issue → Return to 0f → Fix → Re-test → New RC → Restart 0h
```

### Issue found in Phase 64
```
NO changes in Phase 64 → Return to Phase 61/62/63 or 0f/0g → New RC if needed
```

---

## What You Should Have When Done

| Document | Location |
|----------|----------|
| CI-PIPELINE.md | Phase 61 |
| RC tag (e.g., v1.0.0-rc.1) | Git |
| ENVIRONMENT-CONFIG.md | Phase 62 |
| PERFORMANCE-BASELINE.md | Phase 63 |
| SECURITY-AUDIT.md | Phase 63 |
| LOAD-TEST-RESULTS.md | Phase 63 |
| ALERTING-THRESHOLDS.md | Phase 63 |
| DEPLOYMENT-GUIDE.md | Phase 64 |
| RUNBOOK.md | Phase 64 |
| CHANGELOG.md | Phase 64 |
| LICENSE-REPORT.md | Phase 64 |
| PRERELEASE-CHECKLIST.md | Phase 64 |
| Sign-offs | Phase 64 |

---

*Give PHASE-XX-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
