# Master Prerelease Guide — Phases 61-64

---
Status: TEMPLATE
Version: v4.2.0
Last Updated: {{timestamp}}
Purpose: Complete guide to 0h Prerelease section
---

## Overview

### What Is 0h?

**0h Prerelease** prepares the application for production deployment. It bridges the gap between "working tested code" and "ready to ship."

### Core Distinction

| Section | Question Answered |
|---------|-------------------|
| 0f Implementation | "Did we build it?" |
| 0g Testing | "Does it work?" |
| **0h Prerelease** | **"Is it ready for production?"** |

### Key Principle

**Harden for production without changing functionality.**

0h is about production readiness, not new development. No features, no bug fixes, no test changes.

---

## 0h Philosophy

**0h is not about adding functionality.**

0h is about proving the release is:

- **Safe**: No regressions, no vulnerabilities
- **Stable**: Performance within targets
- **Observable**: Logs, metrics, traces ready
- **Reversible**: Rollback tested and fast
- **Compliant**: Licenses, costs, security, API contracts

**Core principle**: 0h ensures the release is production-ready, not feature-ready.

**Anti-pattern**: Adding new features during 0h phases. Features belong in 0a–0f.

**0h workflow**:
1. **Freeze** (Phase 61): Lock the code
2. **Validate** (Phases 62–63): Prove production readiness
3. **Approve** (Phase 64): Sign-off from stakeholders
4. **Handoff → 0i**: Controlled deployment with rollback readiness

---

## Glossary

| Term | Definition |
|------|------------|
| **RC (Release Candidate)** | The exact tagged commit that passes Phase 64 and deploys to production |
| **RC Immutability** | RC tags must never be moved or retagged; failures require creating a new RC (rc.N+1) |
| **IaC (Infrastructure as Code)** | Infrastructure defined in version-controlled files (Terraform, Pulumi, etc.) |
| **Expand/Contract** | Migration pattern: add new structure, migrate data, remove old structure |
| **Core Web Vitals** | Google's metrics: LCP, FID, CLS |
| **p50/p95/p99** | Percentile response times (median, 95th percentile, 99th percentile) |
| **Backward Compatibility** | New code can run against old schema; old code can run against new schema |
| **Feature Flag** | Runtime toggle to enable/disable features without deployment |
| **Canary** | Deployment pattern: route small % of traffic to new version first |
| **Dark Launch** | Deploy feature disabled, enable gradually via feature flags |
| **Burn Rate** | Daily/hourly cloud infrastructure cost |

---

## Release Candidate (RC) Concept

### Definition

The **Release Candidate (RC)** is the exact commit (or tag) that passes Phase 64. All CI/CD, staging, performance, and security checks apply only to this commit.

### RC Naming Convention

```
vX.Y.Z-rc.N

Where:
- X.Y.Z = Semantic version
- N = RC iteration (1, 2, 3...)
```

**Examples:**
- `v1.0.0-rc.1` — First release candidate for v1.0.0
- `v1.0.0-rc.2` — Second RC (after rc.1 failed)
- `v2.1.0-rc.1` — First RC for v2.1.0

### RC Rules

| Rule | Rationale |
|------|-----------|
| RC tags are immutable | Never move a tag to a different commit |
| Failed RC → new RC | If rc.1 fails, create rc.2 on fixed commit |
| RC references CHANGELOG | Each RC tag links to CHANGELOG entry |
| Only RC deploys | 0i deploys the signed-off RC, nothing else |

### RC Lifecycle

```
Phase 61: RC created (vX.Y.Z-rc.1)
    ↓
Phase 62-63: RC verified
    ↓
    ├── If fails → Return to 0f → New RC (rc.2)
    ↓
Phase 64: RC signed off
    ↓
0i: RC deployed to production
```

**Audit Trail:** Old RC tags remain, documenting rejection history.

---

## What 0h Does NOT Do

| Item | Why | Owner |
|------|-----|-------|
| Change functionality | Feature freeze | Return to 0f |
| Change tests | Test structure locked | Return to 0g |
| Add features | Scope locked | Future release |
| Fix non-critical bugs | Defer to post-launch | Backlog |
| Modify application logic | RC is frozen | Return to 0f |

**CI/CD Configuration Rule:** CI/CD configuration changes must not modify application logic, tests, or specs.

---

## What 0h Locks

After 0h, these are frozen:

