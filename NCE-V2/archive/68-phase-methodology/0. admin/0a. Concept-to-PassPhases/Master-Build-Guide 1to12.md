# Master Build Guide

---
Version: v1.0.0
Last Updated: {{timestamp}}
Location: 0. Admin/0a. Concept to PassPhase/
---

## Purpose
This is the **master index** for building a project from concept to Pass 0-ready.
Follow phases in order. Do not skip phases. Check off as you complete.

---

## Quick Reference

| Phase | Folder | Prompt | Template | Output Location |
|-------|--------|--------|----------|-----------------|
| 1 | `1. Project Overview/` | PROJECT-OVERVIEW-PROMPT.md | PROJECT-OVERVIEW-TEMPLATE.md | `/admin/` |
| 2 | `2. Project Intention/` | PROJECT-INTENTION-PROMPT.md | PROJECT-INTENTION-TEMPLATE.md | `/admin/` |
| 3 | `3. System Identification/` | SYSTEMS-IDENTIFICATION-PROMPT.md | SYSTEMS-CLARIFICATION-TEMPLATE.md + EXTERNAL-INTEGRATIONS-REGISTER-TEMPLATE.md | `/admin/` |
| 4 | `4. Systems Pre-Build Check/` | SYSTEMS-PREBUILD-CHECK-PROMPT.md | SYSTEMS-PREBUILD-CHECK-TEMPLATE.md | `/admin/` |
| 5 | `5. Systems Build/` | SYSTEM-BUILD-PROMPT.md | SYSTEM-NOTES-TEMPLATE.md | `/{{system}}/` |
| 6 | `6. Subsystems Identification/` | SUBSYSTEM-IDENTIFICATION-PROMPT.md | SUBSYSTEMS-CLARIFICATION-TEMPLATE.md | `/{{system}}/` |
| 7 | `7. Subsystems Pre-Build Check/` | SUBSYSTEM-PREBUILD-CHECK-PROMPT.md | SUBSYSTEM-PREBUILD-CHECK-TEMPLATE.md | `/{{system}}/` |
| 8 | `8. Subsystem Build/` | SUBSYSTEM-BUILD-PROMPT.md | SUBSYSTEM-NOTES-TEMPLATE.md | `/{{system}}/{{subsystem}}/` |
| 9 | `9. External Integration Provider Build/` | EXTERNAL-INTEGRATION-PROVIDER-BUILD-PROMPT.md | PROVIDER-NOTES-TEMPLATE.md | `/integration-{{provider}}/` |
| 10 | `10. External Integration Service Scope/` | EXTERNAL-INTEGRATION-SERVICE-SCOPE-PROMPT.md | SERVICE-SCOPE-TEMPLATE.md | `/integration-{{provider}}/` |
| 11 | `11. External Integration Service Build/` | EXTERNAL-INTEGRATION-SERVICE-BUILD-PROMPT.md | SERVICE-NOTES-TEMPLATE.md | `/integration-{{provider}}/{{service}}/` |
| 12 | `12. PrePass Check/` | PRE-PASS-0-CHECKLIST-PROMPT.md | PRE-PASS-0-CHECKLIST-TEMPLATE.md | `/admin/` |

---

## Template Folder Structure

