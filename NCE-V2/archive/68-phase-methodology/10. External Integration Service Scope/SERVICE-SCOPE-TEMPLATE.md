# Service Scope — {{Provider Name}}

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Provider
{{Provider Name}}

## Provider Purpose Reminder
(One sentence from PROVIDER-NOTES.md.)

---

## Proposed Services

### {{Service Name}}

| Field | Value |
|-------|-------|
| **Service** | {{e.g., Google Drive API v3}} |
| **Direction** | Inbound source / Outbound receiver / Downstream renderer / Combined |
| **Output Form (if outbound)** | Rendered HTML / PDF blob / DOCX blob / JSON / Other |
| **Asset Sink (if inbound binary)** | R2 via `assets/` system |
| **Library Sink (if inbound metadata)** | D1 binding `{{NAME}}` via `library/` |
| **Documentation** | {{URL}} |

**Purpose:** (One sentence — what capability this service provides.)

**Why Needed:**
- How this service supports the project
- Which internal system(s) will use it

**Authentication:**
- Inherits provider auth / Service-specific auth: {{details}}
- Worker secret binding name: `{{SECRET_NAME}}`

---

### {{Next Service Name}}

(repeat for each service)

---

## Service Summary

| Service | Direction | Purpose | Consuming System(s) |
|---------|-----------|---------|---------------------|
| {{service}} | In / Out / Renderer | {{brief}} | {{system}} |

---

## Services Explicitly NOT Needed

| Service | Reason |
|---------|--------|
| | |

---

## Notes
(Dependencies between services, considerations.)

---

## Next Step
When APPROVED, proceed to: **Phase 11 — External Integration Service Build**
