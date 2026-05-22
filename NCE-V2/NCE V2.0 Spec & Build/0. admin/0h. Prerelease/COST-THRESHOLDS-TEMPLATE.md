# Cost Thresholds

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
---

## Overview

This document defines cloud spend limits and billing alerts for the application.

| Field | Value |
|-------|-------|
| Application | {{APPLICATION_NAME}} |
| Cloud Provider | {{AWS | GCP | Azure | Cloudflare | Vercel}} |
| Billing Account | {{Account ID}} |
| Budget Owner | {{NAME}} |
| Fiscal Period | {{Monthly | Quarterly}} |

---

## Budget Summary

| Category | Monthly Budget | Current Spend | Status |
|----------|----------------|---------------|--------|
| Compute | ${{N}} | ${{N}} | ☐ On track |
| Database | ${{N}} | ${{N}} | ☐ On track |
| Storage | ${{N}} | ${{N}} | ☐ On track |
| Network/CDN | ${{N}} | ${{N}} | ☐ On track |
| Third-party services | ${{N}} | ${{N}} | ☐ On track |
| **Total** | **${{N}}** | **${{N}}** | ☐ On track |

---

## Cost Alerts

### Budget Percentage Alerts

| Threshold | Alert Type | Channel | Action |
|-----------|------------|---------|--------|
| 50% of monthly budget | Warning | Slack | Review spend trends |
| 80% of monthly budget | Critical | Slack + Email | Evaluate cost reduction |
| 100% of monthly budget | Emergency | Slack + Email + PagerDuty | Immediate escalation |

### Burn Rate Alerts

| Metric | Threshold | Alert Type | Channel |
|--------|-----------|------------|---------|
| Daily burn rate | > ${{N}} / day | Warning | Slack |
| Hourly spike | > 2x average hourly | Warning | Slack |

### Anomaly Detection

| Metric | Condition | Alert Type | Channel |
|--------|-----------|------------|---------|
| Cost anomaly | > 2x normal daily spend | Warning | Slack |
| Unexpected service | New service appears in billing | Info | Slack |

---

## Service-Level Budgets

### Compute ({{EC2 | Cloud Run | etc.}})

| Resource | Instances | Unit Cost | Monthly Budget |
|----------|-----------|-----------|----------------|
| {{Instance Type}} | {{N}} | ${{N}}/hr | ${{N}} |
| {{Instance Type}} | {{N}} | ${{N}}/hr | ${{N}} |
| **Compute Total** | | | **${{N}}** |

### Database ({{RDS | Cloud SQL | etc.}})

| Resource | Size | Unit Cost | Monthly Budget |
|----------|------|-----------|----------------|
| {{DB Instance}} | {{Size}} | ${{N}}/mo | ${{N}} |
| Backup storage | {{Size}} | ${{N}}/GB | ${{N}} |
| **Database Total** | | | **${{N}}** |

### Storage ({{S3 | GCS | etc.}})

| Resource | Size | Unit Cost | Monthly Budget |
|----------|------|-----------|----------------|
| {{Bucket/Container}} | {{Size}} | ${{N}}/GB | ${{N}} |
| Data transfer | {{Volume}} | ${{N}}/GB | ${{N}} |
| **Storage Total** | | | **${{N}}** |

### Network / CDN

| Resource | Volume | Unit Cost | Monthly Budget |
|----------|--------|-----------|----------------|
| CDN bandwidth | {{Volume}} | ${{N}}/GB | ${{N}} |
| Load balancer | {{N}} | ${{N}}/mo | ${{N}} |
| **Network Total** | | | **${{N}}** |

### Third-Party Services

| Service | Plan | Monthly Cost |
|---------|------|--------------|
| {{Service}} | {{Plan}} | ${{N}} |
| **Third-Party Total** | | **${{N}}** |

---

## Cost Optimization Opportunities

| Opportunity | Potential Savings | Status |
|-------------|-------------------|--------|
| Reserved instances | ${{N}}/mo | ☐ Evaluated |
| Spot instances | ${{N}}/mo | ☐ Evaluated |
| Right-sizing | ${{N}}/mo | ☐ Evaluated |
| Unused resources | ${{N}}/mo | ☐ Evaluated |

---

## Billing Alert Configuration

### Cloud Provider Alerts ({{AWS Budgets | GCP Billing | etc.}})

```
# Example AWS Budget configuration
Budget: {{APPLICATION_NAME}}-monthly
Amount: ${{N}}
Alerts:
  - Threshold: 50%, Type: ACTUAL, Channel: SNS → Slack
  - Threshold: 80%, Type: ACTUAL, Channel: SNS → Slack + Email
  - Threshold: 100%, Type: ACTUAL, Channel: SNS → Slack + Email + PagerDuty
  - Threshold: 100%, Type: FORECASTED, Channel: SNS → Slack
```

### Notification Channels

| Channel | Recipients | Purpose |
|---------|------------|---------|
| Slack #costs | Engineering team | Daily awareness |
| Email | Budget owner, Finance | Budget milestones |
| PagerDuty | On-call | Budget exceeded |

---

## Escalation Policy

| Threshold | Action | Contact |
|-----------|--------|---------|
| 80% budget | Review with Tech Lead | {{Name}} |
| 100% budget | Escalate to Engineering Manager | {{Name}} |
| 120% budget | Escalate to Head of Engineering | {{Name}} |

---

## Cost Tracking

### Daily Review

| Day | Spend | Daily Avg | Projected Monthly | Notes |
|-----|-------|-----------|-------------------|-------|
| | | | | |

### Monthly Summary

| Month | Budget | Actual | Variance | Notes |
|-------|--------|--------|----------|-------|
| | ${{N}} | ${{N}} | {{+/-}}% | |

---

## Verification Checklist

| Check | Status |
|-------|--------|
| Budget alerts configured in cloud provider | ☐ |
| Slack integration working | ☐ |
| Email notifications working | ☐ |
| Cost anomaly detection enabled | ☐ |
| Budget owner notified | ☐ |

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Budget Owner | | | ☐ |
| Finance (if required) | | | ☐ |

---

## Implementation Notes for 0i

These thresholds are defined in 0h and must be implemented in 0i:

1. Configure cloud provider billing alerts
2. Set up cost dashboard
3. Enable anomaly detection
4. Verify notification channels
5. Schedule first cost review

---
Generated: {{timestamp}}
Phase: 63 - Performance & Security
Template: COST-THRESHOLDS-TEMPLATE.md v1.0
---