```
SYSBUILDTEMPLATES/
│
├── 0. Admin/
│   ├── 0a. Concept to PassPhase/       ← YOU ARE HERE (Pre-build)
│   │   ├── Claude-prebuild.md          ← Instructions for Claude
│   │   └── Master-Build-Guide.md       ← This file
│   │
│   ├── 0b. PassPhases 0-4/             ← Pass 0, 1, 2, 3, 4
│   │   └── ...
│   │
│   └── 0c. PreCode/                    ← Before implementation
│       └── ...
│
├── 1. Project Overview/
│   ├── PROJECT-OVERVIEW-PROMPT.md
│   └── PROJECT-OVERVIEW-TEMPLATE.md
│
├── 2. Project Intention/
│   ├── PROJECT-INTENTION-PROMPT.md
│   └── PROJECT-INTENTION-TEMPLATE.md
│
├── 3. System Identification/
│   ├── SYSTEMS-IDENTIFICATION-PROMPT.md
│   ├── SYSTEMS-CLARIFICATION-TEMPLATE.md
│   └── EXTERNAL-INTEGRATIONS-REGISTER-TEMPLATE.md
│
├── 4. Systems Pre-Build Check/
│   ├── SYSTEMS-PREBUILD-CHECK-PROMPT.md
│   └── SYSTEMS-PREBUILD-CHECK-TEMPLATE.md
│
├── 5. Systems Build/
│   ├── SYSTEM-BUILD-PROMPT.md
│   └── SYSTEM-NOTES-TEMPLATE.md
│
├── 6. Subsystems Identification/
│   ├── SUBSYSTEM-IDENTIFICATION-PROMPT.md
│   └── SUBSYSTEMS-CLARIFICATION-TEMPLATE.md
│
├── 7. Subsystems Pre-Build Check/
│   ├── SUBSYSTEM-PREBUILD-CHECK-PROMPT.md
│   └── SUBSYSTEM-PREBUILD-CHECK-TEMPLATE.md
│
├── 8. Subsystem Build/
│   ├── SUBSYSTEM-BUILD-PROMPT.md
│   └── SUBSYSTEM-NOTES-TEMPLATE.md
│
├── 9. External Integration Provider Build/
│   ├── EXTERNAL-INTEGRATION-PROVIDER-BUILD-PROMPT.md
│   └── PROVIDER-NOTES-TEMPLATE.md
│
├── 10. External Integration Service Scope/
│   ├── EXTERNAL-INTEGRATION-SERVICE-SCOPE-PROMPT.md
│   └── SERVICE-SCOPE-TEMPLATE.md
│
├── 11. External Integration Service Build/
│   ├── EXTERNAL-INTEGRATION-SERVICE-BUILD-PROMPT.md
│   └── SERVICE-NOTES-TEMPLATE.md
│
└── 12. PrePass Check/
    ├── PRE-PASS-0-CHECKLIST-PROMPT.md
    └── PRE-PASS-0-CHECKLIST-TEMPLATE.md
```

---

## Phase-by-Phase Workflow

---

### PHASE 1: Project Overview
**Folder:** `1. Project Overview/`

**Goal:** Capture the raw project concept in a structured format.

| Item | Value |
|------|-------|
| **Prompt** | `1. Project Overview/PROJECT-OVERVIEW-PROMPT.md` |
| **Template** | `1. Project Overview/PROJECT-OVERVIEW-TEMPLATE.md` |
| **Input** | Raw project idea from user |
| **Output** | `{{project}}/admin/PROJECT-OVERVIEW.md` |
| **Approval** | User approves before proceeding |

**Checklist:**
- [ ] Run prompt with user's raw input
- [ ] Complete all template sections
- [ ] User approves
- [ ] Status updated to APPROVED

**Next:** Phase 2

---

### PHASE 2: Project Intention
**Folder:** `2. Project Intention/`

**Goal:** Derive purpose, success criteria, and boundaries from the overview.

| Item | Value |
|------|-------|
| **Prompt** | `2. Project Intention/PROJECT-INTENTION-PROMPT.md` |
| **Template** | `2. Project Intention/PROJECT-INTENTION-TEMPLATE.md` |
| **Input** | Approved PROJECT-OVERVIEW.md |
| **Output** | `{{project}}/admin/PROJECT-INTENTION.md` |
| **Approval** | User approves before proceeding |

**Checklist:**
- [ ] Run prompt with approved Project Overview
- [ ] Complete all template sections
- [ ] User approves
- [ ] Status updated to APPROVED

**Next:** Phase 3

---

### PHASE 3: Systems Identification
**Folder:** `3. System Identification/`

**Goal:** Identify internal systems AND external integration needs.

| Item | Value |
|------|-------|
| **Prompt** | `3. System Identification/SYSTEMS-IDENTIFICATION-PROMPT.md` |
| **Templates** | `3. System Identification/SYSTEMS-CLARIFICATION-TEMPLATE.md` + `3. System Identification/EXTERNAL-INTEGRATIONS-REGISTER-TEMPLATE.md` |
| **Input** | Approved PROJECT-OVERVIEW.md + PROJECT-INTENTION.md |
| **Outputs** | `{{project}}/admin/SYSTEMS-CLARIFICATION.md` + `{{project}}/admin/EXTERNAL-INTEGRATIONS-REGISTER.md` |
| **Approval** | User approves both + specifies providers for integrations |

**Checklist:**
- [ ] Run prompt with approved Overview + Intention
- [ ] Complete Systems Clarification (internal systems)
- [ ] Complete External Integrations Register (capabilities)
- [ ] User specifies providers for each integration
- [ ] User approves both documents
- [ ] Status updated to APPROVED on both

**Next:** Phase 4

---