| Item | Cannot Change In 0i |
|------|---------------------|
| RC commit | Deploy this exact commit |
| CI/CD pipeline | No restructuring |
| Deployment configuration | No env changes |
| Performance baselines | No lowering targets |
| Security requirements | No relaxing standards |
| Alerting thresholds | No raising thresholds |
| Cost thresholds | No raising limits |

**Explicit Rule:**

> **Any change to a locked item requires a new RC and a full restart of 0h from Phase 61.**

This is non-negotiable. There are no "small exceptions."

---

## Sign-off Authority

Phase 64 sign-off requires explicit approval from these roles:

| Role | Required? | Signs Off On |
|------|-----------|--------------|
| Tech Lead | **Yes** | Code quality, architecture compliance |
| Security Owner | **Yes** (if findings exist) | Security audit acceptance |
| Ops / SRE | **Yes** | Operational readiness, runbook |
| Product Owner | Optional (org-dependent) | Feature completeness |
| Release Owner | **Yes** | Final GO/NO-GO authority |

**Release Owner** is the single person with final GO/NO-GO authority. This prevents consensus deadlock.

**Audit Trail:** Sign-offs must be recorded in PRERELEASE-CHECKLIST.md with name and timestamp.

### Release Owner Fallback Rule

**Primary authority**: Release Owner (designated in `PRERELEASE-CHECKLIST.md`)

**Fallback chain** (if Release Owner unavailable):
1. Engineering Manager (EM)
2. Head of Engineering (if EM unavailable)
3. CTO (if neither available)

**Documentation requirement**:
If fallback authority used, document in `PRERELEASE-CHECKLIST.md`:
```
## Sign-off Authority
- Release Owner: [Name] (unavailable - reason)
- Fallback Authority Used: [Role] [Name]
- Authorization Date: YYYY-MM-DD
- Reason: [Brief explanation]
```

---

## Phase Summary

| Phase | Name | Focus | Collapsible |
|-------|------|-------|-------------|
| 61 | CI/CD Pipeline | Automation, RC creation | **NEVER** |
| 62 | Environment Configuration | Staging, secrets, migrations, feature flags | Yes |
| 63 | Performance & Security | Benchmarks, audits, cost thresholds | Yes |
| 64 | Documentation & Final Review | Docs, checklists, sign-off | **NEVER** |

### Collapsing Rules

```
ALLOWED:
├── Phases 62-63 → "Environment & Hardening"
└── Adjacent phases within this block

NEVER COLLAPSE:
├── Phase 61 (CI/CD Pipeline) — Automation foundation, RC creation
└── Phase 64 (Documentation & Final Review) — Release gate, RC sign-off
```

---

## What 0h Receives (Inputs)

### From 0g Testing

| Artifact | Contains | Purpose in 0h |
|----------|----------|---------------|
| All tests | Unit, integration, component, E2E, a11y | CI pipeline integration |
| TEST-PLAN.md | Testing strategy | CI configuration reference |
| TEST-RESULTS.md | Final results | Baseline verification |
| COVERAGE-REPORT.md | Coverage metrics | CI gate thresholds |
| `npm test` | CI-ready command | Pipeline integration |

### From Earlier Sections

| Artifact | From | Purpose in 0h |
|----------|------|---------------|
| TECH-STACK.md | 0d | Deployment platform constraints |
| REPO-STRUCTURE.md | 0c | Build configuration |
| API-SURFACE.md | 0c/0d | API documentation source |
| ERROR-CODES.md | 0c | Error reference documentation |
| Migration scripts | 0f | Verify in staging |

---

## What 0h Produces (Outputs)

### Infrastructure Artifacts

| Artifact | Contains |
|----------|----------|
| CI/CD pipeline | Automated test/build/deploy workflow |
| Deployment configuration | Environment configs, secrets management |
| Staging environment | Production-like test environment |
| Build optimization | Production build settings |
| Branch protection | Locked main branch |

### Quality Artifacts

| Artifact | Contains |
|----------|----------|
| PERFORMANCE-BASELINE.md | Core Web Vitals, API response times, resource utilization |
| SECURITY-AUDIT.md | Vulnerability scan results |
| LICENSE-REPORT.md | Dependency license compliance (Pass/Fail) |
| LOAD-TEST-RESULTS.md | Capacity benchmarks |
| ALERTING-THRESHOLDS.md | Monitoring rules for 0i (standalone) |
| COST-THRESHOLDS.md | Cloud spend limits and billing alerts |

### Documentation Artifacts

