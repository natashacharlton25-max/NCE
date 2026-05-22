# Ambiguity Review

---
Phase: 27 (Hardening)
Review: 2 of 4 — Ambiguity
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Identify and resolve ambiguous specifications that could lead to multiple interpretations during implementation.

---

## Summary

| Metric | Count |
|--------|-------|
| Ambiguities found | {{n}} |
| Resolved | {{n}} |
| Pending resolution | {{n}} |
| Accepted (documented) | {{n}} |

**Ambiguity Score:** {{resolved}}/{{total}} = {{%}} resolved

---

## Ambiguity Patterns Checked

| Pattern | Description | Found |
|---------|-------------|-------|
| Vague language | "should", "might", "usually", "generally" | {{n}} |
| Missing defaults | Optional params without default values | {{n}} |
| Undefined behavior | What happens in X case? | {{n}} |
| Implicit assumptions | Unstated prerequisites | {{n}} |
| Incomplete validation | "validate X" without rules | {{n}} |
| Unclear error handling | "returns error" without code | {{n}} |
| Ambiguous timing | "after", "when", "soon" | {{n}} |
| Unclear ownership | Who is responsible for X? | {{n}} |

---

## Findings

### AMB-001: {{Title}}

**Severity:** Critical / High / Medium / Low
**Location:** `{{component}}/spec/{{file}}.md` → {{section}}
**Pattern:** {{which pattern}}

**Ambiguous Text:**
> "{{quoted ambiguous text}}"

**Problem:**
{{Why this is ambiguous — what are the multiple interpretations?}}

**Interpretation A:** {{one way to read it}}
**Interpretation B:** {{another way to read it}}

**Resolution:**
{{Clear, unambiguous replacement text}}

**Status:** RESOLVED / PENDING / ACCEPTED

---

### AMB-002: Vague Validation Rules

**Severity:** High
**Location:** `user-system/spec/FUNCTIONS.md` → createUser()
**Pattern:** Incomplete validation

**Ambiguous Text:**
> "Validate the email address"

**Problem:**
Does not specify what makes an email valid.

**Interpretation A:** Any string with @ symbol
**Interpretation B:** RFC 5322 compliant
**Interpretation C:** Must be deliverable (MX check)

**Resolution:**
> "Validate email: must match RFC 5322 format, maximum 254 characters, normalized to lowercase. Do NOT verify deliverability."

**Status:** RESOLVED

---

### AMB-003: Missing Default Value

**Severity:** Medium
**Location:** `project-system/spec/CONFIG.md` → timeout
**Pattern:** Missing defaults

**Ambiguous Text:**
> "timeout (optional): Request timeout in milliseconds"

**Problem:**
No default value specified. Implementer must guess.

**Resolution:**
> "timeout (optional): Request timeout in milliseconds. Default: 30000 (30 seconds). Range: 1000-300000."

**Status:** RESOLVED

---

### AMB-004: Undefined Error Behavior

**Severity:** High
**Location:** `auth-system/spec/FUNCTIONS.md` → login()
**Pattern:** Unclear error handling

**Ambiguous Text:**
> "Returns error if credentials invalid"

**Problem:**
Which error code? Same error for "user not found" vs "wrong password"?

**Resolution:**
> "Returns `AUTH_101` (Invalid credentials) for both unknown email and incorrect password. Do NOT distinguish between these cases (security: prevents enumeration)."

**Status:** RESOLVED

---

### AMB-005: Implicit Assumption

**Severity:** High
**Location:** `billing-subsystem/spec/FUNCTIONS.md` → chargeUser()
**Pattern:** Implicit assumptions

**Ambiguous Text:**
> "Charges the user's default payment method"

**Problem:**
Assumes user has a default payment method. What if they don't?

**Resolution:**
> "Charges the user's default payment method. If no default payment method exists, returns `BILL_102` (No payment method). Caller must ensure payment method exists or handle this error."

**Status:** RESOLVED

---

### AMB-006: Ambiguous Timing

**Severity:** Medium
**Location:** `notification-system/spec/BEHAVIOUR.md` → Event handling
**Pattern:** Ambiguous timing

**Ambiguous Text:**
> "Send notification after user signup"

**Problem:**
Immediately after? Async? What if send fails?

**Resolution:**
> "On `user.created` event, queue notification for async delivery within 5 seconds. If delivery fails, retry 3 times with exponential backoff. After 3 failures, log error and continue (non-blocking)."

**Status:** RESOLVED

---

### AMB-007: {{Next Finding}}

(Repeat for each ambiguity found)

---

## By Component

### {{Component Name}}

| ID | Location | Pattern | Severity | Status |
|----|----------|---------|----------|--------|
| AMB-001 | FUNCTIONS.md | Vague language | High | RESOLVED |
| AMB-005 | FUNCTIONS.md | Implicit assumption | High | RESOLVED |
| AMB-008 | TYPES.md | Missing defaults | Medium | PENDING |

**Component Ambiguity Score:** {{resolved}}/{{total}}

---

## Accepted Ambiguities

Some ambiguities are intentionally left for implementation-time decision:

| ID | Location | Ambiguity | Reason for Acceptance |
|----|----------|-----------|----------------------|
| AMB-010 | CONFIG.md | Exact cache eviction strategy | Performance tuning, measure first |
| _None_ | | | |

---

## Common Resolutions Applied

| Pattern | Standard Resolution |
|---------|---------------------|
| "validate X" | Add specific rules, formats, constraints |
| "optional param" | Add default value and valid range |
| "returns error" | Specify exact error code |
| "should/might" | Replace with "must/will" or remove |
| "after X happens" | Specify sync/async, timing, failure handling |
| "user must be Y" | Specify how verified, error if not |

---

## Language Cleanup

### Banned Phrases

| Phrase | Replace With |
|--------|--------------|
| "should" | "must" or remove |
| "might" | "will" or specify condition |
| "usually" | specify when/when not |
| "generally" | specify exceptions |
| "etc." | list all items explicitly |
| "and so on" | list all items explicitly |
| "as appropriate" | specify criteria |
| "if needed" | specify when needed |
| "optionally" | "optional: [default]" |

---

## Action Items

| Priority | ID | Component | Action | Status |
|----------|-----|-----------|--------|--------|
| Critical | AMB-002 | user-system | Add email validation rules | ✅ |
| High | AMB-004 | auth-system | Clarify error codes | ✅ |
| High | AMB-005 | billing | Handle missing payment method | ✅ |
| Medium | AMB-003 | project-system | Add config defaults | ✅ |
| Medium | AMB-008 | {{component}} | {{action}} | PENDING |

---

## Sign-off

- [ ] All spec files scanned for ambiguous language
- [ ] All "should/might/usually" replaced or justified
- [ ] All optional parameters have defaults
- [ ] All validation rules specified
- [ ] All error conditions have error codes
- [ ] Accepted ambiguities documented with rationale

**Reviewer:** Claude
**Date:** {{timestamp}}

---
Phase: 27 (Hardening)
Generated: {{timestamp}}
---