### PHASE 4: Systems Pre-build Check
**Folder:** `4. Systems Pre-Build Check/`

**Goal:** Validate proposed systems before creating folders.

| Item | Value |
|------|-------|
| **Prompt** | `4. Systems Pre-Build Check/SYSTEMS-PREBUILD-CHECK-PROMPT.md` |
| **Template** | `4. Systems Pre-Build Check/SYSTEMS-PREBUILD-CHECK-TEMPLATE.md` |
| **Input** | Approved SYSTEMS-CLARIFICATION.md |
| **Output** | `{{project}}/admin/SYSTEMS-PREBUILD-CHECK.md` |
| **Approval** | All systems pass check |

**Checklist:**
- [ ] Run prompt with approved Systems Clarification
- [ ] Review each system against checklist
- [ ] All systems APPROVED (or revise and re-check)
- [ ] Status updated to ALL APPROVED

**Next:** Phase 5

---

### PHASE 5: System Build
**Folder:** `5. Systems Build/`

**Goal:** Create system folders and SYSTEM-NOTES.md files.

| Item | Value |
|------|-------|
| **Prompt** | `5. Systems Build/SYSTEM-BUILD-PROMPT.md` |
| **Template** | `5. Systems Build/SYSTEM-NOTES-TEMPLATE.md` |
| **Input** | Approved SYSTEMS-CLARIFICATION.md + SYSTEMS-PREBUILD-CHECK.md |
| **Output** | `{{project}}/{{system-name}}/SYSTEM-NOTES.md` (for each system) |
| **Approval** | User approves all system folders |

**Checklist:**
- [ ] Run prompt
- [ ] Create folder for each system
- [ ] Create SYSTEM-NOTES.md in each folder
- [ ] Complete all sections
- [ ] User approves
- [ ] Status updated to APPROVED on each

**Next:** Phase 6

---

### PHASE 6: Subsystem Identification
**Folder:** `6. Subsystems Identification/`

**Goal:** Identify subsystems for each system (if needed).

| Item | Value |
|------|-------|
| **Prompt** | `6. Subsystems Identification/SUBSYSTEM-IDENTIFICATION-PROMPT.md` |
| **Template** | `6. Subsystems Identification/SUBSYSTEMS-CLARIFICATION-TEMPLATE.md` |
| **Input** | Approved SYSTEM-NOTES.md for target system |
| **Output** | `{{project}}/{{system}}/SUBSYSTEMS-CLARIFICATION.md` |
| **Approval** | User approves (or confirms no subsystems needed) |

**Process:** Run once per system.

**Checklist:**
- [ ] For each system:
  - [ ] Determine if subsystems needed
  - [ ] If yes: complete Subsystems Clarification
  - [ ] If no: document "No subsystems needed"
  - [ ] User approves
  - [ ] Status updated to APPROVED

**Next:** Phase 7 (if subsystems exist) or Phase 9 (if no subsystems)

---

### PHASE 7: Subsystem Pre-build Check
**Folder:** `7. Subsystems Pre-Build Check/`

**Goal:** Validate proposed subsystems before creating folders.

| Item | Value |
|------|-------|
| **Prompt** | `7. Subsystems Pre-Build Check/SUBSYSTEM-PREBUILD-CHECK-PROMPT.md` |
| **Template** | `7. Subsystems Pre-Build Check/SUBSYSTEM-PREBUILD-CHECK-TEMPLATE.md` |
| **Input** | Approved SUBSYSTEMS-CLARIFICATION.md |
| **Output** | `{{project}}/{{system}}/SUBSYSTEM-PREBUILD-CHECK.md` |
| **Approval** | All subsystems pass check |

**Process:** Run once per system that has subsystems.

**Checklist:**
- [ ] For each system with subsystems:
  - [ ] Run prompt
  - [ ] All subsystems APPROVED (or revise)
  - [ ] Status updated to ALL APPROVED

**Next:** Phase 8

---

### PHASE 8: Subsystem Build
**Folder:** `8. Subsystem Build/`

**Goal:** Create subsystem folders and SUBSYSTEM-NOTES.md files.

| Item | Value |
|------|-------|
| **Prompt** | `8. Subsystem Build/SUBSYSTEM-BUILD-PROMPT.md` |
| **Template** | `8. Subsystem Build/SUBSYSTEM-NOTES-TEMPLATE.md` |
| **Input** | Approved SUBSYSTEMS-CLARIFICATION.md + SUBSYSTEM-PREBUILD-CHECK.md |
| **Output** | `{{project}}/{{system}}/{{subsystem}}/SUBSYSTEM-NOTES.md` |
| **Approval** | User approves all subsystem folders |

