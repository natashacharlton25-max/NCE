# Operations Runbook

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
Application: {{APPLICATION_NAME}}
---

## Quick Links

| Resource | URL |
|----------|-----|
| Application | {{PRODUCTION_URL}} |
| Staging | {{STAGING_URL}} |
| Monitoring Dashboard | {{MONITORING_URL}} |
| Logs | {{LOGS_URL}} |
| Error Tracking | {{ERROR_TRACKING_URL}} |
| Status Page | {{STATUS_PAGE_URL}} |

---

## Incident Response Plan (IRP)

### Severity Definitions

| Severity | Definition | Examples | Response Time |
|----------|------------|----------|---------------|
| **Sev1** | Service completely down | Site unreachable, all requests failing | < 15 minutes |
| **Sev2** | Major functionality impaired | Core feature broken, high error rate | < 30 minutes |
| **Sev3** | Minor functionality impaired | Non-critical feature broken | < 2 hours |
| **Sev4** | Cosmetic or minor issue | UI glitch, typo | Next business day |

### Escalation Path

| Level | Contact | When to Escalate |
|-------|---------|------------------|
| **L1: On-Call Engineer** | {{Name}} - {{Contact}} | First response |
| **L2: Tech Lead** | {{Name}} - {{Contact}} | L1 cannot resolve in 30 min |
| **L3: Engineering Manager** | {{Name}} - {{Contact}} | L2 cannot resolve in 1 hour |
| **L4: Head of Engineering** | {{Name}} - {{Contact}} | Customer impact > 2 hours |

### On-Call Rotation

| Week | Primary | Secondary |
|------|---------|-----------|
| {{Week 1}} | {{Name}} | {{Name}} |
| {{Week 2}} | {{Name}} | {{Name}} |

**On-Call Schedule:** {{Link to PagerDuty/Opsgenie schedule}}

### Communication Templates

#### Internal Status Update

```
🔴 INCIDENT: [Brief description]
Severity: Sev[1/2/3/4]
Status: [Investigating | Identified | Monitoring | Resolved]
Impact: [Description of user impact]
Next update: [Time]
```

#### Customer Communication (Sev1/Sev2)

```
We are currently experiencing issues with [service/feature].

Impact: [What users are experiencing]
Status: Our team is actively investigating.
Next update: We will provide an update within [time].

We apologize for any inconvenience.
```

#### Resolution Communication

```
The issue affecting [service/feature] has been resolved.

Root cause: [Brief explanation]
Resolution: [What was done]
Duration: [Start time] to [End time]

We apologize for any inconvenience caused.
```

---

## Common Issues and Fixes

### Issue: Application Not Responding

**Symptoms:**
- Health checks failing
- 5xx errors
- Requests timing out

**Diagnosis:**
```bash
# Check application status
{{STATUS_CHECK_COMMAND}}

# Check logs for errors
{{LOG_COMMAND}}

# Check resource utilization
{{RESOURCE_CHECK_COMMAND}}
```

**Resolution:**
1. Check if deployment is in progress
2. Restart application: `{{RESTART_COMMAND}}`
3. If persists, check database connectivity
4. If persists, escalate to L2

---

### Issue: High Error Rate

**Symptoms:**
- Error rate > 1%
- Alerts firing

**Diagnosis:**
```bash
# Check error tracking
# URL: {{ERROR_TRACKING_URL}}

# Check recent deployments
# URL: {{CI_URL}}

# Check logs for patterns
{{LOG_SEARCH_COMMAND}}
```

**Resolution:**
1. Identify error type (client vs server)
2. Check for recent changes
3. If related to deployment, initiate rollback
4. If external dependency, check status page

---

### Issue: High Latency

**Symptoms:**
- p95 latency > {{threshold}}
- Slow user experience

**Diagnosis:**
```bash
# Check database query performance
{{DB_SLOW_QUERY_COMMAND}}

# Check external service latency
{{EXTERNAL_SERVICE_CHECK}}

# Check resource utilization
{{RESOURCE_CHECK_COMMAND}}
```

**Resolution:**
1. Identify slow endpoints
2. Check database indexes
3. Check for resource exhaustion
4. Check external dependencies
5. If related to deployment, consider rollback

---

### Issue: Database Connection Errors

**Symptoms:**
- "Connection refused" errors
- Timeout errors

**Diagnosis:**
```bash
# Check database status
{{DB_STATUS_COMMAND}}

# Check connection pool
{{CONNECTION_POOL_CHECK}}

# Check network connectivity
{{NETWORK_CHECK}}
```

**Resolution:**
1. Verify database is running
2. Check connection pool exhaustion
3. Check for max connections limit
4. Restart application if connection pool stuck

---

### Issue: Out of Memory

**Symptoms:**
- OOM errors in logs
- Application restarts

**Diagnosis:**
```bash
# Check memory usage
{{MEMORY_CHECK_COMMAND}}

# Check for memory leaks
{{HEAP_DUMP_COMMAND}}
```

