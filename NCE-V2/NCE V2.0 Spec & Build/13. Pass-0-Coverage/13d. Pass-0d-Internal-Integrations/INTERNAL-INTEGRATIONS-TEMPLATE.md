# Internal Integrations Map

## Project
{{Project Name}}

---

## Summary

| Metric | Value |
|--------|-------|
| Total Systems | {{n}} |
| Total Internal Integrations | {{n}} |
| Systems with no integrations | {{n}} |

---

## Integration Matrix

(✓ = row sends to column)

|  | System A | System B | System C | System D |
|--|----------|----------|----------|----------|
| **System A** | — | | | |
| **System B** | | — | | |
| **System C** | | | — | |
| **System D** | | | | — |

---

## Integration Details

### {{Source System}} → {{Destination System}}

| Field | Value |
|-------|-------|
| **Source** | {{source system}} |
| **Destination** | {{destination system}} |
| **Direction** | One-way / Bi-directional |
| **Data/Responsibility** | {{what crosses the boundary}} |
| **Initiator** | {{who initiates}} |
| **Frequency** | On-demand / Scheduled / Event-driven |

**Purpose:**
(Why does this integration exist?)

**Notes:**
(Any clarifications or concerns)

---

(Repeat for each integration)

---

## Systems With No Internal Integrations

| System | Notes |
|--------|-------|
| {{system}} | {{why no integrations — standalone? external only?}} |

---

## Unclear Integrations

| Source | Destination | Question |
|--------|-------------|----------|
| {{system}} | {{system}} | {{what needs clarification}} |

---

## Integration Graph

```
(ASCII representation)

┌──────────┐      ┌──────────┐
│ System A │─────▶│ System B │
└──────────┘      └────┬─────┘
                       │
                       ▼
                 ┌──────────┐
                 │ System C │
                 └──────────┘
```

---

## Notes

(Observations, patterns, or concerns)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Phase: 13d (Pass 0d)
Owner: Claude | Human
---
