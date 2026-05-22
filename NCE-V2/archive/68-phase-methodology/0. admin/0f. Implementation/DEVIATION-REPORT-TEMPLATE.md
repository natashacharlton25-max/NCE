# Deviation Report

---
ID: DEV-{{number}}
Project: {{project_name}}
Phase: {{phase_number}}
Filed: {{date}}
Status: OPEN | UNDER REVIEW | APPROVED | REJECTED | BLOCKED
Severity: BLOCKING | NON-BLOCKING
---

## Severity Classification

| Severity | Definition | Action Required |
|----------|------------|-----------------|
| **BLOCKING** | Cannot proceed with current phase | Must resolve before continuing |
| **NON-BLOCKING** | Can proceed with workaround | May continue if explicitly approved |

**This deviation is:** ☐ Blocking / ☐ Non-Blocking

**Blocking examples:** Missing required field, impossible to implement, security issue
**Non-Blocking examples:** Performance target not met, minor UX improvement, optional feature deferred

---

## Summary

{{One-sentence description of the deviation}}

---

## What Spec Says

**Document:** {{spec_document_name}}
**Section:** {{section_name}}
**Location:** {{file_path}}

**Specification:**

```
{{Quote the exact specification text}}
```

---

## What Was Implemented (or Proposed)

**Description:**

{{Describe what was actually implemented or what you propose to implement}}

**Code/Structure:**

```
{{Show the implementation or proposed structure}}
```

---

## Why Deviation Is Necessary

### Root Cause

{{Explain why the spec cannot be followed exactly}}

Options:
- [ ] Spec is ambiguous
- [ ] Spec is impossible to implement
- [ ] Spec conflicts with another spec
- [ ] Spec has incorrect assumptions
- [ ] Technical limitation discovered
- [ ] Performance requirement cannot be met
- [ ] External dependency constraint
- [ ] Other: {{explain}}

### Detailed Explanation

{{Provide full context for why this deviation is needed}}

---

## Impact Assessment

### Affected Areas

| Area | Impact | Details |
|------|--------|---------|
| Backend | None / Low / Medium / High | {{details}} |
| Frontend | None / Low / Medium / High | {{details}} |
| Database | None / Low / Medium / High | {{details}} |
| API Contract | None / Low / Medium / High | {{details}} |
| Other components | None / Low / Medium / High | {{details}} |

### Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| {{risk_1}} | Low / Medium / High | Low / Medium / High | {{mitigation}} |

### Breaking Changes

- [ ] This deviation introduces breaking changes
- [ ] This deviation is backward compatible

If breaking:
{{List what breaks and how to handle it}}

---

## Alternatives Considered

### Alternative 1: {{name}}

**Description:** {{what this alternative would do}}

**Pros:**
- {{pro_1}}
- {{pro_2}}

**Cons:**
- {{con_1}}
- {{con_2}}

**Why Not Chosen:** {{reason}}

### Alternative 2: {{name}}

**Description:** {{what this alternative would do}}

**Pros:**
- {{pro_1}}

**Cons:**
- {{con_1}}

**Why Not Chosen:** {{reason}}

---

## Recommended Action

### Recommendation

- [ ] **Approve deviation** — Implement as proposed
- [ ] **Modify spec** — Update {{spec_document}} to reflect reality
- [ ] **Modify implementation** — Change approach to match spec
- [ ] **Escalate** — Needs decision from {{role}}

### If Approved

**Spec documents to update:**
- [ ] {{document_1}} — {{what to change}}
- [ ] {{document_2}} — {{what to change}}

**Implementation to complete:**
- [ ] {{implementation_task_1}}
- [ ] {{implementation_task_2}}

---

## Resolution

### Decision

**Decision:** APPROVED / REJECTED / MODIFIED
**Decided By:** {{name}}
**Date:** {{date}}

**Rationale:**

{{Explain the decision}}

### Actions Taken

- [ ] Spec updated: {{document}} v{{old_version}} → v{{new_version}}
- [ ] Implementation completed
- [ ] Tests updated
- [ ] Documentation updated

### Final Notes

{{Any additional context for future reference}}

---

## Audit Trail

| Date | Action | By |
|------|--------|-----|
| {{date}} | Filed | {{name}} |
| {{date}} | Reviewed | {{name}} |
| {{date}} | {{action}} | {{name}} |

---
Template: DEVIATION-REPORT-TEMPLATE.md
Section: 0f. Implementation
---
