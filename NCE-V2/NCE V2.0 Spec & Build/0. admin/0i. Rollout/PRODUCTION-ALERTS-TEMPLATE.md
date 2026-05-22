# Production Alerts Configuration

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
---

## Overview

This document records the alert configuration implemented in production based on ALERTING-THRESHOLDS.md from 0h.

| Field | Value |
|-------|-------|
| Monitoring Platform | {{Datadog | New Relic | Prometheus | CloudWatch}} |
| Alerting Platform | {{PagerDuty | Opsgenie | VictorOps}} |
| Configuration Date | {{DATE}} |
| Configured By | {{NAME}} |

---

## Alert Configuration Status

### Application Alerts

| Alert | Condition | Configured | Tested | Routing Verified |
|-------|-----------|------------|--------|------------------|
| Service Down | Health check fails 3x | ☐ | ☐ | ☐ |
| High Error Rate (P1) | Error rate > 1% for 5 min | ☐ | ☐ | ☐ |
| Elevated Error Rate (P2) | Error rate > 0.5% for 10 min | ☐ | ☐ | ☐ |
| Slow Response (P1) | p95 > 2s for 5 min | ☐ | ☐ | ☐ |
| Slow Response (P2) | p95 > 1s for 10 min | ☐ | ☐ | ☐ |

### Infrastructure Alerts

| Alert | Condition | Configured | Tested | Routing Verified |
|-------|-----------|------------|--------|------------------|
| High CPU (P1) | CPU > 90% for 10 min | ☐ | ☐ | ☐ |
| High CPU (P2) | CPU > 80% for 15 min | ☐ | ☐ | ☐ |
| High Memory (P1) | Memory > 90% for 5 min | ☐ | ☐ | ☐ |
| High Memory (P2) | Memory > 85% for 10 min | ☐ | ☐ | ☐ |
| Disk Space Low | Disk > 80% | ☐ | ☐ | ☐ |

### Database Alerts

| Alert | Condition | Configured | Tested | Routing Verified |
|-------|-----------|------------|--------|------------------|
| DB Connection Failure | Connection errors > 0 | ☐ | ☐ | ☐ |
| High DB Latency | Query p95 > 500ms for 5 min | ☐ | ☐ | ☐ |
| Connection Pool Exhaustion | Available < 10% | ☐ | ☐ | ☐ |

### Cost Alerts

| Alert | Condition | Configured | Tested | Routing Verified |
|-------|-----------|------------|--------|------------------|
| Daily Burn Rate | > ${{X}}/day | ☐ | ☐ | ☐ |
| Budget Warning (50%) | > 50% monthly | ☐ | ☐ | ☐ |
| Budget Critical (80%) | > 80% monthly | ☐ | ☐ | ☐ |
| Cost Anomaly | > 2× normal daily | ☐ | ☐ | ☐ |

---

## Alert Routing Configuration

### Channel Configuration

| Channel | Platform | Purpose | Verified |
|---------|----------|---------|----------|
| #alerts-critical | Slack | P1 alerts | ☐ |
| #alerts-warning | Slack | P2/P3 alerts | ☐ |
| {{SERVICE}} | PagerDuty | P1 pages | ☐ |
| {{EMAIL}} | Email | Budget alerts | ☐ |

### Escalation Policy

| Service | Escalation Policy | Integration Key Location | Verified |
|---------|-------------------|--------------------------|----------|
| {{Application}} | {{Policy Name}} | {{Location}} | ☐ |

---

## Alert Ownership

| Alert Category | Owner | Backup | Contact |
|----------------|-------|--------|---------|
| Application | {{NAME}} | {{NAME}} | {{EMAIL}} |
| Infrastructure | {{NAME}} | {{NAME}} | {{EMAIL}} |
| Database | {{NAME}} | {{NAME}} | {{EMAIL}} |
| Cost | {{NAME}} | {{NAME}} | {{EMAIL}} |
| Security | {{NAME}} | {{NAME}} | {{EMAIL}} |

---

## Runbook Links

Each alert links to the relevant runbook section:

| Alert | Runbook Section | Link Verified |
|-------|-----------------|---------------|
| Service Down | RUNBOOK.md#service-down | ☐ |
| High Error Rate | RUNBOOK.md#high-error-rate | ☐ |
| Slow Response | RUNBOOK.md#high-latency | ☐ |
| High CPU/Memory | RUNBOOK.md#resource-exhaustion | ☐ |
| Database Issues | RUNBOOK.md#database-issues | ☐ |

---

## Alert Testing Results

| Alert | Test Method | Test Date | Result | Tester |
|-------|-------------|-----------|--------|--------|
| Service Down | Manual trigger | | ☐ Pass | |
| High Error Rate | Threshold test | | ☐ Pass | |
| High CPU | Load test | | ☐ Pass | |
| PagerDuty routing | Test alert | | ☐ Pass | |
| Slack routing | Test alert | | ☐ Pass | |

---

## Dashboard Configuration

| Dashboard | Purpose | URL | Verified |
|-----------|---------|-----|----------|
| Overview | High-level metrics | {{URL}} | ☐ |
| Application | App-specific metrics | {{URL}} | ☐ |
| Infrastructure | Resource utilization | {{URL}} | ☐ |
| Database | DB performance | {{URL}} | ☐ |
| Cost | Spend tracking | {{URL}} | ☐ |

---

## Monitoring Gaps

| Gap | Impact | Remediation Plan | Target Date |
|-----|--------|------------------|-------------|
| None identified | - | - | - |

---

## Configuration Checklist

| Check | Status |
|-------|--------|
| All P1 alerts configured | ☐ |
| All P2 alerts configured | ☐ |
| All alerts have runbook links | ☐ |
| Alert channels verified | ☐ |
| Escalation policies tested | ☐ |
| On-call schedule current | ☐ |
| Dashboard links working | ☐ |
| Alert ownership assigned | ☐ |

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Deployer | | | ☐ Alerts configured |
| On-call Engineer | | | ☐ Routing verified |
| SRE/Ops | | | ☐ Ownership confirmed |

---
Generated: {{timestamp}}
Phase: 68 - Stabilization & Handoff
Template: PRODUCTION-ALERTS-TEMPLATE.md v1.0
---