**Process:** Run once per system that has subsystems.

**Checklist:**
- [ ] For each system with subsystems:
  - [ ] Create folder for each subsystem
  - [ ] Create SUBSYSTEM-NOTES.md in each
  - [ ] User approves
  - [ ] Status updated to APPROVED on each

**Next:** Phase 9

---

### PHASE 9: External Integration Provider Build
**Folder:** `9. External Integration Provider Build/`

**Goal:** Create provider folders and PROVIDER-NOTES.md files.

| Item | Value |
|------|-------|
| **Prompt** | `9. External Integration Provider Build/EXTERNAL-INTEGRATION-PROVIDER-BUILD-PROMPT.md` |
| **Template** | `9. External Integration Provider Build/PROVIDER-NOTES-TEMPLATE.md` |
| **Input** | Approved EXTERNAL-INTEGRATIONS-REGISTER.md (with providers specified) |
| **Output** | `{{project}}/integration-{{provider}}/PROVIDER-NOTES.md` |
| **Approval** | User approves all provider folders |

**Skip if:** No external integrations identified in Phase 3.

**Checklist:**
- [ ] For each provider:
  - [ ] Create `integration-{{provider}}/` folder
  - [ ] Create PROVIDER-NOTES.md
  - [ ] User approves
  - [ ] Status updated to APPROVED

**Next:** Phase 10

---

### PHASE 10: External Integration Service Scope
**Folder:** `10. External Integration Service Scope/`

**Goal:** Identify which services are needed from each provider.

| Item | Value |
|------|-------|
| **Prompt** | `10. External Integration Service Scope/EXTERNAL-INTEGRATION-SERVICE-SCOPE-PROMPT.md` |
| **Template** | `10. External Integration Service Scope/SERVICE-SCOPE-TEMPLATE.md` |
| **Input** | Approved PROVIDER-NOTES.md |
| **Output** | `{{project}}/integration-{{provider}}/SERVICE-SCOPE.md` |
| **Approval** | User approves service list |

**Process:** Run once per provider.

**Skip if:** No external integrations.

**Checklist:**
- [ ] For each provider:
  - [ ] Identify required services
  - [ ] User confirms/adds services
  - [ ] User approves
  - [ ] Status updated to APPROVED

**Next:** Phase 11

---

### PHASE 11: External Integration Service Build
**Folder:** `11. External Integration Service Build/`

**Goal:** Create service folders and SERVICE-NOTES.md files.

| Item | Value |
|------|-------|
| **Prompt** | `11. External Integration Service Build/EXTERNAL-INTEGRATION-SERVICE-BUILD-PROMPT.md` |
| **Template** | `11. External Integration Service Build/SERVICE-NOTES-TEMPLATE.md` |
| **Input** | Approved SERVICE-SCOPE.md |
| **Output** | `{{project}}/integration-{{provider}}/{{service}}/SERVICE-NOTES.md` |
| **Approval** | User approves all service folders |

**Process:** Run once per provider.

**Skip if:** No external integrations.

**Checklist:**
- [ ] For each provider:
  - [ ] Create folder for each service
  - [ ] Create SERVICE-NOTES.md in each
  - [ ] User approves
  - [ ] Status updated to APPROVED

**Next:** Phase 12

---

### PHASE 12: Pre-Pass 0 Checklist
**Folder:** `12. PrePass Check/`

**Goal:** Verify everything is built and approved before starting Pass 0.

| Item | Value |
|------|-------|
| **Prompt** | `12. PrePass Check/PRE-PASS-0-CHECKLIST-PROMPT.md` |
| **Template** | `12. PrePass Check/PRE-PASS-0-CHECKLIST-TEMPLATE.md` |
| **Input** | Entire project folder structure |
| **Output** | `{{project}}/admin/PRE-PASS-0-CHECKLIST.md` |
| **Approval** | All items checked, status READY FOR PASS 0 |

**Checklist:**
- [ ] Run prompt
- [ ] Verify all foundation docs
- [ ] Verify all systems built
- [ ] Verify all subsystems built (or N/A)
- [ ] Verify all providers built (or N/A)
- [ ] Verify all services built (or N/A)
- [ ] Status: READY FOR PASS 0
- [ ] User approves

**Next:** Pass 0 (Coverage Reviews) — see `0. Admin/0b. PassPhases 0-4/`