| Artifact | Contains |
|----------|----------|
| DEPLOYMENT-GUIDE.md | How to deploy, rollback procedures |
| RUNBOOK.md | Operational procedures, incident response |
| API-DOCS/ | Generated API documentation |
| CHANGELOG.md | What's in this release |
| PRERELEASE-CHECKLIST.md | Final verification with sign-offs |
| 0h-to-0i-HANDOFF.md | Handoff to rollout |

### Rework Artifacts (conditional)

| Artifact | When Created | Contains |
|----------|--------------|----------|
| PERFORMANCE-IMPACT-ANALYSIS.md | Phase 63 fails, returns to 0f | Bottleneck analysis proving fix doesn't shift problem |

---

## Artifact Storage Policy

All 0h artifacts must follow this storage policy:

| Artifact Type | Location | Rationale |
|---------------|----------|-----------|
| Documentation (*.md) | Repository: `/docs/0h/` | Version controlled, reviewable |
| Generated docs (API-DOCS) | CI artifact or docs site | Must be reproducible via `npm run docs` |
| Test/Audit reports | Repository: `/docs/0h/reports/` | Audit trail |
| CI/CD config | Repository root (`.github/`, etc.) | Standard location |
| Secrets | Never in repo | Secrets manager only |

**Rule:** If an artifact isn't in the repo or reproducible from CI, it doesn't exist.

---

## Phase Details

### Phase 61: CI/CD Pipeline

**Purpose:** Set up automated testing, building, and deployment. Create the Release Candidate.

**Key Steps:**
1. **Branch Protection (FIRST)** — Lock main before any other work
2. **Choose CI/CD Platform** — GitHub Actions, GitLab CI, Vercel, etc.
3. **Configure Pipeline Stages** — Lint → Test → Build → Deploy
4. **Define CI/CD Gates** — Binary pass/fail, no judgement calls
5. **CI/CD Reproducibility** — `npm run ci` must work locally
6. **Create Release Candidate** — Tag the RC commit
7. **Configure Notifications** — Pipeline failures, deploy success, security alerts

**CI/CD Gates:**

| Gate | Trigger | Action |
|------|---------|--------|
| Lint failure | Any lint error | Block immediately |
| Test failure | Any test fails | Block deploy |
| Coverage below minimum | < 70% | Block deploy |
| Security scan critical/high | npm audit / Snyk | Block deploy |
| Performance regression | > 20% slower than baseline | Block deploy |
| Build failure | Build error | Block deploy |

**No judgement calls.** Gates are binary pass/fail.

**Success Criteria:**

| Criterion | Required |
|-----------|----------|
| Branch protection enabled | ☐ |
| CI reproducible locally (`npm run ci`) | ☐ |
| All CI/CD gates configured | ☐ |
| RC tag created and pushed | ☐ |
| Notifications and escalation policies configured | ☐ |

**Output:**
- CI/CD configuration files
- Branch protection enabled
- RC tag created
- CI-PIPELINE.md documenting the setup

**Prompt Location:** `61. CI-CD-Pipeline/PHASE-61-PROMPT.md`

---

### Phase 62: Environment Configuration

**Purpose:** Configure staging environment, manage secrets, verify migrations, and audit feature flags.

**Key Steps:**
1. **Set Up Staging Environment** — IaC-defined, production-like
2. **Staging Data Policy** — Fresh data within 24h of Phase 63
3. **Configure Secrets Management** — Vault/Secrets Manager
4. **Secrets Rotation Verification** — Test rotation on staging
5. **Migration Backward Compatibility** — Expand/Contract pattern
6. **Rollback Verification (Dry Run)** — Force failure, verify recovery
7. **Feature Flag Audit** — All new features flagged, defaults OFF

**IaC Requirement:** Staging must be defined as Infrastructure-as-Code, not hand-crafted.

**No Manual Staging Edits Rule:**
> Any manual change to staging invalidates the environment and must be reverted or codified in IaC before proceeding.

**Rollback Time Targets:**

| Service Type | Target | Hard Limit |
|--------------|--------|------------|
| Stateless services | < 5 minutes | 10 minutes |
| Stateful services | < 15 minutes | 30 minutes |
| Database rollback | < 30 minutes | 60 minutes |

**Success Criteria:**

| Criterion | Required |
|-----------|----------|
| Staging environment runs RC commit | ☐ |
| Staging is IaC-reproducible | ☐ |
| Staging data is fresh (within 24h) | ☐ |
| Secrets rotation tested | ☐ |
| Migrations backward-compatible | ☐ |
| Rollback tested within time target | ☐ |
| Feature flags audited | ☐ |
| No manual staging edits | ☐ |

