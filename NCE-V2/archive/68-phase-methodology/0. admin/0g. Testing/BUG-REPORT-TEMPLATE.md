# BUG-REPORT Template

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Document bugs discovered during testing
Create: One file per bug in BUG-REPORTS/ folder
---

# Bug Report: {{BUG-ID}}

## Summary

| Field | Value |
|-------|-------|
| Bug ID | BUG-{{number}} |
| Title | {{short_title}} |
| Severity | {{CRITICAL / HIGH / MEDIUM / LOW}} |
| Status | {{OPEN / IN_PROGRESS / RESOLVED / WONT_FIX}} |
| Discovered | Phase {{phase}}, {{date}} |
| Reporter | {{name}} |

---

## Classification

### Severity Guide

| Severity | Definition | SLA |
|----------|------------|-----|
| CRITICAL | App crashes, data loss, security issue | Block release |
| HIGH | Feature broken, unusable | Fix before release |
| MEDIUM | Feature impaired, workaround exists | Fix or document |
| LOW | Minor issue, cosmetic | Fix if time |

### Type

- [ ] Bug (implementation doesn't match spec)
- [ ] Spec Issue (spec needs clarification/change)
- [ ] Test Issue (test is wrong, not code)

---

## Specification Reference

| Field | Value |
|-------|-------|
| Spec Document | {{document_name}}.md |
| Section | {{section}} |
| Expected Behaviour | {{what_spec_says}} |

**Spec Quote:**
> {{exact quote from spec}}

---

## Bug Details

### Description

{{Clear description of the bug}}

### Steps to Reproduce

1. {{step_1}}
2. {{step_2}}
3. {{step_3}}
4. Observe: {{what_happens}}

### Expected Behaviour

{{What should happen according to spec}}

### Actual Behaviour

{{What actually happens}}

### Evidence

**Screenshot/Recording:**
{{attach or link}}

**Error Message:**
```
{{error output if any}}
```

**Test Output:**
```
{{failing test output}}
```

---

## Environment

| Field | Value |
|-------|-------|
| Environment | {{test / dev / staging}} |
| Browser | {{if applicable}} |
| OS | {{if applicable}} |
| Commit | {{git_sha}} |

---

## Impact

### Affected Areas

| Area | Impact |
|------|--------|
| {{feature}} | {{how_affected}} |

### Workaround

- [ ] Workaround exists
- [ ] No workaround

**Workaround details:**
{{if exists, describe}}

---

## Related

### Related Tests

| Test File | Test Name | Status |
|-----------|-----------|--------|
| {{file}} | {{test}} | FAILING |

### Related Bugs

| Bug ID | Relationship |
|--------|--------------|
| BUG-{{n}} | {{related_to / duplicate_of / blocked_by}} |

---

## Resolution

### Assigned To

| Role | Name | Date |
|------|------|------|
| Fix Owner | {{name}} | {{date}} |

### Root Cause

{{After investigation, what caused this bug}}

### Fix Description

{{How it was fixed}}

### Fix Verification

| Check | Status |
|-------|--------|
| Fix implemented | ☐ |
| Phase 53 re-passed | ☐ |
| Test now passes | ☐ |
| No regression | ☐ |

---

## Timeline

| Date | Action | By |
|------|--------|-----|
| {{date}} | Bug discovered | {{name}} |
| {{date}} | Assigned to 0f | {{name}} |
| {{date}} | Fix implemented | {{name}} |
| {{date}} | Verified fixed | {{name}} |

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Reporter | | | ☐ Bug Documented |
| Fixer | | | ☐ Fix Complete |
| Verifier | | | ☐ Fix Verified |

---

## Notes

{{Any additional context or notes}}

---
Generated: {{timestamp}}
Phase: 0g Testing
---
