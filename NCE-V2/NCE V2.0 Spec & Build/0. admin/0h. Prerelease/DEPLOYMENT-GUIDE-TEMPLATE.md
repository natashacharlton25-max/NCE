# Deployment Guide

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
| Application | {{Application Name}} |
| Release Candidate | {{RC_TAG}} |
| Deployment Target | {{Production URL}} |
| Deployment Method | {{Automated CI/CD | Manual}} |
| Expected Downtime | {{Zero | X minutes}} |

---

## Prerequisites

### Access Requirements

| System | Access Level | Contact |
|--------|--------------|---------|
| Cloud Provider Console | {{Admin | Deploy}} | {{Name}} |
| CI/CD Platform | {{Admin | Operator}} | {{Name}} |
| Secrets Manager | {{Read | Write}} | {{Name}} |
| Monitoring Dashboard | {{Read}} | {{Name}} |

### Pre-Deployment Checklist

| Item | Status |
|------|--------|
| RC commit SHA verified | ☐ |
| All 0h phases complete | ☐ |
| Sign-offs collected | ☐ |
| Rollback plan reviewed | ☐ |
| On-call engineer notified | ☐ |
| Status page ready | ☐ |

---

## Deployment Steps

### Step 1: Pre-Flight Checks

```bash
# Verify RC tag
git fetch --tags
git show {{RC_TAG}}

# Verify commit SHA matches
git rev-parse {{RC_TAG}}
# Expected: {{COMMIT_SHA}}

# Verify CI is green
# Check: {{CI_URL}}
```

### Step 2: Notify Team

- [ ] Post in deployment channel: "Starting deployment of {{RC_TAG}}"
- [ ] Ensure on-call engineer is available
- [ ] Update status page (if applicable)

### Step 3: Deploy to Production

**Option A: Automated Deployment**

```bash
# Trigger deployment via CI/CD
# Method: {{Tag push | Manual trigger | Merge to release branch}}

{{DEPLOYMENT_COMMAND}}
```

**Option B: Manual Deployment**

```bash
# Step-by-step manual deployment
{{MANUAL_STEPS}}
```

### Step 4: Verify Deployment

| Check | Command/URL | Expected Result |
|-------|-------------|-----------------|
| Health check | `{{curl -f https://api.example.com/health}}` | 200 OK |
| Version check | `{{curl https://api.example.com/version}}` | `{{RC_TAG}}` |
| Smoke test | {{URL or command}} | Pass |

### Step 5: Post-Deployment

- [ ] Verify metrics are flowing
- [ ] Check error rates
- [ ] Confirm alerting is active
- [ ] Post in deployment channel: "Deployment of {{RC_TAG}} complete"
- [ ] Update status page (if applicable)

---

## Rollback Procedure

### When to Rollback

| Condition | Action |
|-----------|--------|
| Error rate > {{1%}} | Rollback immediately |
| Health checks failing | Rollback immediately |
| p95 latency > {{2x baseline}} | Evaluate, likely rollback |
| Critical bug reported | Evaluate severity |

### Rollback Steps

**Target Rollback Time:** {{< 10 minutes}}

#### Step 1: Initiate Rollback

```bash
# Announce rollback
# Post in deployment channel: "⚠️ Initiating rollback of {{RC_TAG}}"

{{ROLLBACK_COMMAND}}
```

#### Step 2: Verify Rollback

| Check | Command/URL | Expected Result |
|-------|-------------|-----------------|
| Health check | `{{curl -f https://api.example.com/health}}` | 200 OK |
| Version check | `{{curl https://api.example.com/version}}` | `{{PREVIOUS_VERSION}}` |
| Error rates | {{Monitoring URL}} | Decreasing |

#### Step 3: Post-Rollback

- [ ] Post in deployment channel: "Rollback complete. Running version: {{PREVIOUS_VERSION}}"
- [ ] Create incident ticket
- [ ] Schedule post-mortem
- [ ] Update PRERELEASE-CHECKLIST.md with failure reason

### Rollback Targets

| Service Type | Target | Hard Limit |
|--------------|--------|------------|
| Stateless services | < 5 minutes | 10 minutes |
| Stateful services | < 15 minutes | 30 minutes |
| Database rollback | < 30 minutes | 60 minutes |

---

## Database Migrations

### Pre-Deployment Migration Check

| Check | Status |
|-------|--------|
| Migrations are backward-compatible | ☐ |
| Previous code version works with new schema | ☐ |
| Data backup taken (if irreversible) | ☐ |

### Migration Commands

```bash
# Run migrations
{{MIGRATION_COMMAND}}

# Verify migration status
{{MIGRATION_STATUS_COMMAND}}
```

### Migration Rollback

```bash
# If migration needs rollback
{{MIGRATION_ROLLBACK_COMMAND}}
```

**Note:** Following Expand/Contract pattern means code rollback should work without migration rollback in most cases.

---

## Environment Configuration

### Production Environment Variables

| Variable | Source | Notes |
|----------|--------|-------|
| `DATABASE_URL` | {{Secrets Manager}} | Do not log |
| `API_KEY` | {{Secrets Manager}} | Do not log |
| {{OTHER_VARS}} | | |

### Configuration Verification

```bash
# Verify environment is correctly configured
{{VERIFICATION_COMMAND}}
```

---

## Monitoring During Deployment

### Key Metrics to Watch

| Metric | Normal Range | Alert Threshold |
|--------|--------------|-----------------|
| Error rate | < 0.1% | > 1% |
| p95 latency | < {{500ms}} | > {{1000ms}} |
| CPU utilization | < 50% | > 80% |
| Memory utilization | < 70% | > 85% |

### Dashboard Links

| Dashboard | URL |
|-----------|-----|
| Application metrics | {{URL}} |
| Infrastructure | {{URL}} |
| Error tracking | {{URL}} |
| Logs | {{URL}} |

---

## Troubleshooting

### Common Issues

#### Issue: Health check failing

**Symptoms:** Health endpoint returns 5xx or times out

**Resolution:**
1. Check application logs: `{{LOG_COMMAND}}`
2. Verify database connectivity
3. Check secrets are loaded
4. If unresolved in 5 minutes, rollback

#### Issue: High error rate

**Symptoms:** Error rate > 1%

**Resolution:**
1. Check error tracking dashboard
2. Identify error type/pattern
3. If widespread, rollback immediately
4. If localized, evaluate severity

#### Issue: Performance degradation

**Symptoms:** p95 latency > 2x baseline

**Resolution:**
1. Check resource utilization
2. Check database query performance
3. Check external service latency
4. If degradation persists > 10 minutes, rollback

---

## Contacts

| Role | Name | Contact |
|------|------|---------|
| Release Owner | {{Name}} | {{Contact}} |
| On-Call Engineer | {{Name}} | {{Contact}} |
| Database Admin | {{Name}} | {{Contact}} |
| Security Contact | {{Name}} | {{Contact}} |

---

## Deployment History

| Date | Version | Deployer | Result | Notes |
|------|---------|----------|--------|-------|
| {{DATE}} | {{RC_TAG}} | {{Name}} | ☐ Success / ☐ Rollback | |

---
Generated: {{timestamp}}
Phase: 64 - Documentation & Final Review
Template: DEPLOYMENT-GUIDE-TEMPLATE.md v1.0
---
