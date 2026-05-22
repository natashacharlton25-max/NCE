# Deployment Log

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
---

## Deployment Header

| Field | Value |
|-------|-------|
| RC Tag | {{RC_TAG}} |
| Commit SHA | {{COMMIT_SHA}} |
| Deployer | {{NAME}} |
| Deployment Strategy | {{Rolling | Blue-Green | Canary | Shadow}} |
| Start Time | {{TIMESTAMP}} |
| End Time | {{TIMESTAMP}} |
| Duration | {{DURATION}} |
| Golden Hour Ends | {{TIMESTAMP}} |

---

## Timestamped Steps

| Time | Step | Command/Action | Output/Result | Status |
|------|------|----------------|---------------|--------|
| | Deployment announced | Slack notification | | ☐ |
| | Traffic shadow started (if applicable) | | | ☐ / N/A |
| | Shadow metrics verified | | | ☐ / N/A |
| | Deployment initiated | `{{COMMAND}}` | | ☐ |
| | Deployment in progress | | | ☐ |
| | Deployment complete | | | ☐ |
| | Health check | `{{COMMAND}}` | | ☐ |
| | Version verified | `{{COMMAND}}` | {{RC_TAG}} | ☐ |
| | Golden Hour started | | | ☐ |
| | Deployment announced complete | Slack notification | | ☐ |

---

## Traffic Shadowing (if applicable)

| Field | Value |
|-------|-------|
| Shadow enabled | ☐ Yes / ☐ No / ☐ N/A |
| Traffic percentage | {{5%}} |
| Shadow duration | {{minutes}} |
| Shadow metrics OK | ☐ |

**Shadow Metrics:**

| Metric | Baseline | Shadow | Status |
|--------|----------|--------|--------|
| Error rate | | | ☐ Pass |
| p95 latency | | | ☐ Pass |
| CPU | | | ☐ Pass |
| Memory | | | ☐ Pass |

---

## Canary Progression (if applicable)

| Stage | % Traffic | Start Time | End Time | Metrics OK | Status |
|-------|-----------|------------|----------|------------|--------|
| 1 | 1% | | | ☐ | |
| 2 | 5% | | | ☐ | |
| 3 | 25% | | | ☐ | |
| 4 | 50% | | | ☐ | |
| 5 | 100% | | | ☐ | |

**Canary Exit Criteria Met:**
- [ ] Error rate ≤ baseline
- [ ] p95 latency ≤ baseline
- [ ] No new error types
- [ ] Duration met (30-60 min per stage)

---

## Verification Results

| Check | Status | Notes |
|-------|--------|-------|
| Version matches RC tag | ☐ | |
| Health endpoint responding | ☐ | |
| No immediate errors | ☐ | |
| Metrics within expected range | ☐ | |

**Version Check:**
```bash
{{VERSION_CHECK_COMMAND}}
# Result: {{RESULT}}
```

**Health Check:**
```bash
{{HEALTH_CHECK_COMMAND}}
# Result: {{RESULT}}
```

---

## Golden Hour Status

| Field | Value |
|-------|-------|
| Golden Hour started | {{TIMESTAMP}} |
| Golden Hour ends | {{TIMESTAMP}} |
| Release Owner available | ☐ |
| Stack freeze active | ☐ |

**Golden Hour Events:**

| Time | Event | Severity | Action Taken |
|------|-------|----------|--------------|
| | | | |

---

## Post-Golden-Hour Review

| Check | Status | Notes |
|-------|--------|-------|
| Any anomalies during Golden Hour? | ☐ No / ☐ Yes | |
| Any alerts triggered? | ☐ No / ☐ Yes | |
| Any unexpected log patterns? | ☐ No / ☐ Yes | |
| Any user complaints? | ☐ No / ☐ Yes | |
| Metrics still within baseline? | ☐ | |
| Cost trending as expected? | ☐ | |

---

## Rollback Decision Markers

| Field | Value |
|-------|-------|
| Rollback triggered | ☐ No / ☐ Yes |
| Rollback reason | {{if applicable}} |
| Rollback time | {{if applicable}} |
| Evidence captured before rollback | ☐ / N/A |
| INCIDENT-REPORT.md created | ☐ / N/A |

---

## Abort Threshold Monitoring

| Metric | Abort Threshold | Observed | Status |
|--------|-----------------|----------|--------|
| Error rate | > 2× baseline | | ☐ OK |
| p95 latency | > 2× baseline | | ☐ OK |
| Cost spike | > 3× baseline (15 min) | | ☐ OK |
| New error types | Any | | ☐ OK |

---

## Deployment Summary

| Outcome | Selected |
|---------|----------|
| **SUCCESS** - Deployment complete, proceeding to Phase 67 | ☐ |
| **ROLLED BACK** - Deployment failed, returned to previous version | ☐ |
| **ABORTED** - Deployment stopped before completion | ☐ |

---

## Notes

```
{{Any additional context, observations, or issues encountered}}
```

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Deployer | | | ☐ Deployment logged |

---
Generated: {{timestamp}}
Phase: 66 - Production Deployment
Template: DEPLOYMENT-LOG-TEMPLATE.md v1.0
---
