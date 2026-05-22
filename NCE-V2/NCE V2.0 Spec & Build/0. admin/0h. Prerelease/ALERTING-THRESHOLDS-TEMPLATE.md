# Alerting Thresholds

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
---

## Overview

This document defines alerting thresholds for the application. These thresholds are defined in 0h (Prerelease) and implemented in 0i (Rollout).

| Field | Value |
|-------|-------|
| Application | {{APPLICATION_NAME}} |
| Environment | Production |
| Monitoring Platform | {{Datadog | New Relic | Grafana | CloudWatch | etc.}} |
| Alerting Platform | {{PagerDuty | Opsgenie | VictorOps | etc.}} |

---

## Alert Severity Levels

| Severity | Definition | Response Time | Notification |
|----------|------------|---------------|--------------|
| **P1 - Critical** | Service down or severely degraded | < 15 min | Page on-call |
| **P2 - High** | Major functionality impaired | < 30 min | Slack + Email |
| **P3 - Medium** | Minor functionality impaired | < 2 hours | Slack |
| **P4 - Low** | Non-urgent issue | Next business day | Ticket |

---

## Application Alerts

### Availability

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| Service Down | Health check fails | 3 consecutive | P1 | PagerDuty |
| High Error Rate | Error rate > 1% | 5 min | P1 | PagerDuty |
| Elevated Error Rate | Error rate > 0.5% | 10 min | P2 | Slack |

### Performance

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| Slow Response (Critical) | p95 > 2000ms | 5 min | P1 | PagerDuty |
| Slow Response (Warning) | p95 > 1000ms | 10 min | P2 | Slack |
| High Latency Variance | p99 > 3x p50 | 15 min | P3 | Slack |

### Throughput

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| Traffic Drop | Requests < 50% baseline | 10 min | P2 | Slack |
| Traffic Spike | Requests > 200% baseline | 5 min | P3 | Slack |

---

## Infrastructure Alerts

### Compute

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| High CPU (Critical) | CPU > 90% | 10 min | P1 | PagerDuty |
| High CPU (Warning) | CPU > 80% | 15 min | P2 | Slack |
| High Memory (Critical) | Memory > 90% | 5 min | P1 | PagerDuty |
| High Memory (Warning) | Memory > 85% | 10 min | P2 | Slack |

### Database

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| DB Connection Failure | Connection errors > 0 | 1 min | P1 | PagerDuty |
| High DB Latency | Query p95 > 500ms | 5 min | P2 | Slack |
| Connection Pool Exhaustion | Available connections < 10% | 5 min | P2 | Slack |
| Disk Space Low | Disk usage > 80% | Immediate | P2 | Slack |

### Network

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| High Network Errors | Error rate > 1% | 5 min | P2 | Slack |
| SSL Certificate Expiring | < 14 days to expiry | Immediate | P2 | Slack |
| SSL Certificate Expired | Expired | Immediate | P1 | PagerDuty |

---

## External Dependency Alerts

| Alert | Dependency | Condition | Severity | Channel |
|-------|------------|-----------|----------|---------|
| {{Dependency}} Down | {{Service}} | Errors > 50% | P2 | Slack |
| {{Dependency}} Slow | {{Service}} | Latency > 2x normal | P3 | Slack |

---

## Business Metric Alerts

| Alert | Condition | Duration | Severity | Channel |
|-------|-----------|----------|----------|---------|
| {{Metric}} Drop | {{condition}} | {{duration}} | P2 | Slack |

---

## Cost Alerts

See COST-THRESHOLDS.md for detailed cost alerting.

| Alert | Condition | Severity | Channel |
|-------|-----------|----------|---------|
| Cost Anomaly | > 2x normal daily | P2 | Slack |
| Budget Warning | > 80% monthly | P2 | Slack + Email |
| Budget Critical | > 100% monthly | P1 | Slack + Email |

---

## Alert Configuration

### PagerDuty Configuration

| Service | Escalation Policy | Integration Key |
|---------|-------------------|-----------------|
| {{Application}} | {{Policy Name}} | {{Key Location}} |

### Slack Configuration

| Channel | Purpose | Alerts Sent |
|---------|---------|-------------|
| #alerts-critical | P1 alerts | Service Down, High Error Rate |
| #alerts-warning | P2/P3 alerts | Performance, Infrastructure |
| #alerts-info | P4 alerts | Non-urgent notifications |

---

## Runbook Links

Each alert should link to the relevant runbook section:

| Alert | Runbook Section |
|-------|-----------------|
| Service Down | RUNBOOK.md#issue-application-not-responding |
| High Error Rate | RUNBOOK.md#issue-high-error-rate |
| Slow Response | RUNBOOK.md#issue-high-latency |
| High CPU/Memory | RUNBOOK.md#issue-out-of-memory |

---

## Alert Testing

| Alert | Last Tested | Tested By | Result |
|-------|-------------|-----------|--------|
| Service Down | | | ☐ Pass |
| High Error Rate | | | ☐ Pass |
| High CPU | | | ☐ Pass |

---

## Maintenance Windows

| Window | Schedule | Alerts Suppressed |
|--------|----------|-------------------|
| Planned maintenance | {{Schedule}} | All non-critical |

---

## Alert Ownership

| Alert Category | Owner | Backup |
|----------------|-------|--------|
| Application | {{Name}} | {{Name}} |
| Infrastructure | {{Name}} | {{Name}} |
| Database | {{Name}} | {{Name}} |

---

## Verification Checklist

| Check | Status |
|-------|--------|
| All P1 alerts configured | ☐ |
| All alerts have runbook links | ☐ |
| Alert channels verified | ☐ |
| Escalation policies tested | ☐ |
| On-call schedule current | ☐ |

---

## Implementation Notes for 0i

These thresholds are defined in 0h and must be implemented in 0i:

1. Configure monitoring dashboards
2. Set up alert rules per this document
3. Verify PagerDuty/Slack integration
4. Test alert routing
5. Confirm runbook links work

---
Generated: {{timestamp}}
Phase: 63 - Performance & Security
Template: ALERTING-THRESHOLDS-TEMPLATE.md v1.0
---