**Output:**
- Staging environment running (IaC-defined)
- ENVIRONMENT-CONFIG.md
- SECRETS-ROTATION.md
- Migration backward compatibility verified
- Rollback tested
- Feature flag audit complete

**Prompt Location:** `62. Environment-Configuration/PHASE-62-PROMPT.md`

---

### Phase 63: Performance & Security

**Purpose:** Verify the application meets performance, security, and cost requirements.

**Key Areas:**

**Performance Testing:**
- Frontend: Core Web Vitals (LCP < 2.5s, FID < 100ms, CLS < 0.1)
- API: Response times (p50 < 200ms, p95 < 500ms, p99 < 1000ms)
- Resources: CPU < 50% normal / 80% peak, Memory < 70% normal / 85% peak
- Bundle size: Initial JS < 200kb gzipped

**Security Audit:**
- Dependency vulnerabilities (npm audit / Snyk)
- OWASP Top 10 (ZAP / manual)
- SSL/TLS configuration (A rating minimum)
- Headers security (A rating minimum)
- Secrets exposure (GitLeaks / TruffleHog)
- License compliance (no GPL in production)

**Security Severity Decision Table:**

| Severity | Action | Timeline |
|----------|--------|----------|
| Critical | Block release, return to 0f | Immediate |
| High | Block release, return to 0f | Immediate |
| Medium | Document, approve explicitly | Before release |
| Low | Document only | Post-release OK |

**Cost Thresholds:**
- Daily burn rate alerts
- Budget percentage warnings (50%, 80%, 100%)
- Cost anomaly detection

**Zombie Dependency Audit:**
```bash
npx depcheck
```
- Zero unused dependencies in `dependencies`
- Document intentional "unused" packages (polyfills)

**API Breaking Change Guard:**
- Compare API-SURFACE.md with previous release
- Breaking changes require major version bump + MIGRATION.md

**Output:**
- PERFORMANCE-BASELINE.md
- SECURITY-AUDIT.md
- LICENSE-REPORT.md
- LOAD-TEST-RESULTS.md
- ALERTING-THRESHOLDS.md
- COST-THRESHOLDS.md
- All critical/high issues resolved

**Prompt Location:** `63. Performance-Security/PHASE-63-PROMPT.md`

---

### Phase 64: Documentation & Final Review

**Purpose:** Complete documentation and perform final release verification.

**Critical Rule:** During Phase 64, no code, config, or infrastructure changes are allowed. If an issue is found, return to the appropriate phase and restart Phase 64.

**Required Documentation:**

| Document | Audience | Content |
|----------|----------|---------|
| DEPLOYMENT-GUIDE.md | DevOps | How to deploy, rollback procedures |
| RUNBOOK.md | Operations | Incident response, maintenance |
| API-DOCS/ | Developers | API reference (generated) |
| CHANGELOG.md | All | What's in this release |

**RUNBOOK.md Must Include:**
- Severity Definitions
- Who to Call (on-call rotation, escalation path)
- Communication Templates
- Common Issues and Fixes
- Rollback Procedure (step-by-step)

**Runbook Accessibility Check:**
- [ ] Runbook linked in PagerDuty/Opsgenie alert templates
- [ ] Runbook sections have deep-linkable anchors
- [ ] Critical procedures (rollback, escalation) are bookmarked
- [ ] Runbook tested by someone unfamiliar with the system

**Final Review Checklist Categories:**
- CI/CD (pipeline, gates, coverage, security, branch protection)
- Tests (all pass, run against RC)
- Performance (Core Web Vitals, API times, resources, bundle)
- Security (no critical/high, SSL, no secrets in repo, licenses)
- Cost (thresholds, alerts, anomaly detection)
- Operations (secrets rotation, rollback tested, migrations, alerting)
- Documentation (deployment guide, runbook, API docs, changelog)
- Code Quality (no TODOs, logs searchable, error tracking)
- Feature Flags (all flagged, defaults OFF)
- Staging (full flow works, uses RC exactly)

**Sign-off Collection:**

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Tech Lead | | | ☐ |
| Security Owner | | | ☐ |
| Ops / SRE | | | ☐ |
| Product Owner | | | ☐ (optional) |
| **Release Owner** | | | ☐ **GO / NO-GO** |