---

## Expected Project Folder Structure After Build

```
{{project}}/
│
├── admin/
│   ├── PROJECT-OVERVIEW.md
│   ├── PROJECT-INTENTION.md
│   ├── SYSTEMS-CLARIFICATION.md
│   ├── SYSTEMS-PREBUILD-CHECK.md
│   ├── EXTERNAL-INTEGRATIONS-REGISTER.md
│   └── PRE-PASS-0-CHECKLIST.md
│
├── {{system-name}}/
│   ├── SYSTEM-NOTES.md
│   ├── SUBSYSTEMS-CLARIFICATION.md (if has subsystems)
│   ├── SUBSYSTEM-PREBUILD-CHECK.md (if has subsystems)
│   └── {{subsystem-name}}/
│       └── SUBSYSTEM-NOTES.md
│
├── {{system-name}}/
│   └── SYSTEM-NOTES.md
│
├── integration-{{provider}}/
│   ├── PROVIDER-NOTES.md
│   ├── SERVICE-SCOPE.md
│   ├── {{service-name}}/
│   │   └── SERVICE-NOTES.md
│   └── {{service-name}}/
│       └── SERVICE-NOTES.md
│
└── integration-{{provider}}/
    ├── PROVIDER-NOTES.md
    ├── SERVICE-SCOPE.md
    └── {{service-name}}/
        └── SERVICE-NOTES.md
```

---

## File Index by Phase

| Phase | Folder | Files |
|-------|--------|-------|
| 1 | `1. Project Overview/` | PROJECT-OVERVIEW-PROMPT.md, PROJECT-OVERVIEW-TEMPLATE.md |
| 2 | `2. Project Intention/` | PROJECT-INTENTION-PROMPT.md, PROJECT-INTENTION-TEMPLATE.md |
| 3 | `3. System Identification/` | SYSTEMS-IDENTIFICATION-PROMPT.md, SYSTEMS-CLARIFICATION-TEMPLATE.md, EXTERNAL-INTEGRATIONS-REGISTER-TEMPLATE.md |
| 4 | `4. Systems Pre-Build Check/` | SYSTEMS-PREBUILD-CHECK-PROMPT.md, SYSTEMS-PREBUILD-CHECK-TEMPLATE.md |
| 5 | `5. Systems Build/` | SYSTEM-BUILD-PROMPT.md, SYSTEM-NOTES-TEMPLATE.md |
| 6 | `6. Subsystems Identification/` | SUBSYSTEM-IDENTIFICATION-PROMPT.md, SUBSYSTEMS-CLARIFICATION-TEMPLATE.md |
| 7 | `7. Subsystems Pre-Build Check/` | SUBSYSTEM-PREBUILD-CHECK-PROMPT.md, SUBSYSTEM-PREBUILD-CHECK-TEMPLATE.md |
| 8 | `8. Subsystem Build/` | SUBSYSTEM-BUILD-PROMPT.md, SUBSYSTEM-NOTES-TEMPLATE.md |
| 9 | `9. External Integration Provider Build/` | EXTERNAL-INTEGRATION-PROVIDER-BUILD-PROMPT.md, PROVIDER-NOTES-TEMPLATE.md |
| 10 | `10. External Integration Service Scope/` | EXTERNAL-INTEGRATION-SERVICE-SCOPE-PROMPT.md, SERVICE-SCOPE-TEMPLATE.md |
| 11 | `11. External Integration Service Build/` | EXTERNAL-INTEGRATION-SERVICE-BUILD-PROMPT.md, SERVICE-NOTES-TEMPLATE.md |
| 12 | `12. PrePass Check/` | PRE-PASS-0-CHECKLIST-PROMPT.md, PRE-PASS-0-CHECKLIST-TEMPLATE.md |

---

## What's Next After Pre-Build?

When Phase 12 shows **READY FOR PASS 0**, proceed to:

**Location:** `0. Admin/0b. PassPhases 0-4/`

| Pass | Purpose |
|------|---------|
| Pass 0 | Coverage Reviews — assess completeness |
| Pass 1 | Grounding — establish system boundaries |
| Pass 2 | (TBD) |
| Pass 3 | (TBD) |
| Pass 4 | (TBD) |

---

## Notes

- Always get user approval before proceeding to next phase
- If a phase has issues, resolve before continuing
- External integration phases (9-11) can be skipped if no integrations
- Subsystem phases (6-8) run per-system, not once globally
- Keep this guide updated as the process evolves