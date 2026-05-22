# Environment Configuration

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
| Application | {{APPLICATION_NAME}} |
| RC Tag | {{RC_TAG}} |
| Staging URL | {{STAGING_URL}} |
| Staging Region | {{REGION}} |

---

## Staging Environment

### Infrastructure

| Component | Type | Configuration |
|-----------|------|---------------|
| Compute | {{Service type}} | {{Instance size}} |
| Database | {{Service type}} | {{Instance size}} |
| Cache | {{Service type}} | {{Instance size}} |
| CDN | {{Provider}} | {{Config}} |

### IaC Definition

| Check | Status | Location |
|-------|--------|----------|
| Infrastructure as Code exists | ☐ | {{Path to IaC files}} |
| Staging can be destroyed | ☐ | |
| Staging can be recreated | ☐ | |
| No manual configuration | ☐ | |

**IaC Tool:** {{Terraform | Pulumi | CloudFormation | CDK}}

**Deployment Command:**
```bash
{{IaC deployment command}}
```

### Staging vs Production Parity

| Aspect | Staging | Production | Parity |
|--------|---------|------------|--------|
| Infrastructure | {{Config}} | {{Config}} | ☐ Match |
| Database type | {{Type}} | {{Type}} | ☐ Match |
| Environment variables | {{All defined}} | {{All defined}} | ☐ Match |
| Secrets (structure) | {{Same keys}} | {{Same keys}} | ☐ Match |
| Feature flags | {{Same config}} | {{Same config}} | ☐ Match |

**Difference Justification:**
| Difference | Staging | Production | Reason |
|------------|---------|------------|--------|
| Instance size | {{smaller}} | {{larger}} | Cost optimization |

---

## Staging Data Policy

| Question | Answer |
|----------|--------|
| Uses anonymized production data? | ☐ Yes / ☐ No / ☐ N/A |
| Data refresh frequency | {{Daily | Weekly | On-demand | Never}} |
| Last data refresh | {{DATE}} |
| Staging persistent or ephemeral? | {{Persistent | Ephemeral}} |
| Who can reset staging? | {{ROLES}} |

### Data Anonymization

| Data Type | Anonymization Method |
|-----------|---------------------|
| User emails | Hashed/Replaced |
| Names | Faker-generated |
| Addresses | Removed/Generalized |
| Payment info | Removed |

### Data Freshness Verification

**Requirement:** Staging data must be refreshed within 24 hours of starting Phase 63.

| Check | Status |
|-------|--------|
| Data refreshed within 24h | ☐ |
| Anonymization verified | ☐ |
| Schema matches production | ☐ |

---

## RC Deployment Verification

| Check | Status | Evidence |
|-------|--------|----------|
| Staging runs RC commit | ☐ | |
| Commit SHA matches tag | ☐ | SHA: {{SHA}} |
| Full application deployed | ☐ | |
| Health checks passing | ☐ | |

**Verification Command:**
```bash
# Get deployed version
{{VERSION_CHECK_COMMAND}}

# Expected: {{RC_TAG}}
```

---

## Secrets Management

### Secrets Inventory

| Secret | Location | Rotation Frequency |
|--------|----------|-------------------|
| Database credentials | {{Secrets Manager}} | 90 days |
| API keys | {{Secrets Manager}} | 90 days |
| JWT secret | {{Secrets Manager}} | 90 days |
| Third-party tokens | {{Secrets Manager}} | Per provider |

### Secrets Rotation Verification

| Secret Type | Rotation Tested | Zero-Downtime | Documented |
|-------------|-----------------|---------------|------------|
| Database password | ☐ | ☐ | ☐ |
| API keys | ☐ | ☐ | ☐ |
| JWT secret | ☐ | ☐ | ☐ |

**Rotation Procedure Location:** SECRETS-ROTATION.md

---

## Migration Verification

### Migration List

| Migration | Description | Reversible | Status |
|-----------|-------------|------------|--------|
| {{Migration ID}} | {{Description}} | ☐ Yes / ☐ No | ☐ Applied |

### Backward Compatibility Test

| Step | Status |
|------|--------|
| Migrations run on staging | ☐ |
| Previous code version deployed | ☐ |
| Previous version works | ☐ |
| New code version deployed | ☐ |
| New version works | ☐ |

**Expand/Contract Compliance:**

| Migration | Pattern Used | Backward Compatible |
|-----------|--------------|---------------------|
| {{Migration}} | {{Expand | Contract | Both}} | ☐ Yes |

### Irreversible Transformations

| Transformation | Backup Taken | Restore Verified |
|----------------|--------------|------------------|
| {{None or list}} | ☐ | ☐ |

---

## Rollback Verification

### Rollback Dry Run Results

| Step | Result | Time |
|------|--------|------|
| Initiate rollback | ☐ Success | |
| Previous version deployed | ☐ Success | |
| Health checks pass | ☐ Success | |
| Data integrity verified | ☐ Success | |
| **Total rollback time** | | {{TIME}} |

### Rollback Time Target

| Service Type | Target | Actual | Status |
|--------------|--------|--------|--------|
| Application rollback | < {{10}} min | {{TIME}} | ☐ Met |

### Rollback Procedure

```bash
{{ROLLBACK_COMMANDS}}
```

---

## Feature Flags

### Flag Inventory

| Flag | Description | Default State | Status |
|------|-------------|---------------|--------|
| {{flag_name}} | {{Description}} | OFF | ☐ Verified |

### Feature Flag Audit

| Check | Status |
|-------|--------|
| All new features wrapped in flags | ☐ |
| Default state for all new flags is OFF | ☐ |
| Feature flag service accessible from staging | ☐ |
| Version-split dashboards available | ☐ |

**Feature Flag Service:** {{LaunchDarkly | Split | Custom | etc.}}

---

## Environment Variables

### Required Variables

| Variable | Set in Staging | Set in Production | Sensitive |
|----------|----------------|-------------------|-----------|
| `DATABASE_URL` | ☐ | ☐ | Yes |
| `API_KEY` | ☐ | ☐ | Yes |
| `{{OTHER}}` | ☐ | ☐ | |

### Variable Sources

| Environment | Source |
|-------------|--------|
| Staging | {{Secrets Manager / .env / Platform config}} |
| Production | {{Secrets Manager / Platform config}} |

---

## Verification Summary

### Phase 62 Success Criteria

| Criterion | Status |
|-----------|--------|
| Staging environment runs RC commit | ☐ |
| Staging is IaC-reproducible | ☐ |
| Staging data is fresh (within 24h) | ☐ |
| Secrets rotation tested | ☐ |
| Migrations backward-compatible | ☐ |
| Rollback tested within time target | ☐ |
| Feature flags audited | ☐ |
| No manual staging edits | ☐ |

**All criteria must pass before proceeding to Phase 63.**

---

## Failure Log (if applicable)

| Date | Issue | Root Cause | Resolution |
|------|-------|------------|------------|
| | | | |

---
Generated: {{timestamp}}
Phase: 62 - Environment Configuration
Template: ENVIRONMENT-CONFIG-TEMPLATE.md v1.0
---
