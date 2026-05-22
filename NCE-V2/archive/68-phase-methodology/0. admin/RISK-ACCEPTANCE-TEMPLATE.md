# Risk Acceptance Template

---
**Purpose:** Document when human owner overrides or accepts audit findings
**Authority:** Human owner has final decision authority
**Legal Note:** This document creates an audit trail of informed decisions
---

## When to Use This Template

Use this when:
- An audit finding is acknowledged but not fully addressed
- You're proceeding despite a warning or recommendation
- Claude and external auditor disagree and you're choosing a path
- Time/budget constraints prevent ideal resolution
- You're accepting technical debt intentionally

---

## Risk Acceptance Record

### Project Information

| Field | Value |
|-------|-------|
| **Project Name** | {{project_name}} |
| **Date** | {{date}} |
| **Section/Phase** | {{section}} / Phase {{phase_number}} |
| **Decision Owner** | {{human_owner_name}} |

---

### Finding Details

**Source:** ☐ Claude ☐ External Audit ☐ Self-Identified ☐ Other: _______

**Finding ID:** {{finding_id_if_applicable}}

**Severity as Reported:**
☐ Critical ☐ Important ☐ Minor ☐ Suggestion

**Summary of Finding:**
```
{{paste_or_describe_the_finding}}
```

---

### Decision

**Action Taken:**

☐ **ACCEPTED** — Proceeding without change, risk acknowledged
☐ **MITIGATED** — Partial fix applied, residual risk accepted
☐ **DEFERRED** — Will address post-launch / in future phase
☐ **REJECTED** — Finding is invalid or not applicable (explain below)
☐ **ESCALATED** — Needs higher authority / more expertise

---

### Rationale

**Why this decision:**
```
{{explain_your_reasoning}}
```

**Constraints affecting decision:**
☐ Time ☐ Budget ☐ Technical limitation ☐ Scope ☐ Other: _______

**What would change this decision:**
```
{{conditions_that_would_require_revisiting}}
```

---

### Risk Assessment

**If this finding is not addressed, what could happen?**
```
{{describe_potential_consequences}}
```

**Likelihood:** ☐ High ☐ Medium ☐ Low ☐ Unknown

**Impact:** ☐ High ☐ Medium ☐ Low ☐ Unknown

**Affected areas:**
☐ Security ☐ Performance ☐ Data integrity ☐ User experience
☐ Maintainability ☐ Compliance ☐ Other: _______

---

### Mitigation (if applicable)

**Partial measures taken:**
```
{{what_was_done_to_reduce_risk}}
```

**Monitoring plan:**
```
{{how_will_you_detect_if_this_becomes_a_problem}}
```

**Remediation trigger:**
```
{{what_conditions_would_trigger_a_fix}}
```

---

### Sign-Off

**I acknowledge that:**
- I have read and understood the finding
- I have authority to make this decision
- I accept responsibility for the consequences
- This decision is documented for future reference

**Decision Owner:** _________________________ **Date:** ___________

**Witness (optional):** _________________________ **Date:** ___________

---

### Follow-Up

**Review date (if deferred):** {{date_or_NA}}

**Linked to backlog item:** {{ticket_number_or_NA}}

**Post-launch check required:** ☐ Yes ☐ No

---

## Quick Decision Matrix

| Severity | Typical Action | Requires Sign-Off |
|----------|---------------|-------------------|
| Critical | Fix before proceeding | Yes, always |
| Important | Fix or document mitigation | Yes |
| Minor | Fix, accept, or defer | Recommended |
| Suggestion | Optional | No |

---

## Examples

### Example 1: Accepting Security Finding
```
Finding: API endpoint lacks rate limiting
Decision: DEFERRED
Rationale: Low-traffic internal tool, will add in v1.1
Mitigation: Monitoring alert if traffic exceeds 100 req/min
Trigger: Add rate limiting if alert fires
```

### Example 2: Rejecting Finding
```
Finding: Function exceeds 50 LOC guideline
Decision: REJECTED
Rationale: Function is a single cohesive operation, splitting
would reduce readability. External auditor applied generic
rule without context.
```

### Example 3: Accepting with Mitigation
```
Finding: No automated rollback for database migrations
Decision: MITIGATED
Rationale: Manual rollback scripts documented in RUNBOOK
Mitigation: Tested rollback procedure, DBA on-call during deploy
Trigger: Automate if we exceed 2 deploys/week
```

---

## Filing

Save completed forms as:
```
RISK-ACCEPTANCE-{{date}}-{{finding_summary}}.md
```

Location: `0. Admin/risk-acceptances/` (create folder if needed)

---

*Your signature on this document means you own the outcome. Choose wisely.*
