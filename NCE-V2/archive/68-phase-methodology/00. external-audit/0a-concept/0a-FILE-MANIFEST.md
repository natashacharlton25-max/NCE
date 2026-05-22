# 0a Concept to PassPhase File Manifest

---
Section: 0a Concept to PassPhase (Phases 1-12)
Purpose: Complete list of files available for audit review
---

## Dependency Bundles

| Bundle Name | Request | You Get |
|-------------|---------|---------|
| **Project Foundation** | "Project foundation bundle" | PROJECT-OVERVIEW.md, PROJECT-INTENTION.md |
| **Systems** | "Systems bundle" | SYSTEM-NOTES.md, all SYSTEM-*.md files |
| **Subsystems** | "Subsystems bundle" | SUBSYSTEM-NOTES.md, all SUBSYSTEM-*.md files |
| **Integrations** | "Integrations bundle" | PROVIDER-NOTES.md, SERVICE-SCOPE.md, SERVICE-NOTES.md |
| **Gate** | "Gate bundle" | PREPASS-CHECKLIST.md |

---

## Phase 1-2: Project Definition

| File | Location | Purpose |
|------|----------|---------|
| PROJECT-OVERVIEW.md | /1. Project Overview/ | Project scope and description |
| PROJECT-INTENTION.md | /2. Project Intention/ | Success criteria and constraints |

---

## Phase 3-5: Systems

| File | Location | Purpose |
|------|----------|---------|
| SYSTEM-NOTES.md | /3. System Identification/ | Initial system identification |
| SYSTEMS-CLARIFICATION.md | /4. Systems Pre-Build Check/ | Clarification questions |
| SYSTEM-{{name}}.md | /5. Systems Build/ | Per-system documentation |

**Systems in this project:**
| System | File |
|--------|------|
| {{system_name}} | SYSTEM-{{name}}.md |

---

## Phase 6-8: Subsystems

| File | Location | Purpose |
|------|----------|---------|
| SUBSYSTEM-NOTES.md | /6. Subsystems Identification/ | Initial subsystem identification |
| SUBSYSTEMS-CLARIFICATION.md | /7. Subsystems Pre-Build Check/ | Clarification questions |
| SUBSYSTEM-{{name}}.md | /8. Subsystem Build/ | Per-subsystem documentation |

**Subsystems in this project:**
| Subsystem | Parent System | File |
|-----------|---------------|------|
| {{subsystem_name}} | {{system}} | SUBSYSTEM-{{name}}.md |

---

## Phase 9-11: External Integrations

| File | Location | Purpose |
|------|----------|---------|
| PROVIDER-NOTES.md | /9. External Integration Provider Build/ | Provider identification |
| SERVICE-SCOPE.md | /10. External Integration Service Scope/ | Service scope per provider |
| SERVICE-NOTES.md | /11. External Integration Service Build/ | Service details |

**Integrations in this project:**
| Provider | Services |
|----------|----------|
| {{provider}} | {{service_list}} |

---

## Phase 12: Gate

| File | Location | Purpose |
|------|----------|---------|
| PREPASS-CHECKLIST.md | /12. PrePass Check/ | Final verification before 0b |

---

## Priority Files for Audit

1. **PROJECT-OVERVIEW.md** - Project scope
2. **PROJECT-INTENTION.md** - Success criteria
3. **SYSTEM-NOTES.md** - System structure
4. **PREPASS-CHECKLIST.md** - Gate status
5. **SUBSYSTEM-NOTES.md** - Subsystem structure

---

## File Count Summary

| Category | Count |
|----------|-------|
| Project definition | 2 |
| System files | 2 + {{n}} per system |
| Subsystem files | 2 + {{n}} per subsystem |
| Integration files | 3 |
| Gate files | 1 |
| **TOTAL** | **~10-20** |

---

*End of File Manifest*
