# Phase 62: Environment Configuration

---
Phase: 62
Section: 0h Prerelease
Name: Environment Configuration
Status: TEMPLATE
---

## Role

You are a **Site Reliability Engineer (SRE)** configuring the staging environment and verifying operational readiness.

---

## Task

Configure staging environment, manage secrets, verify migrations are backward-compatible, test rollback procedures, and audit feature flags.

---

## Inputs

Read these files before starting:

| File | Location | Purpose |
|------|----------|---------|
| CI-PIPELINE.md | From Phase 61 | RC tag and commit SHA |
| TECH-STACK.md | From 0d | Infrastructure choices |
| Migration scripts | From 0f | Migrations to verify |

---

## Process

### Step 1: Set Up Staging Environment

**IaC Requirement:** Staging must be defined as Infrastructure-as-Code.

Configure staging to match production:

| Aspect | Staging | Production | Must Match |
|--------|---------|------------|------------|
| Infrastructure type | | | ✅ |
| Database type | | | ✅ |
| Environment variables | | | ✅ (keys) |
| Feature flag config | | | ✅ |

**IaC Verification:**
- [ ] Terraform / Pulumi / CloudFormation files exist
- [ ] Staging can be torn down and recreated from code
- [ ] No manual configuration steps required

**No Manual Staging Edits Rule:**
> Any manual change to staging invalidates the environment and must be reverted or codified in IaC before proceeding.

---

### Step 2: Verify Staging Data Freshness

**Requirement:** Staging data must be refreshed within 24 hours of starting Phase 63.

Document staging data policy:

| Question | Answer |
|----------|--------|
| Uses anonymized production data? | Yes / No / N/A |
| Data refresh frequency | Daily / Weekly / On-demand |
| Last data refresh | {{DATE}} |
| Staging persistent or ephemeral? | Persistent / Ephemeral |
| Who can reset staging? | {{ROLES}} |

**Verification:**
- [ ] Data refreshed within 24h
- [ ] Anonymization verified (if applicable)
- [ ] Schema matches production

---

### Step 3: Deploy RC to Staging

Deploy the exact RC commit to staging:

```bash
# Verify RC is deployed
{{VERSION_CHECK_COMMAND}}
# Expected: {{RC_TAG}}
```

**Verification:**
- [ ] Staging runs RC commit
- [ ] Commit SHA matches tag
- [ ] Health checks passing

---

### Step 4: Configure Secrets Management

Document all secrets:

| Secret | Location | Rotation Frequency |
|--------|----------|-------------------|
| Database credentials | Secrets Manager | 90 days |
| API keys | Secrets Manager | 90 days |
| JWT secret | Secrets Manager | 90 days |

**Verification:**
- [ ] All secrets in secrets manager (not in code)
- [ ] No secrets in repository
- [ ] GitLeaks/TruffleHog scan clean

---

### Step 5: Secrets Rotation Verification

Test that secrets can be rotated without downtime:

| Secret Type | Rotation Tested | Zero-Downtime |
|-------------|-----------------|---------------|
| Database password | ☐ | ☐ |
| API keys | ☐ | ☐ |
| JWT secret | ☐ | ☐ |

**Process:**
1. Document rotation procedure for each secret type
2. Test database password rotation on staging
3. Test API key rotation on staging
4. Verify zero-downtime rotation is possible

**Output:** SECRETS-ROTATION.md

---

### Step 6: Verify Migration Backward Compatibility

**Expand/Contract Pattern Required:**

Every migration must allow the previous version of code to run against the new schema.

| Migration Type | Requirement |
|----------------|-------------|
| Add column | Must have default or be nullable |
| Remove column | Previous code must not reference it |
| Rename column | Use expand/contract |
| Change type | Use expand/contract |

**Verification Steps:**
1. Run migrations on staging
2. Deploy previous version of code
3. Verify previous version still works
4. Deploy new version of code
5. Verify new version works

- [ ] All migrations pass backward compatibility check

**Irreversible Data Transform Rule:**
> If a migration includes irreversible data transformation, a documented data backup and restore procedure is mandatory.

---

### Step 7: Rollback Verification (Dry Run)

**Force a deployment failure and verify recovery:**

1. Deploy RC to staging
2. Intentionally deploy a broken build (or simulate failure)
3. Execute rollback procedure
4. Verify staging returns to RC state
5. Verify data integrity after rollback

**Rollback Time Targets:**

| Service Type | Target | Hard Limit |
|--------------|--------|------------|
| Stateless services | < 5 minutes | 10 minutes |
| Stateful services | < 15 minutes | 30 minutes |
| Database rollback | < 30 minutes | 60 minutes |

**Default:** < 10 minutes unless architecture constraints require override.

**Verification:**
- [ ] Rollback procedure tested and documented
- [ ] Rollback completes within target time
- [ ] Data integrity verified after rollback

---

### Step 8: Feature Flag Audit

**For canary/dark launch readiness:**

| Check | Status |
|-------|--------|
| All new features wrapped in feature flags | ☐ |
| Default state for all new flags is "OFF" | ☐ |
| Feature flag service accessible from staging | ☐ |
| Monitoring supports version-split dashboards | ☐ |

**Version-Split Dashboard:** Can you see p95 latency for the RC vs previous version simultaneously?

---

## Rules

### DOs
- ✅ Use Infrastructure as Code for staging
- ✅ Verify data freshness before Phase 63
- ✅ Test secrets rotation on staging
- ✅ Use Expand/Contract pattern for migrations
- ✅ Test rollback and record time
- ✅ Audit all feature flags

### DON'Ts
- ❌ Make manual changes to staging
- ❌ Skip backward compatibility testing
- ❌ Accept rollback times over target without justification
- ❌ Leave new features without flags

---

## Output Format

Create **ENVIRONMENT-CONFIG.md** using ENVIRONMENT-CONFIG-TEMPLATE.md in `/docs/0h/`

Also create:
- SECRETS-ROTATION.md (rotation procedures)

Document:
- Staging environment configuration
- IaC verification
- Data freshness policy
- Secrets management
- Rotation procedures
- Migration compatibility results
- Rollback test results
- Feature flag audit

---

## Success Criteria

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

**All criteria must pass before proceeding to Phase 63.**

---

## Failure Path

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

---

## On Approval

When ENVIRONMENT-CONFIG.md is approved:

1. Update PRERELEASE-CHECKLIST.md Phase 62 section
2. Update Lock Status Tracker:
   - Lock "CI/CD pipeline" with timestamp
   - Lock "Deployment config" with timestamp
3. Proceed to Phase 63

---

## Template

Use: `0. Admin/0h. Prerelease/ENVIRONMENT-CONFIG-TEMPLATE.md`

---
Generated: {{timestamp}}
Phase: 62 - Environment Configuration
Section: 0h Prerelease
---
