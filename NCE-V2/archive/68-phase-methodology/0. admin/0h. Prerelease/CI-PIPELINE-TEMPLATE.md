# CI Pipeline Documentation

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
---

## Overview

| Field | Value |
|-------|-------|
| CI/CD Platform | {{GitHub Actions | GitLab CI | Vercel | Cloudflare Pages | AWS CodePipeline | Other}} |
| Pipeline Location | {{e.g., .github/workflows/ci.yml}} |
| Primary Branch | {{main | master}} |
| Deployment Target | {{Staging environment URL}} |

---

## Branch Protection

| Rule | Enabled | Notes |
|------|---------|-------|
| Require pull request reviews | ☐ | Minimum reviewers: {{N}} |
| Require status checks to pass | ☐ | Required checks: {{list}} |
| Require branches to be up to date | ☐ | |
| Restrict who can push | ☐ | Allowed: {{CI/CD service account}} |
| No force pushes | ☐ | |
| No deletions | ☐ | |

**Verification Date:** {{DATE}}
**Verified By:** {{NAME}}

---

## Pipeline Stages

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│  Lint   │ → │  Test   │ → │  Build  │ → │ Deploy  │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
```

### Stage 1: Lint

| Check | Tool | Configuration |
|-------|------|---------------|
| Code linting | {{ESLint | Biome | etc.}} | {{config file}} |
| Type checking | {{TypeScript | etc.}} | {{config file}} |
| Formatting | {{Prettier | Biome | etc.}} | {{config file}} |

**Command:** `{{npm run lint}}`

### Stage 2: Test

| Test Type | Framework | Coverage Target |
|-----------|-----------|-----------------|
| Unit tests | {{Jest | Vitest | etc.}} | {{70%}} |
| Integration tests | {{Jest | etc.}} | |
| E2E tests | {{Playwright | Cypress | etc.}} | |

**Command:** `{{npm test}}`

### Stage 3: Build

| Build Type | Tool | Output |
|------------|------|--------|
| Application build | {{Vite | Next.js | etc.}} | {{dist/ | .next/}} |
| Type generation | {{TypeScript}} | {{types/}} |

**Command:** `{{npm run build}}`

### Stage 4: Deploy

| Environment | Trigger | URL |
|-------------|---------|-----|
| Staging | PR merge to main | {{staging URL}} |
| Production | Manual / Tag push | {{production URL}} |

**Command:** `{{npm run deploy}}`

---

## CI/CD Gates

| Gate | Trigger Condition | Action | Status |
|------|-------------------|--------|--------|
| Lint failure | Any lint error | Block immediately | ☐ Configured |
| Test failure | Any test fails | Block deploy | ☐ Configured |
| Coverage below minimum | < {{70%}} | Block deploy | ☐ Configured |
| Security scan | Critical/high vulnerability | Block deploy | ☐ Configured |
| Performance regression | > 20% slower than baseline | Block deploy | ☐ Configured |
| Build failure | Build error | Block deploy | ☐ Configured |

**All gates are binary pass/fail. No judgement calls.**

---

## CI Reproducibility

**Requirement:** Pipeline must be reproducible locally.

| Check | Status |
|-------|--------|
| Local CI command exists | ☐ |
| Command works locally | ☐ |
| Results match CI | ☐ |

**Local CI Command:** `{{npm run ci}}`

**Verification:**
```bash
# Run locally
npm run ci

# Expected: Same pass/fail as CI
```

---

## Release Candidate

| Field | Value |
|-------|-------|
| RC Tag | {{v1.0.0-rc.1}} |
| Commit SHA | {{abc123...}} |
| Created Date | {{DATE}} |
| Created By | {{NAME}} |

**Tag Command:**
```bash
git tag -a {{RC_TAG}} -m "Release Candidate {{N}}"
git push origin {{RC_TAG}}
```

---

## Notifications

| Event | Channel | Recipients |
|-------|---------|------------|
| Pipeline failure | {{Slack #channel}} | {{Team}} |
| Successful deploy | {{Slack #channel}} | {{Team}} |
| Security alert | {{Slack #security}} | {{Security Lead}} |

---

## Escalation Policy

| Condition | Action | Contact |
|-----------|--------|---------|
| RC fails for > 2 days | Escalate to Tech Lead | {{Name}} |
| RC fails for > 5 days | Escalate to Engineering Manager | {{Name}} |
| Blocked by external dependency | Document in PRERELEASE-CHECKLIST.md | |

---

## Pipeline Configuration

### GitHub Actions Example

```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint

  test:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm test -- --coverage
      - name: Check coverage
        run: |
          COVERAGE=$(cat coverage/coverage-summary.json | jq '.total.lines.pct')
          if (( $(echo "$COVERAGE < 70" | bc -l) )); then
            echo "Coverage $COVERAGE% is below 70%"
            exit 1
          fi

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run build

  security:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level=high

  deploy-staging:
    needs: [build, security]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run build
      - run: npm run deploy:staging
```

---

## Verification Checklist

| Item | Status | Verified By | Date |
|------|--------|-------------|------|
| Branch protection enabled | ☐ | | |
| All pipeline stages configured | ☐ | | |
| All gates configured | ☐ | | |
| CI reproducible locally | ☐ | | |
| RC tag created | ☐ | | |
| Notifications configured | ☐ | | |
| Escalation policy documented | ☐ | | |

---

## Phase 61 Success Criteria

| Criterion | Status |
|-----------|--------|
| Branch protection enabled | ☐ |
| CI reproducible locally (`npm run ci`) | ☐ |
| All CI/CD gates configured | ☐ |
| RC tag created and pushed | ☐ |
| Notifications and escalation policies configured | ☐ |

**All criteria must pass before proceeding to Phase 62.**

---
Generated: {{timestamp}}
Phase: 61 - CI/CD Pipeline
Template: CI-PIPELINE-TEMPLATE.md v1.0
---
