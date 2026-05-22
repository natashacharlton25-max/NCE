# Internal Integrations Map

## Project
NCE-V2 (TypeScript on Cloudflare Workers)

---

## Summary

| Metric | Value |
|--------|-------|
| Total Systems | 27 (per FileTree-v2.md) |
| Total Internal Integrations | {{n}} |
| Systems with no integrations | {{n}} |
| Library accesses (via `library/Librarian`) | {{n}} |
| Boundary violations flagged | {{n}} |

---

## Integration Matrix

(✓ = row sends to column)

|  | System A | System B | System C | ... |
|--|----------|----------|----------|-----|
| **System A** | — | | | |
| **System B** | | — | | |
| **System C** | | | — | |

---

## Integration Details

### {{Source System}} → {{Destination System}}

| Field | Value |
|-------|-------|
| **Source** | {{source system}} |
| **Destination** | {{destination system}} |
| **Direction** | One-way / Bi-directional |
| **Binding Type** | service binding / D1 / R2 / KV / DO / Queue / in-Worker import |
| **D1 Binding Name (if D1)** | {{NAME}} or N/A |
| **Data/Responsibility** | {{what crosses the boundary}} |
| **Output Form** | JSON / rendered artefact / library entry / metadata / binary asset |
| **Initiator** | {{who initiates}} |
| **Frequency** | On-demand / Scheduled / Event-driven |
| **Output-Boundary Respected?** | Yes / Flag — see notes |
| **Library Access via Librarian?** | Yes / N/A / Flag (direct D1) |

**Purpose:**
(Why does this integration exist?)

**Notes:**
(Any clarifications or concerns)

---

(Repeat for each integration)

---

## Output-Boundary Audit

| Pair | Issue | Action |
|------|-------|--------|
| {{source → dest}} | e.g. JSON-emitter passing rendered HTML | Flag for Pass 1 grounding fix |

---

## Library Access Audit

| Pair | Issue | Action |
|------|-------|--------|
| {{source → dest}} | e.g. direct D1 binding from outside library/ | Flag for re-routing through `library/Librarian` |

---

## Systems With No Internal Integrations

| System | Notes |
|--------|-------|
| {{system}} | {{why no integrations — standalone? external only?}} |

---

## Unclear Integrations (TBD — Needs Clarification)

| Source | Destination | Question | Logged in DECISION-NOTES? |
|--------|-------------|----------|----------------------------|
| {{system}} | {{system}} | {{what needs clarification}} | Yes / No |

---

## Integration Graph

```
(ASCII representation)
```

---

## Worker Topology Note

`platform` Worker groups `services` + `system` + `state` + `library` — their inter-dependencies are in-Worker imports, not cross-Worker service bindings. Other 23 systems each have their own Worker; cross-system flows use service bindings.

---

## Notes

(Observations, patterns, or concerns)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Phase: 13d (Pass 0d)
Owner: Claude | Human
---