**Output:**
- All documentation complete
- PRERELEASE-CHECKLIST.md (all items checked, all sign-offs collected)
- 0h-to-0i-HANDOFF.md
- RC signed off for production deployment

**Prompt Location:** `64. Documentation-Final-Review/PHASE-64-PROMPT.md`

---

## Rework Processes

### Performance Issues

```
0h discovers slow endpoint
    ↓
Document in PERFORMANCE-BASELINE.md
    ↓
If critical: Return to 0f
    ↓
Developer provides PERFORMANCE-IMPACT-ANALYSIS.md (required)
    ↓
Re-run 0g affected tests
    ↓
Create new RC
    ↓
Restart 0h from Phase 61
```

### Security Issues

```
0h discovers vulnerability
    ↓
Document in SECURITY-AUDIT.md
    ↓
Classify severity (see decision table)
    ↓
Critical/High: Return to 0f, new RC, restart 0h
Medium: Document, get explicit approval
Low: Document, proceed
```

### Phase 62 Issues

```
Phase 62 fails
    ↓
Document failure in ENVIRONMENT-CONFIG.md
    ↓
Identify root cause:
    ├── Migration issue → Return to 0f
    ├── Secrets issue → Return to 0f or infra team
    ├── IaC issue → Fix in infra repo
    ├── Staging drift → Refresh staging, retry
    ├── Feature flag issue → Fix in 0f
    ↓
Create new RC if code changed
    ↓
Restart 0h from Phase 61
```

### Phase 63 Issues

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

### Phase 64 Issues

```
Issue found during Phase 64
    ↓
NO changes allowed in Phase 64
    ↓
Return to appropriate phase (61/62/63 or 0f/0g)
    ↓
Create new RC if code changed
    ↓
Restart Phase 64
```

**Key principle**: Never patch an RC. Create rc.N+1 and restart 0h.

---

## 0h-to-0i Handoff

### What 0h Delivers

| Artifact | Contains |
|----------|----------|
| Signed-off RC | Exact commit to deploy |
| Working CI/CD | Automated test/build/deploy |
| Staging environment | Production-like, IaC-defined |
| Performance baseline | Metrics and targets |
| Security sign-off | Audit results |
| License compliance | LICENSE-REPORT.md |
| Cost thresholds | COST-THRESHOLDS.md |
| Alerting thresholds | ALERTING-THRESHOLDS.md for 0i to implement |
| Documentation | Deployment, runbook, API docs |
| PRERELEASE-CHECKLIST.md | All items verified, all sign-offs |

### What 0h Guarantees

| Guarantee | Evidence |
|-----------|----------|
| RC is verified | All phases applied to same commit |
| CI/CD works | Pipeline runs successfully |
| Staging works | Full flow verified on RC |
| Performance acceptable | Baseline meets targets |
| Security acceptable | No critical/high vulnerabilities |
| Licenses acceptable | LICENSE-REPORT.md shows all Pass |
| Costs controlled | COST-THRESHOLDS.md with alerts |
| Rollback works | Tested on staging within time target |
| Secrets rotatable | Rotation procedure verified |
| Migrations safe | Backward compatibility verified |
| Feature flags ready | Audit complete, defaults OFF |
| Docs complete | All required docs exist |
| Runbook findable | Linked from alerts |
| Monitoring ready | ALERTING-THRESHOLDS.md ready for 0i |
| Authority clear | Sign-offs collected |

### What 0i Does

| Task | Purpose |
|------|---------|
| Production deployment | Deploy RC to production |
| Monitoring implementation | Connect ALERTING-THRESHOLDS.md + COST-THRESHOLDS.md |
| Rollback readiness | Have rollback ready |
| Post-launch verification | Confirm success |

---

## Enterprise-Scale Optional Features

These features are optional additions for enterprises, regulated industries, or teams requiring enhanced auditability.

### 1. Evidence Hashing (Optional)

Teams may optionally hash key artifacts and record hashes in `PRERELEASE-CHECKLIST.md` for tamper-evident audit trails.

**Artifacts to hash**:
- PERFORMANCE-BASELINE.md
- SECURITY-AUDIT.md
- LICENSE-REPORT.md
- ENVIRONMENT-CONFIG.md
- COST-THRESHOLDS.md

**Implementation**:
```bash
sha256sum PERFORMANCE-BASELINE.md >> ARTIFACT-HASHES.txt
```

### 2. Template Version Pinning

