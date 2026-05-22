# Subsystems Clarification

---
Status: DRAFT | APPROVED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Parent System
{{System Name}}

## System Intent Reminder
(One short paragraph restating the system's responsibility. Pull from SYSTEM-NOTES.md Purpose and Intent.)

---

## Proposed Subsystems

### {{Subsystem Name}}

**Purpose:** (One sentence describing the internal responsibility this subsystem owns.)

**Justification:**
- Why this responsibility must be separated from other subsystems
- How this separation helps keep each unit under ~1,500 LOC

---

### {{Next Subsystem Name}}

(repeat for each subsystem)

---

## Subsystem Count Summary

| Parent System | Subsystem Count | Expected LOC per Subsystem |
|---------------|-----------------|---------------------------|
| {{System Name}} | {{n}} | ~{{estimate}} |

---

## Dependency
This document requires approved: **SYSTEM-NOTES.md** for {{System Name}}

## Next Step
When this document is APPROVED, proceed to: **Subsystem Pre-build Check**