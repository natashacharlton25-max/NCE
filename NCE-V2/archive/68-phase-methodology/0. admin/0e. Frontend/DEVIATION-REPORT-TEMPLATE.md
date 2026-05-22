# Deviation Report

---
Report ID: DEV-{{number}}
Date: {{date}}
Phase: 0f Implementation
Reporter: {{name}}
Status: OPEN | UNDER REVIEW | APPROVED | REJECTED
---

## Deviation Summary

**One sentence:** {{What deviation is being requested?}}

---

## Affected Specification

| Field | Value |
|-------|-------|
| Document | {{PAGES.md / COMPONENTS.md / STATE-MANAGEMENT.md / etc.}} |
| Section | {{specific section or component name}} |
| Line/Item | {{specific item if applicable}} |

---

## Current Specification Says

> {{Quote the exact specification text}}

---

## Proposed Change

> {{What should it say instead, or what should be added?}}

---

## Reason for Deviation

| Category | Check |
|----------|-------|
| ☐ Spec is ambiguous | Unclear what was intended |
| ☐ Spec is incorrect | Does not match requirements |
| ☐ Spec is incomplete | Missing necessary detail |
| ☐ Spec is impossible | Cannot be implemented as written |
| ☐ New requirement | Discovered need not in original scope |
| ☐ Performance | Spec approach has unacceptable performance |
| ☐ Security | Spec approach has security implications |
| ☐ Other | {{explain}} |

**Detailed Explanation:**

{{Why is this deviation necessary? What was discovered during implementation?}}

---

## Impact Assessment

### If Approved

| Impact Area | Description |
|-------------|-------------|
| Other components | {{which components are affected}} |
| Other pages | {{which pages are affected}} |
| State management | {{any state changes needed}} |
| API contract | {{any API implications}} |
| Timeline | {{estimated time impact}} |

### If Rejected

{{What happens if we proceed with the original spec? What workarounds exist?}}

---

## Alternatives Considered

| Alternative | Pros | Cons | Why Not Chosen |
|-------------|------|------|----------------|
| {{option 1}} | {{pros}} | {{cons}} | {{reason}} |
| {{option 2}} | {{pros}} | {{cons}} | {{reason}} |
| Proposed approach | {{pros}} | {{cons}} | **Recommended** |

---

## Evidence

{{Screenshots, code snippets, error messages, or other evidence supporting the need for deviation}}

```
{{code or error if applicable}}
```

---

## Resolution Path

| Option | Description | Who Decides |
|--------|-------------|-------------|
| A | Approve deviation, update spec | 0e Owner |
| B | Reject deviation, implement as spec'd | 0e Owner |
| C | Escalate to 0c/0d (breaks contract) | Project Lead |
| D | Defer to future version | Project Lead |

---

## Decision

**Decision:** {{A / B / C / D}}

**Decided By:** {{name}}

**Date:** {{date}}

**Rationale:**

{{Why this decision was made}}

---

## Actions Required

If approved:

- [ ] Update {{document}} section {{section}}
- [ ] Update FRONTEND-DECISION-NOTES.md
- [ ] Notify affected parties: {{who}}
- [ ] Bump document version

If rejected:

- [ ] Document workaround used
- [ ] Note in FRONTEND-DECISION-NOTES.md

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Reporter | | | ☐ |
| 0e Owner | | | ☐ |
| Project Lead (if escalated) | | | ☐ |

---
Generated: {{timestamp}}
Template: DEVIATION-REPORT
---
