# {{Provider}} {{Service}} — Spec Index

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
Last Updated: {{timestamp}}
---

## Purpose

(One sentence — what capability this integration provides to the project)

---

## Quick Reference

### Our Wrapper Functions

| Function | Purpose | File |
|----------|---------|------|
| `{{function}}()` | {{purpose}} | OUR-WRAPPER.md#{{anchor}} |
| `{{function}}()` | {{purpose}} | OUR-WRAPPER.md#{{anchor}} |
| `{{function}}()` | {{purpose}} | OUR-WRAPPER.md#{{anchor}} |

### Provider Endpoints We Use

| Endpoint | Purpose | File |
|----------|---------|------|
| `{{METHOD}} /path` | {{purpose}} | API-SURFACE.md#{{anchor}} |
| `{{METHOD}} /path` | {{purpose}} | API-SURFACE.md#{{anchor}} |

### Key Error Codes

| Code | Meaning | File |
|------|---------|------|
| `{{PREFIX}}_XXX` | {{meaning}} | ERRORS.md#{{anchor}} |
| `{{PREFIX}}_XXX` | {{meaning}} | ERRORS.md#{{anchor}} |

---

## Spec Files

| File | Description | Status | LOC | Updated |
|------|-------------|--------|-----|---------|
| INDEX.md | This file | ✅ | ~{{n}} | {{date}} |
| OVERVIEW.md | Purpose, what we use it for | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| API-SURFACE.md | Their API (endpoints we call) | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| OUR-WRAPPER.md | Our functions wrapping their API | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| FIELD-MAPPING.md | Their fields ↔ our fields | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| ERRORS.md | Their errors → our errors | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| CONSTRAINTS.md | Rate limits, quotas, auth, costs | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| FAILURE-MODES.md | What can fail, degradation | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| EXAMPLES.md | Request/response examples | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |

**Legend:** ✅ Complete | 🔄 In Progress | ❌ Not Started

---

## Provider Information

| Field | Value |
|-------|-------|
| **Provider** | {{provider name}} |
| **Service** | {{service name}} |
| **API Version** | {{version}} |
| **Documentation** | {{link}} |
| **Status Page** | {{link}} |
| **Our Account** | {{reference only, no credentials}} |

---

## Consuming Systems

### Systems That Use This Integration

| System | What They Use | Dependency Type |
|--------|---------------|-----------------|
| {{system}} | {{functions/capabilities}} | Required / Optional |
| {{system}} | {{functions/capabilities}} | Required / Optional |

### If This Integration Fails

| System | Impact | Fallback |
|--------|--------|----------|
| {{system}} | {{impact}} | {{fallback behaviour}} |
| {{system}} | {{impact}} | {{fallback behaviour}} |

---

## Constraints Summary

| Constraint | Value | Notes |
|------------|-------|-------|
| Rate limit | {{n}}/min | See CONSTRAINTS.md |
| Daily quota | {{n}} | See CONSTRAINTS.md |
| Auth method | {{type}} | See CONSTRAINTS.md |
| Cost | ${{n}}/1000 | See CONSTRAINTS.md |

---

## Build Notes & Constraints

### Unresolved Issues

| Issue | Impact | Owner | Status | Source |
|-------|--------|-------|--------|--------|
| {{issue}} | High / Medium / Low | {{owner}} | Open / Resolved | {{phase}} |
| _None_ | | | | |

### Implementation Constraints

| Constraint | Reason | Workaround |
|------------|--------|------------|
| {{constraint}} | {{why}} | {{workaround or "None"}} |
| _None_ | | |

### Provider Limitations

| Limitation | Impact | Workaround |
|------------|--------|------------|
| {{limitation}} | {{impact}} | {{workaround}} |
| _None_ | | |

### Questions for Human Review

| Question | Context | Decision |
|----------|---------|----------|
| {{question}} | {{why asking}} | PENDING / {{answer}} |
| _None_ | | |

### Spec Gaps

| Gap | Affected File | Impact | Blocking? |
|-----|---------------|--------|-----------|
| {{gap}} | {{file}} | High / Medium / Low | Yes / No |
| _None_ | | | |

---

## Related Documentation

| Document | Location | Purpose |
|----------|----------|---------|
| PRE-SPEC-NOTES.md | `../PRE-SPEC-NOTES.md` | Pre-spec thinking |
| PASS-NOTES.md | `../PASS-NOTES.md` | Pass phase notes |
| EXTERNAL-INTEGRATION-NOTES.md | `../EXTERNAL-INTEGRATION-NOTES.md` | Integration definition |
| Provider API Docs | {{external link}} | Official reference |

---

## Change Log

| Version | Date | Change | Author |
|---------|------|--------|--------|
| v0.1.0 | {{date}} | Initial creation | Claude |

---
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
