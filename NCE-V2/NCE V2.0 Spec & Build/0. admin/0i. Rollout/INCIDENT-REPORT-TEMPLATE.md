# Incident Report

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
Incident ID: {{INC-YYYY-MM-DD-NNN}}
---

## Incident Summary

| Field | Value |
|-------|-------|
| Incident ID | {{INC-YYYY-MM-DD-NNN}} |
| Severity | P1 / P2 / P3 / P4 |
| Status | Open / Resolved / Post-mortem Complete |
| RC Tag | {{RC_TAG}} |
| Phase when discovered | 65 / 66 / 67 / 68 |
| Incident Commander | {{NAME}} |

---

## Timeline

| Time | Event |
|------|-------|
| {{TIMESTAMP}} | Incident detected |
| {{TIMESTAMP}} | Incident acknowledged |
| {{TIMESTAMP}} | Evidence capture started |
| {{TIMESTAMP}} | Evidence capture completed |
| {{TIMESTAMP}} | Rollback initiated |
| {{TIMESTAMP}} | Rollback completed |
| {{TIMESTAMP}} | Service restored |
| {{TIMESTAMP}} | Incident resolved |

**Time to Detect (TTD):** {{DURATION}}
**Time to Acknowledge (TTA):** {{DURATION}}
**Time to Mitigate (TTM):** {{DURATION}}
**Time to Resolve (TTR):** {{DURATION}}

---

## Impact

### User Impact

| Metric | Value |
|--------|-------|
| Users affected | {{N}} / {{%}} |
| Duration of impact | {{DURATION}} |
| Geographic scope | {{REGIONS}} |
| Features affected | {{LIST}} |

### Business Impact

| Metric | Value |
|--------|-------|
| Revenue impact | {{$X}} / Unknown |
| SLA breach | ☐ Yes / ☐ No |
| Customer complaints | {{N}} |
| Support tickets | {{N}} |

### Technical Impact

| Metric | Value |
|--------|-------|
| Error rate peak | {{%}} |
| p95 latency peak | {{ms}} |
| Failed requests | {{N}} |
| Data affected | {{DESCRIPTION}} / None |

---

## Detection

| Field | Value |
|-------|-------|
| How detected | Alert / User report / Monitoring / Manual check |
| Alert name | {{ALERT_NAME}} |
| First indicator | {{DESCRIPTION}} |
| Detection method | {{TOOL/PROCESS}} |

---

## Evidence Captured

### Before Rollback

| Evidence Type | Captured | Location | Notes |
|---------------|----------|----------|-------|
| Application logs (30 min) | ☐ | {{LOCATION}} | |
| Heap dump | ☐ / N/A | {{LOCATION}} | |
| Thread dump | ☐ / N/A | {{LOCATION}} | |
| Metrics snapshot | ☐ | {{LOCATION}} | |
| Error samples | ☐ | {{LOCATION}} | |
| Network traces | ☐ | {{LOCATION}} | |

**Evidence Bucket:** `s3://{{BUCKET}}/incidents/{{DATE}}/`

---

## Root Cause Analysis

### What Happened

```
{{Detailed description of what happened}}
```

### Why It Happened

```
{{Root cause analysis - the "5 Whys" or similar}}
```

### Contributing Factors

| Factor | Description |
|--------|-------------|
| {{FACTOR_1}} | {{DESCRIPTION}} |
| {{FACTOR_2}} | {{DESCRIPTION}} |

---

## Resolution

### Immediate Actions Taken

| Action | Time | By |
|--------|------|-----|
| Rollback initiated | {{TIME}} | {{NAME}} |
| {{ACTION}} | {{TIME}} | {{NAME}} |

### Rollback Details

| Field | Value |
|-------|-------|
| Rollback method | {{METHOD}} |
| Previous version | {{VERSION}} |
| Rollback duration | {{DURATION}} |
| Data integrity verified | ☐ |

### Service Restoration

| Check | Status | Time |
|-------|--------|------|
| Previous version running | ☐ | |
| Health checks passing | ☐ | |
| Error rate normalized | ☐ | |
| User traffic restored | ☐ | |

---

## Return Path

| Field | Value |
|-------|-------|
| Return to section | 0f / 0g / 0h |
| Return to phase | {{PHASE_NUMBER}} |
| New RC required | ☐ Yes / ☐ No |
| Fix branch | {{BRANCH_NAME}} |

---

## Preventive Measures

### Immediate Actions

| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| {{ACTION}} | {{NAME}} | {{DATE}} | ☐ |

### Long-term Improvements

| Improvement | Owner | Target Date | Status |
|-------------|-------|-------------|--------|
| {{IMPROVEMENT}} | {{NAME}} | {{DATE}} | ☐ |

---

## Process Improvements

### What Worked Well

1. {{ITEM}}
2. {{ITEM}}

### What Could Be Improved

1. {{ITEM}}
2. {{ITEM}}

### Recommendations for 0h/0i Process

| Recommendation | Rationale |
|----------------|-----------|
| {{RECOMMENDATION}} | {{RATIONALE}} |

---

## Communication Log

| Time | Channel | Message | Audience |
|------|---------|---------|----------|
| {{TIME}} | {{CHANNEL}} | Incident detected | Team |
| {{TIME}} | {{CHANNEL}} | Rollback in progress | Stakeholders |
| {{TIME}} | {{CHANNEL}} | Service restored | All |
| {{TIME}} | {{CHANNEL}} | Post-mortem scheduled | Team |

---

## Post-Mortem

| Field | Value |
|-------|-------|
| Post-mortem scheduled | {{DATE}} |
| Post-mortem completed | ☐ |
| Attendees | {{NAMES}} |
| Action items assigned | ☐ |

---

## Attachments

| Attachment | Description | Location |
|------------|-------------|----------|
| Error logs | Relevant log excerpts | {{LINK}} |
| Metrics graphs | Impact visualization | {{LINK}} |
| Screenshots | UI/dashboard captures | {{LINK}} |

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Incident Commander | | | ☐ |
| On-call Engineer | | | ☐ |
| Release Owner | | | ☐ |

---

## Status Updates

| Date | Status | Update |
|------|--------|--------|
| {{DATE}} | Open | Incident created |
| {{DATE}} | Resolved | Service restored |
| {{DATE}} | Complete | Post-mortem done |

---
Generated: {{timestamp}}
Phase: Any (Incident during 0i)
Template: INCIDENT-REPORT-TEMPLATE.md v1.0
---
