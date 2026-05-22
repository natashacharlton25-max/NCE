# {{Component Name}} — Spec Index

---
Component: {{component_name}}
Type: System / Subsystem / Library
Parent: {{parent_name or N/A}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Quick Reference

| Function | Purpose | File |
|----------|---------|------|
| {{function}} | {{one-line purpose}} | FUNCTIONS.md#{{anchor}} |
| {{function}} | {{one-line purpose}} | FUNCTIONS.md#{{anchor}} |
| {{function}} | {{one-line purpose}} | FUNCTIONS.md#{{anchor}} |

---

## Spec Files

| File | Purpose | Status | Est. LOC | Last Updated |
|------|---------|--------|----------|--------------|
| INDEX.md | Quick reference & navigation | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| OVERVIEW.md | Identity, scope, boundaries | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| FUNCTIONS.md | Public API surface | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| TYPES.md | Data structures & schemas | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| BEHAVIOUR.md | State machines & flows | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| ERRORS.md | Error codes & handling | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| CONFIG.md | Configuration & environment | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| STORAGE.md | Persistence & data access | ✅ / 🔄 / ❌ / N/A | ~{{n}} | {{date}} |
| DECISIONS.md | Architecture decisions | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| EXAMPLES.md | Usage examples | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |

**Legend:** ✅ Complete | 🔄 In Progress | ❌ Not Started | N/A Not Applicable

---

## Dependencies

### This Component Depends On:

| Component | Type | Relationship | Why |
|-----------|------|--------------|-----|
| {{component}} | System / Subsystem / External / Library | Calls / Consumes / Requires | {{reason}} |

### Components That Depend On This:

| Component | Relationship | Why |
|-----------|--------------|-----|
| {{component}} | Calls / Consumes / Requires | {{reason}} |

---

## Key Types

| Type | Purpose | File |
|------|---------|------|
| {{TypeName}} | {{purpose}} | TYPES.md#{{anchor}} |

---

## Error Code Range

| Range | Category | File |
|-------|----------|------|
| {{PREFIX}}_001-099 | Validation errors | ERRORS.md#validation |
| {{PREFIX}}_100-199 | Business rule errors | ERRORS.md#business |
| {{PREFIX}}_200-299 | Authorization errors | ERRORS.md#auth |
| {{PREFIX}}_300-399 | Dependency errors | ERRORS.md#dependency |
| {{PREFIX}}_900-999 | System errors | ERRORS.md#system |

---

## Build Notes & Constraints

### Unresolved Issues

| Issue | Impact | Owner | Status | Source |
|-------|--------|-------|--------|--------|
| {{issue description}} | High / Medium / Low | Human / Claude / TBD | Open / In Progress / Resolved | {{which phase revealed this}} |
| _None_ | | | | |

### Implementation Constraints

| Constraint | Reason | Workaround | Permanent? |
|------------|--------|------------|------------|
| {{constraint}} | {{why this exists}} | {{if any}} | Yes / No |
| _None_ | | | |

### Tech Debt / Known Limitations

| Item | Severity | Plan | When |
|------|----------|------|------|
| {{limitation}} | High / Medium / Low | {{how to address}} | {{when}} |
| _None_ | | | |

### Questions for Human Review

| Question | Context | Decision | Decided By |
|----------|---------|----------|------------|
| {{question}} | {{why asking}} | {{answer when decided}} | |
| _None_ | | | |

### Spec Gaps

| Gap | Affected Section | Impact | Blocking? |
|-----|------------------|--------|-----------|
| {{what's missing or unclear}} | {{which file}} | High / Medium / Low | Yes / No |
| _None_ | | | |

---

## Related Documents

| Document | Purpose | Location |
|----------|---------|----------|
| PRE-SPEC-NOTES.md | Pre-spec thinking | ../PRE-SPEC-NOTES.md |
| PASS-NOTES.md | Pass phase notes | ../PASS-NOTES.md |
| SYSTEM.md | System definition | ../SYSTEM.md |
| CONTRACT.md | System contract | ../CONTRACT.md |

---

## Change Log

| Date | Version | Change | Author |
|------|---------|--------|--------|
| {{date}} | 1.0.0 | Initial creation | Claude |

---
Generated: {{timestamp}}
Phase: 21 (Internal Component Specs)
---
