# Pre-Pass 0 Checklist

---
Status: DRAFT | READY FOR PASS 0 | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Purpose
This checklist confirms that all build phases are complete and approved before starting Pass 0 (Coverage Reviews).

**Do NOT proceed to Pass 0 until all required items are checked.**

---

## Phase 1: Foundation

### Project Overview
- [ ] PROJECT-OVERVIEW.md exists
- [ ] Status: APPROVED
- [ ] Location: `{{project}}/admin/` or project root

### Project Intention
- [ ] PROJECT-INTENTION.md exists
- [ ] Status: APPROVED
- [ ] Location: `{{project}}/admin/` or project root

---

## Phase 2: Internal Systems

### Systems Identification
- [ ] SYSTEMS-CLARIFICATION.md exists
- [ ] Status: APPROVED
- [ ] All systems listed with purpose and justification
- [ ] Location: `{{project}}/admin/`

### Systems Pre-build Check
- [ ] SYSTEMS-PREBUILD-CHECK.md exists
- [ ] Status: ALL APPROVED
- [ ] No unresolved issues
- [ ] Location: `{{project}}/admin/`

### System Build
For each system:

| System | Folder Exists | SYSTEM-NOTES.md | Status |
|--------|---------------|-----------------|--------|
| {{system-name}} | [ ] | [ ] | [ ] APPROVED |
| {{system-name}} | [ ] | [ ] | [ ] APPROVED |
| {{system-name}} | [ ] | [ ] | [ ] APPROVED |

---

## Phase 3: Subsystems

### Subsystem Identification
For each system that has subsystems:

| Parent System | SUBSYSTEMS-CLARIFICATION.md | Status |
|---------------|----------------------------|--------|
| {{system-name}} | [ ] Exists | [ ] APPROVED |
| {{system-name}} | [ ] Exists | [ ] APPROVED |
| {{system-name}} | [ ] N/A (no subsystems) | [ ] N/A |

### Subsystem Pre-build Check
| Parent System | SUBSYSTEM-PREBUILD-CHECK.md | Status |
|---------------|----------------------------|--------|
| {{system-name}} | [ ] Exists | [ ] APPROVED |
| {{system-name}} | [ ] Exists | [ ] APPROVED |
| {{system-name}} | [ ] N/A (no subsystems) | [ ] N/A |

### Subsystem Build
For each subsystem:

| Parent System | Subsystem | Folder Exists | SUBSYSTEM-NOTES.md | Status |
|---------------|-----------|---------------|-------------------|--------|
| {{system}} | {{subsystem}} | [ ] | [ ] | [ ] APPROVED |
| {{system}} | {{subsystem}} | [ ] | [ ] | [ ] APPROVED |
| {{system}} | {{subsystem}} | [ ] | [ ] | [ ] APPROVED |

---

## Phase 4: External Integrations

### External Integrations Register
- [ ] EXTERNAL-INTEGRATIONS-REGISTER.md exists
- [ ] Status: APPROVED
- [ ] All providers specified by user
- [ ] Location: `{{project}}/admin/`

**If no external integrations:** Check here [ ] and skip to Summary.

### Provider Build
For each provider:

| Provider | Folder Exists | PROVIDER-NOTES.md | Status |
|----------|---------------|-------------------|--------|
| {{provider}} | [ ] `integration-{{provider}}/` | [ ] | [ ] APPROVED |
| {{provider}} | [ ] `integration-{{provider}}/` | [ ] | [ ] APPROVED |

### Service Scope
For each provider:

| Provider | SERVICE-SCOPE.md | Status |
|----------|------------------|--------|
| {{provider}} | [ ] Exists | [ ] APPROVED |
| {{provider}} | [ ] Exists | [ ] APPROVED |

### Service Build
For each service:

| Provider | Service | Folder Exists | SERVICE-NOTES.md | Status |
|----------|---------|---------------|------------------|--------|
| {{provider}} | {{service}} | [ ] | [ ] | [ ] APPROVED |
| {{provider}} | {{service}} | [ ] | [ ] | [ ] APPROVED |
| {{provider}} | {{service}} | [ ] | [ ] | [ ] APPROVED |

---

## Summary

### Document Count

| Category | Expected | Found | All Approved? |
|----------|----------|-------|---------------|
| Foundation docs | 2 | | [ ] |
| System folders | {{n}} | | [ ] |
| SYSTEM-NOTES.md | {{n}} | | [ ] |
| Subsystem folders | {{n}} | | [ ] |
| SUBSYSTEM-NOTES.md | {{n}} | | [ ] |
| Provider folders | {{n}} | | [ ] |
| PROVIDER-NOTES.md | {{n}} | | [ ] |
| SERVICE-SCOPE.md | {{n}} | | [ ] |
| Service folders | {{n}} | | [ ] |
| SERVICE-NOTES.md | {{n}} | | [ ] |

### Folder Structure Verification

```
{{project}}/
├── admin/
│   ├── PROJECT-OVERVIEW.md           [ ]
│   ├── PROJECT-INTENTION.md          [ ]
│   ├── SYSTEMS-CLARIFICATION.md      [ ]
│   ├── SYSTEMS-PREBUILD-CHECK.md     [ ]
│   └── EXTERNAL-INTEGRATIONS-REGISTER.md  [ ]
│
├── {{system-name}}/                  [ ]
│   ├── SYSTEM-NOTES.md               [ ]
│   ├── SUBSYSTEMS-CLARIFICATION.md   [ ]
│   ├── SUBSYSTEM-PREBUILD-CHECK.md   [ ]
│   └── {{subsystem-name}}/           [ ]
│       └── SUBSYSTEM-NOTES.md        [ ]
│
├── integration-{{provider}}/         [ ]
│   ├── PROVIDER-NOTES.md             [ ]
│   ├── SERVICE-SCOPE.md              [ ]
│   └── {{service-name}}/             [ ]
│       └── SERVICE-NOTES.md          [ ]
│
└── ...
```

---

## Blockers

List any issues preventing Pass 0:

| Issue | Blocking | Resolution |
|-------|----------|------------|
| | | |

---

## Final Checklist

- [ ] All foundation docs approved
- [ ] All systems built and approved
- [ ] All subsystems built and approved (or marked N/A)
- [ ] All providers built and approved (or marked N/A)
- [ ] All services built and approved (or marked N/A)
- [ ] Folder structure matches expected layout
- [ ] No unresolved blockers

---

## Verdict

**Status:** READY FOR PASS 0 | NOT READY

**If NOT READY, what's missing:**
- 

---

## Next Step
When Status is **READY FOR PASS 0**, proceed to: **Pass 0 (Coverage Reviews)**