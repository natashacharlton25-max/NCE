# Error Code Analysis

---
Phase: 25 (Post-Spec Analysis)
Analysis: 1 of 5 — Error Codes
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Analyze all error codes across the project to ensure uniqueness, consistency, and completeness.

---

## Scope

### Files Analyzed

| Component | File | Status |
|-----------|------|--------|
| {{system-a}} | `spec/ERRORS.md` | ✅ Analyzed |
| {{system-b}} | `spec/ERRORS.md` | ✅ Analyzed |
| {{integration}} | `spec/ERRORS.md` | ✅ Analyzed |
| {{library}} | `spec/ERRORS.md` | ✅ Analyzed |

**Total files analyzed:** {{n}}

---

## Error Code Registry

### All Error Codes

| Code | Component | Category | Description |
|------|-----------|----------|-------------|
| `{{PREFIX}}_001` | {{component}} | Validation | {{description}} |
| `{{PREFIX}}_002` | {{component}} | Validation | {{description}} |
| `{{PREFIX}}_101` | {{component}} | Business | {{description}} |
| `{{PREFIX}}_201` | {{component}} | Dependency | {{description}} |

**Total error codes:** {{n}}

### By Component

| Component | Prefix | Code Range | Count |
|-----------|--------|------------|-------|
| {{system-a}} | `SYS_A` | 001-999 | {{n}} |
| {{system-b}} | `SYS_B` | 001-999 | {{n}} |
| {{integration}} | `INT_{{PROVIDER}}` | 001-999 | {{n}} |
| {{library}} | `LIB_{{NAME}}` | 001-999 | {{n}} |

### By Category

| Category | Range | Count | % |
|----------|-------|-------|---|
| Validation | XXX_001-099 | {{n}} | {{%}} |
| Business | XXX_100-199 | {{n}} | {{%}} |
| Authorization | XXX_200-299 | {{n}} | {{%}} |
| Dependency | XXX_300-399 | {{n}} | {{%}} |
| Storage | XXX_400-499 | {{n}} | {{%}} |
| External | XXX_500-599 | {{n}} | {{%}} |
| Internal | XXX_900-999 | {{n}} | {{%}} |

---

## Findings

### Finding: {{Title}}

**Severity:** Critical / High / Medium / Low
**Location:** `{{file path}}`
**Issue:** {{description of the issue}}
**Impact:** {{what breaks if not fixed}}
**Recommendation:** {{specific fix}}

---

### Finding: Duplicate Error Code

**Severity:** Critical
**Location:** 
- `systems/user/spec/ERRORS.md` — `USR_101`
- `systems/auth/spec/ERRORS.md` — `USR_101`

**Issue:** Same error code `USR_101` defined in two components with different meanings.

**Impact:** Error handling code cannot distinguish between these errors.

**Recommendation:** 
- Rename auth error to `AUTH_101`
- Update all references

---

### Finding: Missing Error Category

**Severity:** Medium
**Location:** `systems/{{system}}/spec/ERRORS.md`

**Issue:** No timeout errors defined, but BEHAVIOUR.md mentions timeout handling.

**Impact:** Timeout scenarios will use generic errors.

**Recommendation:** Add `{{PREFIX}}_XXX` for timeout errors.

---

### Finding: Inconsistent Prefix

**Severity:** High
**Location:** `integrations/stripe/spec/ERRORS.md`

**Issue:** Uses prefix `STRIPE_` instead of convention `INT_STRIPE_`.

**Impact:** Inconsistent error handling patterns.

**Recommendation:** Rename to follow `INT_{{PROVIDER}}_` convention.

---

### Finding: Gap in Code Range

**Severity:** Low
**Location:** `systems/{{system}}/spec/ERRORS.md`

**Issue:** Codes jump from 005 to 010, leaving gap.

**Impact:** Minor — just inconsistent.

**Recommendation:** Document reserved codes or renumber.

---

### Finding: {{Next Finding}}

(Repeat for each finding)

---

## Duplicate Check

### Duplicates Found

| Code | Locations | Resolution |
|------|-----------|------------|
| `{{CODE}}` | {{loc1}}, {{loc2}} | Rename in {{loc}} |
| _None found_ | | |

### Near-Duplicates

| Code 1 | Code 2 | Similarity | Action |
|--------|--------|------------|--------|
| `{{CODE_A}}` | `{{CODE_B}}` | Same meaning | Consolidate |
| _None found_ | | | |

---

## Convention Compliance

### Prefix Convention

| Component Type | Expected | Actual | Compliant |
|----------------|----------|--------|-----------|
| System | `{{SYS}}_` | {{actual}} | ✅ / ❌ |
| Integration | `INT_{{PROVIDER}}_` | {{actual}} | ✅ / ❌ |
| Library | `LIB_{{NAME}}_` | {{actual}} | ✅ / ❌ |

### Range Convention

| Category | Expected Range | Components Using | Compliant |
|----------|---------------|------------------|-----------|
| Validation | 001-099 | {{list}} | ✅ / ❌ |
| Business | 100-199 | {{list}} | ✅ / ❌ |
| Auth | 200-299 | {{list}} | ✅ / ❌ |

---

## Completeness Check

### Required Error Types

| Error Type | Systems Needing | Systems Having | Gap |
|------------|-----------------|----------------|-----|
| Validation errors | All | {{list}} | {{missing}} |
| Not found | CRUD systems | {{list}} | {{missing}} |
| Unauthorized | Auth-aware | {{list}} | {{missing}} |
| Rate limited | Integrations | {{list}} | {{missing}} |
| Timeout | All with I/O | {{list}} | {{missing}} |

---

## Summary

### Statistics

| Metric | Value |
|--------|-------|
| Total error codes | {{n}} |
| Components analyzed | {{n}} |
| Duplicates found | {{n}} |
| Convention violations | {{n}} |
| Missing error types | {{n}} |

### Issues by Severity

| Severity | Count | Action |
|----------|-------|--------|
| Critical | {{n}} | Fix before implementation |
| High | {{n}} | Fix before implementation |
| Medium | {{n}} | Fix during implementation |
| Low | {{n}} | Track for later |

---

## Action Items

| Priority | Action | Component | Effort | Status |
|----------|--------|-----------|--------|--------|
| 1 | Fix duplicate `{{CODE}}` | {{component}} | 15 min | TODO |
| 2 | Rename prefix to follow convention | {{component}} | 30 min | TODO |
| 3 | Add missing timeout errors | {{component}} | 1 hr | TODO |
| 4 | {{action}} | {{component}} | {{effort}} | TODO |

---

## Sign-off

- [ ] All ERRORS.md files analyzed
- [ ] All findings documented
- [ ] Duplicates identified
- [ ] Convention compliance checked
- [ ] Action items prioritized
- [ ] Human reviewed

**Analyst:** Claude
**Date:** {{timestamp}}

---
Phase: 25 (Post-Spec Analysis)
Generated: {{timestamp}}
---
