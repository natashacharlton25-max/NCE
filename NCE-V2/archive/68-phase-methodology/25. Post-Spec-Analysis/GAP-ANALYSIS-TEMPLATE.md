# Gap Analysis

---
Phase: 25 (Post-Spec Analysis)
Analysis: 5 of 5 — Gaps & Inconsistencies
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Identify missing specifications, incomplete sections, contradictions, and unresolved questions across all specs.

---

## Scope

### Components Checked

| Component Type | Count | Checked |
|----------------|-------|---------|
| Systems | {{n}} | ✅ |
| Subsystems | {{n}} | ✅ |
| Integrations | {{n}} | ✅ |
| Libraries | {{n}} | ✅ |

**Total components:** {{n}}

### Spec Files per Component

| File | Expected | Found | Missing |
|------|----------|-------|---------|
| INDEX.md | {{n}} | {{n}} | {{n}} |
| OVERVIEW.md | {{n}} | {{n}} | {{n}} |
| FUNCTIONS.md | {{n}} | {{n}} | {{n}} |
| TYPES.md | {{n}} | {{n}} | {{n}} |
| BEHAVIOUR.md | {{n}} | {{n}} | {{n}} |
| ERRORS.md | {{n}} | {{n}} | {{n}} |
| EXAMPLES.md | {{n}} | {{n}} | {{n}} |

---

## Missing Specifications

### Missing Spec Files

| Component | Missing File | Impact | Priority |
|-----------|--------------|--------|----------|
| {{component}} | BEHAVIOUR.md | Flow undefined | High |
| {{component}} | EXAMPLES.md | Hard to implement | Medium |
| {{component}} | {{file}} | {{impact}} | {{priority}} |
| _None_ | | | |

### Missing Components

| Expected Component | Referenced By | Impact | Priority |
|--------------------|---------------|--------|----------|
| payment-system | billing-subsystem | Cannot implement billing | Critical |
| {{component}} | {{reference}} | {{impact}} | {{priority}} |
| _None_ | | | |

---

## Incomplete Sections

### Finding: Incomplete Section

**Severity:** High
**Location:** `systems/user/spec/FUNCTIONS.md`
**Section:** `updateUser()`

**Issue:** Function documented but missing:
- [ ] Error codes
- [ ] Examples
- [ ] Validation rules

**Impact:** Implementer must guess behaviour.

**Recommendation:** Complete all subsections.

---

### Finding: Empty Section

**Severity:** Medium
**Location:** `systems/project/spec/BEHAVIOUR.md`
**Section:** State Machine

**Issue:** Section header exists but content is "TBD".

**Impact:** State transitions undefined.

**Recommendation:** Define states and transitions.

---

### Finding: {{Title}}

(Repeat for each incomplete section)

---

## Incomplete Section Summary

| Component | File | Section | Status |
|-----------|------|---------|--------|
| user-system | FUNCTIONS.md | updateUser errors | Incomplete |
| project-system | BEHAVIOUR.md | State Machine | Empty |
| {{component}} | {{file}} | {{section}} | {{status}} |

---

## Contradictions Found

### Finding: Contradictory Specification

**Severity:** Critical
**Locations:** 
- `systems/user/spec/TYPES.md` — `status: "active" | "inactive"`
- `systems/user/spec/BEHAVIOUR.md` — States: draft, active, suspended, deleted

**Issue:** TYPES.md defines 2 statuses, BEHAVIOUR.md defines 4 states.

**Impact:** Implementation will be inconsistent.

**Recommendation:** Align — use the 4-state model from BEHAVIOUR.md.

---

### Finding: Conflicting Error Codes

**Severity:** High
**Locations:**
- `systems/auth/spec/ERRORS.md` — `AUTH_101: Invalid credentials`
- `systems/auth/spec/FUNCTIONS.md` — `login() throws AUTH_001 for invalid credentials`

**Issue:** Different error codes for same condition.

**Impact:** Error handling will be wrong.

**Recommendation:** Align to AUTH_101 in both files.

---

### Finding: {{Title}}

(Repeat for each contradiction)

---

## Contradiction Summary

| Type | Count | Critical | High | Medium |
|------|-------|----------|------|--------|
| Type mismatches | {{n}} | {{n}} | {{n}} | {{n}} |
| Error code conflicts | {{n}} | {{n}} | {{n}} | {{n}} |
| Behaviour conflicts | {{n}} | {{n}} | {{n}} | {{n}} |
| Dependency conflicts | {{n}} | {{n}} | {{n}} | {{n}} |

---

## Unresolved Questions

### Questions from INDEX.md "Build Notes"

| Component | Question | Context | Blocking |
|-----------|----------|---------|----------|
| user-system | Should soft-deleted users be queryable? | Affects list() | Yes |
| auth-system | Token refresh — sliding or fixed expiry? | Affects UX | Yes |
| {{component}} | {{question}} | {{context}} | Yes/No |

### Questions Discovered During Analysis