All templates include a `Template-Version:` header. `PRERELEASE-CHECKLIST.md` must record the version of each template used for this release.

**Template header format**:
```markdown
---
Template-Version: 1.0
Last-Updated: {{date}}
---
```

---

## Version Note

| Document Type | Version | Notes |
|---------------|---------|-------|
| Planning Document | v4.2 | Source specification with all requirements |
| Master Guide | v4.2 | Includes enterprise features from v4.1+ |
| Templates & Prompts | v4.2 | Includes Safety Pins and Lock Status Tracker |

Templates and prompts follow v4.2 because they include enterprise features (Safety Pins, Lock Status Tracker, Evidence Hashing) added after the v4.1 planning phase.

---

## Template Index

| Template | Version | Purpose | Used In |
|----------|---------|---------|---------|
| CI-PIPELINE-TEMPLATE.md | 1.0 | CI/CD workflow documentation | Phase 61 |
| ENVIRONMENT-CONFIG-TEMPLATE.md | 1.0 | Staging configuration and data policy | Phase 62 |
| DEPLOYMENT-GUIDE-TEMPLATE.md | 1.0 | Deployment and rollback instructions | Phase 64 |
| RUNBOOK-TEMPLATE.md | 1.0 | Operations and incident response | Phase 64 |
| PRERELEASE-CHECKLIST-TEMPLATE.md | 1.0 | Final verification with Lock Status Tracker | Phase 64 |
| LICENSE-REPORT-TEMPLATE.md | 1.0 | Dependency license compliance | Phase 63 |
| ALERTING-THRESHOLDS-TEMPLATE.md | 1.0 | Monitoring rules for 0i | Phase 63 |
| COST-THRESHOLDS-TEMPLATE.md | 1.0 | Cloud spend limits and alerts | Phase 63 |
| PERFORMANCE-IMPACT-ANALYSIS-TEMPLATE.md | 1.0 | Rework analysis for performance fixes | Phase 63 (rework) |

---

## File Inventory

### Admin Files (`0. Admin/0h. Prerelease/`)

| # | File | Purpose |
|---|------|---------|
| 1 | Master-Prerelease-Guide 61to64.md | This document |
| 2 | Claude-prerelease.md | AI instructions for executing 0h |
| 3 | 0h-to-0i-HANDOFF.md | Final contract before production deployment |
| 4 | CI-PIPELINE-TEMPLATE.md | Structure for documenting CI/CD workflow |
| 5 | DEPLOYMENT-GUIDE-TEMPLATE.md | Technical instructions for deployment |
| 6 | RUNBOOK-TEMPLATE.md | Incident response and operational procedures |
| 7 | PRERELEASE-CHECKLIST-TEMPLATE.md | Comprehensive verification checklist with sign-offs |
| 8 | LICENSE-REPORT-TEMPLATE.md | License compliance with Pass/Fail status |
| 9 | ALERTING-THRESHOLDS-TEMPLATE.md | Standalone monitoring rules for 0i |
| 10 | COST-THRESHOLDS-TEMPLATE.md | Cloud spend limits and billing alerts |
| 11 | PERFORMANCE-IMPACT-ANALYSIS-TEMPLATE.md | Rework analysis when performance fails |
| 12 | ENVIRONMENT-CONFIG-TEMPLATE.md | Staging configuration and data policy |

### Phase Files

| # | File | Location | Purpose |
|---|------|----------|---------|
| 13 | PHASE-61-PROMPT.md | `61. CI-CD-Pipeline/` | CI/CD and RC creation |
| 14 | PHASE-62-PROMPT.md | `62. Environment-Configuration/` | Staging, secrets, rollbacks, feature flags |
| 15 | PHASE-63-PROMPT.md | `63. Performance-Security/` | Performance, security, cost audits |
| 16 | PHASE-64-PROMPT.md | `64. Documentation-Final-Review/` | Documentation and Go/No-Go |

---

## Quick Reference

| Rule | Summary |
|------|---------|
| RC is sacred | Only signed-off RC deploys |
| RC tags immutable | Failed RCs stay in history |
| Gates are binary | No judgement calls |
| Staging = Production | Differ only in scale/secrets |
| No changes in Phase 64 | Return to earlier phases |
| Evidence required | Every check has a link |
| Branch protection first | Lock main before 0h work |
| Locked items = new RC | Any change restarts 0h from Phase 61 |

---
Generated: {{timestamp}}
Section: 0h Prerelease
Phases: 61-64
Version: v4.2.0
---
