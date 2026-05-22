# Performance Impact Analysis

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag (Failed): {{RC_TAG_OLD}}
RC Tag (New): {{RC_TAG_NEW}}
---

## Overview

This document is required when Phase 63 (Performance & Security) fails due to performance issues and the fix requires returning to 0f.

**Purpose:** Prove that the proposed fix resolves the performance issue without shifting the bottleneck elsewhere.

| Field | Value |
|-------|-------|
| Issue Discovered | {{DATE}} |
| Issue Description | {{Brief description}} |
| Failed RC | {{RC_TAG_OLD}} |
| Fix Branch | {{Branch name}} |
| New RC | {{RC_TAG_NEW}} |

---

## Performance Issue Summary

### What Failed

| Metric | Target | Actual (Failed RC) | Gap |
|--------|--------|-------------------|-----|
| {{Metric}} | {{Target}} | {{Actual}} | {{%}} |

### Where It Failed

| Component | Endpoint/Function | Issue |
|-----------|-------------------|-------|
| {{Component}} | {{Endpoint}} | {{Description}} |

### Root Cause

```
{{Detailed root cause analysis}}
```

---

## Proposed Fix

### Description

```
{{Description of the fix}}
```

### Code Changes

| File | Change Type | Description |
|------|-------------|-------------|
| {{file}} | {{Modified/Added/Removed}} | {{What changed}} |

### Commit Reference

| Field | Value |
|-------|-------|
| Branch | {{branch}} |
| Commit SHA | {{sha}} |
| PR | {{PR link}} |

---

## Impact Analysis

### Performance Improvement

| Metric | Before Fix | After Fix | Improvement |
|--------|------------|-----------|-------------|
| {{Metric}} | {{Value}} | {{Value}} | {{%}} |

### No Bottleneck Shift Verification

**Critical:** The fix must not move the bottleneck elsewhere.

| Potential Shift Area | Before Fix | After Fix | Status |
|---------------------|------------|-----------|--------|
| Database queries | {{ms}} | {{ms}} | ☐ No regression |
| Memory usage | {{MB}} | {{MB}} | ☐ No regression |
| CPU usage | {{%}} | {{%}} | ☐ No regression |
| Network latency | {{ms}} | {{ms}} | ☐ No regression |
| External API calls | {{ms}} | {{ms}} | ☐ No regression |

### Load Test Comparison

| Scenario | Before Fix | After Fix | Status |
|----------|------------|-----------|--------|
| Normal load | {{result}} | {{result}} | ☐ Pass |
| Peak load (2x) | {{result}} | {{result}} | ☐ Pass |
| Sustained load (1h) | {{result}} | {{result}} | ☐ Pass |

---

## Tests Affected

### 0g Tests Re-Run

| Test Suite | Result | Notes |
|------------|--------|-------|
| Unit tests | ☐ Pass / ☐ Fail | |
| Integration tests | ☐ Pass / ☐ Fail | |
| E2E tests | ☐ Pass / ☐ Fail | |
| Performance tests | ☐ Pass / ☐ Fail | |

### New Tests Added

| Test | Purpose |
|------|---------|
| {{Test name}} | {{Prevents regression of this fix}} |

---

## Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| {{Risk}} | {{Low/Medium/High}} | {{Low/Medium/High}} | {{Mitigation}} |

---

## Verification Results

### Target Metrics Met

| Metric | Target | New RC Actual | Status |
|--------|--------|---------------|--------|
| LCP | < 2.5s | | ☐ Pass |
| API p95 | < 500ms | | ☐ Pass |
| {{Other}} | {{Target}} | | ☐ Pass |

### No Side Effects

| Check | Status |
|-------|--------|
| Other endpoints unaffected | ☐ |
| Memory usage stable | ☐ |
| CPU usage stable | ☐ |
| Error rate unchanged | ☐ |
| No new warnings in logs | ☐ |

---

## Rollback Plan

If the fix causes issues in production:

| Step | Action |
|------|--------|
| 1 | Revert to previous working version |
| 2 | {{Additional rollback steps}} |

**Rollback Target:** {{Previous working version}}

---

## Approval

### Developer Certification

I certify that:
- [ ] The fix resolves the identified performance issue
- [ ] No bottleneck has been shifted elsewhere
- [ ] All affected 0g tests have been re-run and pass
- [ ] The fix has been tested under load

**Developer:** {{NAME}}
**Date:** {{DATE}}

### Tech Lead Review

| Check | Status |
|-------|--------|
| Root cause correctly identified | ☐ |
| Fix is appropriate | ☐ |
| No bottleneck shift | ☐ |
| Tests adequate | ☐ |

**Tech Lead:** {{NAME}}
**Date:** {{DATE}}
**Approved:** ☐ Yes / ☐ No

---

## Next Steps

1. [ ] Tech Lead approves this analysis
2. [ ] Create new RC tag ({{RC_TAG_NEW}})
3. [ ] Restart 0h from Phase 61
4. [ ] Re-run Phase 63 with new RC

---
Generated: {{timestamp}}
Phase: 63 - Performance & Security (Rework)
Template: PERFORMANCE-IMPACT-ANALYSIS-TEMPLATE.md v1.0
---