| Question | Affects | Source | Priority |
|----------|---------|--------|----------|
| What happens when rate limited mid-batch? | All integrations | BEHAVIOUR review | High |
| How to handle partial failures in batch ops? | All batch functions | FUNCTIONS review | High |
| {{question}} | {{scope}} | {{source}} | {{priority}} |

---

## Unresolved Questions Summary

| Status | Count |
|--------|-------|
| Blocking implementation | {{n}} |
| Should resolve before impl | {{n}} |
| Can defer | {{n}} |
| **Total** | {{n}} |

---

## Edge Cases Not Addressed

### Finding: Missing Edge Case

**Severity:** Medium
**Location:** `systems/user/spec/FUNCTIONS.md` — `deleteUser()`

**Edge Case:** What if user is currently logged in when deleted?

**Impact:** Could leave orphan sessions.

**Recommendation:** Document: invalidate all sessions on delete.

---

### Finding: Missing Edge Case

**Severity:** High
**Location:** `integrations/stripe/spec/FAILURE-MODES.md`

**Edge Case:** What if webhook arrives before API response?

**Impact:** Race condition in order processing.

**Recommendation:** Document idempotency requirements.

---

### Finding: {{Title}}

(Repeat for each edge case)

---

## Edge Cases Summary

| Category | Missing | Documented |
|----------|---------|------------|
| Null/empty inputs | {{n}} | {{n}} |
| Concurrent access | {{n}} | {{n}} |
| Failure during operation | {{n}} | {{n}} |
| Boundary values | {{n}} | {{n}} |
| Race conditions | {{n}} | {{n}} |

---

## Cross-Reference Gaps

### References to Non-Existent Items

| Source | References | Target | Exists |
|--------|------------|--------|--------|
| user OVERVIEW | Depends on | auth-system | ✅ |
| billing FUNCTIONS | Calls | payment.charge() | ❌ |
| {{source}} | {{type}} | {{target}} | ✅/❌ |

### Unreferenced Components

| Component | Referenced By | Action |
|-----------|---------------|--------|
| lib-legacy | Nothing | Remove or document |
| {{component}} | {{references}} | {{action}} |

---

## Security Gaps

### Missing Security Considerations

| Component | Gap | Impact | Priority |
|-----------|-----|--------|----------|
| auth-system | No rate limiting on login | Brute force vulnerable | Critical |
| user-system | No PII handling documented | Compliance risk | High |
| {{component}} | {{gap}} | {{impact}} | {{priority}} |

---

## Performance Gaps

### Missing Performance Specifications

| Component | Gap | Impact | Priority |
|-----------|-----|--------|----------|
| project-system | No pagination limit | Memory issues | High |
| search-subsystem | No timeout specified | Could hang | Medium |
| {{component}} | {{gap}} | {{impact}} | {{priority}} |

---

## Summary

### Gap Statistics

| Category | Count |
|----------|-------|
| Missing spec files | {{n}} |
| Incomplete sections | {{n}} |
| Contradictions | {{n}} |
| Unresolved questions | {{n}} |
| Missing edge cases | {{n}} |
| Security gaps | {{n}} |
| Performance gaps | {{n}} |
| **Total gaps** | {{n}} |

### By Severity

| Severity | Count | Must Fix Before Impl |
|----------|-------|---------------------|
| Critical | {{n}} | Yes |
| High | {{n}} | Yes |
| Medium | {{n}} | Recommended |
| Low | {{n}} | Track |

### By Component

| Component | Gaps | Critical | Status |
|-----------|------|----------|--------|
| user-system | {{n}} | {{n}} | Needs work |
| auth-system | {{n}} | {{n}} | Needs work |
| project-system | {{n}} | {{n}} | Mostly complete |
| {{component}} | {{n}} | {{n}} | {{status}} |

---

## Action Items

| Priority | Action | Component | Effort | Status |
|----------|--------|-----------|--------|--------|
| 1 | Resolve status/state contradiction | user-system | 30 min | TODO |
| 2 | Add missing BEHAVIOUR.md | project-system | 2 hr | TODO |
| 3 | Answer blocking questions | Multiple | 1 hr | TODO |
| 4 | Document edge cases | All | 3 hr | TODO |
| 5 | Add security considerations | auth-system | 1 hr | TODO |

---

## Blocking Issues

These MUST be resolved before implementation:

| Issue | Component | Owner | Deadline |
|-------|-----------|-------|----------|
| Status contradiction | user-system | TBD | Before impl |
| Missing payment-system | billing | TBD | Before impl |
| {{issue}} | {{component}} | {{owner}} | {{deadline}} |

---

## Sign-off

- [ ] All components checked for completeness
- [ ] All contradictions identified
- [ ] All unresolved questions listed
- [ ] Edge cases reviewed
- [ ] Security gaps identified
- [ ] Performance gaps identified
- [ ] Human reviewed

**Analyst:** Claude
**Date:** {{timestamp}}

---
Phase: 25 (Post-Spec Analysis)
Generated: {{timestamp}}
---
