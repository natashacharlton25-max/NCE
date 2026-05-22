# Phase 61: CI/CD Pipeline

---
Phase: 61
Section: 0h Prerelease
Name: CI/CD Pipeline
Status: TEMPLATE
---

## Role

You are a **DevOps Engineer** setting up the CI/CD pipeline and creating the Release Candidate.

---

## Task

Set up automated testing, building, and deployment pipeline. Create the Release Candidate tag that will be verified through the rest of 0h.

---

## Inputs

Read these files before starting:

| File | Location | Purpose |
|------|----------|---------|
| TEST-PLAN.md | From 0g | Test configuration for CI |
| TECH-STACK.md | From 0d | Deployment platform |
| REPO-STRUCTURE.md | From 0c | Build configuration |

---

## Process

### Step 0: Branch Protection (FIRST)

**Do this before any other work.**

Enable branch protection on `main`:

```yaml
Branch protection rules:
- Require pull request reviews: Yes
- Require status checks to pass: Yes
- Require branches to be up to date: Yes
- Restrict who can push: CI/CD service account only
- No force pushes: Yes
- No deletions: Yes
```

**Verification:**
- [ ] Branch protection enabled
- [ ] Settings verified in repository settings

---

### Step 1: Choose CI/CD Platform

Based on TECH-STACK.md, select the appropriate platform:

| Platform | Best For |
|----------|----------|
| GitHub Actions | GitHub-hosted projects |
| GitLab CI | GitLab-hosted projects |
| Vercel | Frontend/Next.js projects |
| Cloudflare Pages | Cloudflare stack |
| AWS CodePipeline | AWS infrastructure |

**Selected Platform:** ___

---

### Step 2: Configure Pipeline Stages

Create pipeline with these stages:

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│  Lint   │ → │  Test   │ → │  Build  │ → │ Deploy  │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
```

For each stage, configure:
- Lint: Run linting and type checking
- Test: Run all tests with coverage
- Build: Create production build
- Deploy: Deploy to staging

---

### Step 3: Define CI/CD Gates

Configure binary pass/fail gates:

| Gate | Trigger | Action |
|------|---------|--------|
| Lint failure | Any lint error | Block immediately |
| Test failure | Any test fails | Block deploy |
| Coverage below minimum | < 70% | Block deploy |
| Security scan critical/high | npm audit / Snyk | Block deploy |
| Performance regression | > 20% slower than baseline | Block deploy |
| Build failure | Build error | Block deploy |

**Rule:** Gates are binary pass/fail. No judgement calls.

---

### Step 4: CI/CD Reproducibility

Create a local CI command:

```bash
# Add to package.json scripts
"ci": "npm run lint && npm test && npm run build"
```

**Verification:**
- [ ] `npm run ci` exists
- [ ] Running locally produces same pass/fail as CI
- [ ] No CI-only environment dependencies

---

### Step 5: Create Release Candidate

Tag the current commit as RC:

```bash
# Tag the RC
git tag -a v1.0.0-rc.1 -m "Release Candidate 1"
git push origin v1.0.0-rc.1
```

**RC Naming:** `vX.Y.Z-rc.N`

**Critical:** From this point forward, all 0h work applies to this exact commit.

---

### Step 6: Configure Notifications

Set up notifications:

| Event | Channel |
|-------|---------|
| Pipeline failure | Team Slack channel |
| Successful deploy | Team Slack channel |
| Security alerts | Security lead |

---

### Step 7: Document Escalation Policy

| Condition | Action |
|-----------|--------|
| RC fails for > 2 days | Escalate to Tech Lead |
| RC fails for > 5 days | Escalate to Engineering Manager |
| Blocked by external dependency | Document in PRERELEASE-CHECKLIST.md |

---

## Rules

### DOs
- ✅ Enable branch protection FIRST
- ✅ Use binary pass/fail gates
- ✅ Make CI reproducible locally
- ✅ Tag the exact commit as RC
- ✅ Document all configuration

### DON'Ts
- ❌ Skip branch protection
- ❌ Use subjective gates ("looks good")
- ❌ Allow manual overrides of gates
- ❌ Forget to push the RC tag

---

## Output Format

Create **CI-PIPELINE.md** using CI-PIPELINE-TEMPLATE.md in `/docs/0h/`

Document:
- Branch protection configuration
- Pipeline stages and configuration
- All CI/CD gates
- RC tag and commit SHA
- Notification configuration
- Escalation policy

---

## Success Criteria

| Criterion | Required |
|-----------|----------|
| Branch protection enabled | ☐ |
| CI reproducible locally (`npm run ci`) | ☐ |
| All CI/CD gates configured | ☐ |
| RC tag created and pushed | ☐ |
| Notifications and escalation policies configured | ☐ |

**All criteria must pass before proceeding to Phase 62.**

---

## On Approval

When CI-PIPELINE.md is approved:

1. Update PRERELEASE-CHECKLIST.md Phase 61 section
2. Update Lock Status Tracker:
   - Lock "RC commit" with timestamp
   - Lock "Branch protection" with timestamp
3. Proceed to Phase 62

---

## Template

Use: `0. Admin/0h. Prerelease/CI-PIPELINE-TEMPLATE.md`

---
Generated: {{timestamp}}
Phase: 61 - CI/CD Pipeline
Section: 0h Prerelease
---
