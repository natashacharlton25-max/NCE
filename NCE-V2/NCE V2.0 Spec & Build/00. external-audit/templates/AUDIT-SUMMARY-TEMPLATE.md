# External Audit Summary

---
Project: {{project_name}}
Section Audited: {{section}} (Phases {{start}}-{{end}})
Audit Date: {{date}}
Audit Mode: FULL | FAST | LIGHT | CONSISTENCY
Auditor: {{AI_name}} / {{version}}
---

## Audit Overview

| Field | Value |
|-------|-------|
| Section | {{section_name}} |
| Phases Covered | {{phases}} |
| Audit Requested By | {{human_name}} |
| Audit Mode | {{mode}} |
| Files Requested | {{count}} |
| Rounds Used | {{rounds}} / 2 |

---

## Executive Summary

{{2-3 sentence summary of audit findings and overall assessment}}

---

## Scores

### Standard Mode (6 Categories)

| Category | Score (1-5) | Notes |
|----------|-------------|-------|
| Completeness | | {{Are all required items present?}} |
| Consistency | | {{Do items match across files?}} |
| Accuracy | | {{Is content correct?}} |
| Methodology | | {{Does it follow Fullspecs rules?}} |
| Quality | | {{Is it well-written and clear?}} |
| Readiness | | {{Ready for next section?}} |
| **Overall** | | |

### Consistency Delta (vs Previous Section)

| Score (1-5) | Notes |
|-------------|-------|
| | {{How well does this align with previous section outputs?}} |

---

## Verdict

### ✅ PASS | ⚠️ CONDITIONAL PASS | ❌ FAIL

**Rationale:** {{Why this verdict}}

---

## Findings

### 🔴 Critical (Must Fix Before Proceeding)

| # | Finding | Location | Impact | Recommendation |
|---|---------|----------|--------|----------------|
| 1 | | | | |

{{Or: "None"}}

### 🟠 Important (Should Fix Before Proceeding)

| # | Finding | Location | Impact | Recommendation |
|---|---------|----------|--------|----------------|
| 1 | | | | |

{{Or: "None"}}

### 🟡 Minor (Fix If Time Permits)

| # | Finding | Location | Recommendation |
|---|---------|----------|----------------|
| 1 | | | |

{{Or: "None"}}

### 🔵 Suggestions (Optional Improvements)

| # | Suggestion | Rationale |
|---|------------|-----------|
| 1 | | |

{{Or: "None"}}

---

## Files Reviewed

### Requested in Round 1
| File | Reason Requested |
|------|------------------|
| | |

### Requested in Round 2
| File | Reason Requested |
|------|------------------|
| | |

{{Or: "No Round 2 needed"}}

---

## Checklist Verification

Based on section checklist ({{checklist_filename}}):

| Item | Status | Notes |
|------|--------|-------|
| {{checklist_item}} | ✅ / ❌ / ⚠️ | |

---

## Claude Self-Assessment Comparison

| Category | Claude's Score | Auditor's Score | Delta |
|----------|----------------|-----------------|-------|
| Completeness | | | |
| Consistency | | | |
| Accuracy | | | |
| Methodology | | | |
| Quality | | | |
| Readiness | | | |

**Significant Discrepancies:** {{Note any areas where Claude and auditor disagree by >1 point}}

---

## Resolution Tracking

| Finding # | Severity | Resolution Type | Status |
|-----------|----------|-----------------|--------|
| | | Fixed / Accepted / Deferred / Disputed | |

### Resolution Types
- **Fixed**: Issue was corrected
- **Accepted**: Known risk, consciously not fixing
- **Deferred**: Will fix in future section/version
- **Disputed**: Disagree it's an issue (with rationale)

---

## Recommendations for Next Section

1. {{Recommendation}}
2. {{Recommendation}}

---

## Auditor Notes

{{Any additional observations, context, or caveats}}

---

## Sign-Off

| Role | Name | Date | Approved |
|------|------|------|----------|
| Auditor (AI) | {{AI_name}} | {{date}} | ✅ |
| Human Reviewer | | | [ ] |

---

## Audit Trail

| Event | Timestamp | Actor |
|-------|-----------|-------|
| Audit requested | | Human |
| Handoff received | | Human |
| Round 1 complete | | Auditor |
| Round 2 complete | | Auditor |
| Report delivered | | Auditor |
| Findings resolved | | Claude |
| Audit closed | | Human |

---
Template Version: 1.0.0
Audit System Version: 4.0.0
---