**Resolution:**
1. Restart application (immediate relief)
2. Scale up instances if needed
3. Investigate memory leak
4. Schedule fix for next release

---

## Rollback Procedure

**Target Time:** < {{10 minutes}}

### Quick Rollback

```bash
# Step 1: Initiate rollback
{{ROLLBACK_COMMAND}}

# Step 2: Verify rollback
{{VERSION_CHECK_COMMAND}}

# Step 3: Confirm health
{{HEALTH_CHECK_COMMAND}}
```

### Detailed Rollback Steps

1. **Announce rollback** in incident channel
2. **Execute rollback:**
   ```bash
   {{DETAILED_ROLLBACK_STEPS}}
   ```
3. **Verify previous version is running:**
   ```bash
   {{VERSION_CHECK_COMMAND}}
   # Expected: {{PREVIOUS_VERSION}}
   ```
4. **Verify health:**
   ```bash
   {{HEALTH_CHECK_COMMAND}}
   # Expected: 200 OK
   ```
5. **Monitor error rates** for 5 minutes
6. **Announce resolution** in incident channel

### Database Rollback (if needed)

**⚠️ Use with caution**

```bash
# Only if migrations need reverting
{{DB_ROLLBACK_COMMAND}}
```

---

## Maintenance Procedures

### Planned Maintenance Window

**Standard window:** {{e.g., Sunday 2-4 AM UTC}}

**Process:**
1. Schedule maintenance in status page
2. Notify customers 24h in advance
3. Notify team in #deployments
4. Execute maintenance
5. Verify system health
6. Update status page

### Secret Rotation

**Rotation frequency:** {{e.g., 90 days}}

**Process:**
```bash
# 1. Generate new secret
{{SECRET_GENERATION_COMMAND}}

# 2. Add new secret to secrets manager
{{SECRET_ADD_COMMAND}}

# 3. Update application to use new secret
{{SECRET_UPDATE_COMMAND}}

# 4. Verify application works with new secret
{{VERIFICATION_COMMAND}}

# 5. Remove old secret after grace period
{{SECRET_REMOVE_COMMAND}}
```

### Database Maintenance

**Regular tasks:**
- [ ] Backup verification: {{frequency}}
- [ ] Index optimization: {{frequency}}
- [ ] Connection pool tuning: {{frequency}}

---

## Monitoring and Alerting

### Alert Response Guide

| Alert | Severity | First Action |
|-------|----------|--------------|
| Service Down | Sev1 | Check health endpoint, restart if needed |
| High Error Rate | Sev2 | Check error tracking, evaluate rollback |
| High Latency | Sev2 | Check resources, identify slow endpoint |
| High CPU | Sev3 | Scale up or investigate cause |
| High Memory | Sev3 | Restart if > 90%, investigate leak |
| Certificate Expiry | Sev2 | Renew certificate |
| Cost Anomaly | Sev3 | Investigate usage spike |

### Dashboard Guide

| Dashboard | What to Check | Link |
|-----------|---------------|------|
| Overview | General health | {{URL}} |
| Errors | Error types and trends | {{URL}} |
| Performance | Latency percentiles | {{URL}} |
| Infrastructure | CPU, memory, network | {{URL}} |
| Costs | Daily burn rate | {{URL}} |

---

## Contacts

### Team Contacts

| Role | Name | Email | Phone/Slack |
|------|------|-------|-------------|
| Release Owner | {{Name}} | {{Email}} | {{Contact}} |
| Tech Lead | {{Name}} | {{Email}} | {{Contact}} |
| On-Call (Primary) | {{Name}} | {{Email}} | {{Contact}} |
| On-Call (Secondary) | {{Name}} | {{Email}} | {{Contact}} |

### External Contacts

| Service | Support URL | Notes |
|---------|-------------|-------|
| {{Cloud Provider}} | {{URL}} | {{Account ID}} |
| {{Database Provider}} | {{URL}} | {{Support tier}} |
| {{CDN Provider}} | {{URL}} | |

---

## Appendix

### Environment Details

| Environment | URL | Region |
|-------------|-----|--------|
| Production | {{URL}} | {{Region}} |
| Staging | {{URL}} | {{Region}} |

### Architecture Overview

```
{{ARCHITECTURE_DIAGRAM_OR_DESCRIPTION}}
```

### Useful Commands

```bash
# Check application version
{{VERSION_COMMAND}}

# Check application logs
{{LOG_COMMAND}}

# Check database status
{{DB_STATUS_COMMAND}}

# Check external service status
{{EXTERNAL_STATUS_COMMAND}}
```

---

## Runbook Accessibility Checklist

| Check | Status |
|-------|--------|
| Runbook linked in alert templates | ☐ |
| Sections have deep-linkable anchors | ☐ |
| Critical procedures bookmarked | ☐ |
| Tested by unfamiliar team member | ☐ |

---
Generated: {{timestamp}}
Phase: 64 - Documentation & Final Review
Template: RUNBOOK-TEMPLATE.md v1.0
---
