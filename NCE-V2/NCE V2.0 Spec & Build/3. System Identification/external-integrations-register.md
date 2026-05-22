# External Integrations Register

---
Status: DRAFT | APPROVED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Human
---

## Project Context
(One short paragraph summarising the project intent.)

---

## Identified External Integrations

### {{Capability Name}}

**Capability Needed:** (One sentence describing what external capability is required.)

**Why Needed:**
- How this capability supports the project intent
- Which internal system(s) will use this integration

**Provider:** TBD — User to specify

**Provider Options:** (Known alternatives)
- Option A
- Option B
- Other

---

(repeat for each external integration)

---

## Integration Summary

| # | Capability | Provider | Consuming System(s) | Status |
|---|------------|----------|---------------------|--------|
| 1 | {{capability}} | TBD | {{system}} | Awaiting provider |
| 2 | {{capability}} | TBD | {{system}} | Awaiting provider |

---

## No External Integrations?

If the project has no external integrations:
- Write "None identified" in the summary table
- Mark status as APPROVED
- Proceed directly — external integration build phase will be skipped

---

## Notes
(Any assumptions, questions, or considerations about external dependencies.)

---

## Dependency
This document requires approved: **Project Overview** + **Project Intention**

## Next Step
When providers are specified and document is APPROVED:
- Store in: `{{project-name}}/admin/EXTERNAL-INTEGRATIONS-REGISTER.md`
- External integrations will be built **after** Systems and Subsystems are complete
- Build order: Systems → Subsystems → External Integrations