# PHASE 13d: PASS 0d — INTERNAL INTEGRATIONS IDENTIFICATION

---
Phase: 13d
Pass: 0d
Name: Internal Integrations Identification
Location: NCE-V2/NCE V2.0 Spec & Build/13. Pass-0-Coverage/13d. Pass-0d-Internal-Integrations/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are identifying which NCE-V2 systems need to communicate with each other.

This phase maps system-to-system integrations BEFORE grounding and contracts. **Identification only — no contract definition.**

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../../Project-Intent.md)
2. [CLAUDE.md](../../../../CLAUDE.md) — pass methodology
3. [STACK-AND-RUNTIME.md](../../../STACK-AND-RUNTIME.md) — Worker boundaries; D1 bindings; cross-Worker communication patterns
4. [FileTree-v2.md](../../../FileTree-v2.md) — canonical 27-system list
5. [LIBRARY-TEMPLATE.md v2.0.0](../../../docs/templates/LIBRARY-TEMPLATE.md) — D1-based library access patterns

---

## RUNTIME CONTEXT

Inter-system communication on Cloudflare Workers happens via:

- **Service bindings** (Worker → Worker, in-region, sub-ms)
- **D1 bindings** (Worker → D1 SQLite for library reads/writes)
- **R2 bindings** (Worker → R2 for asset blobs)
- **KV bindings** (Worker → KV for cache/config)
- **Durable Objects** (Worker → DO for stateful coordination)
- **Queues** (Worker → Queue for async/decoupled)

Treat each binding type as a distinct integration kind in the mapping.

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**

When mapping internal flows, flag any system pair where:

- A system passes rendered web output to another internal system (suggests `website/` produced HTML instead of JSON — boundary violation)
- A renderer is invoked from a system that should produce structured JSON only
- A library access bypasses `library/Librarian` (suggests direct D1 binding from outside `library/`)

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 13d as **ACTIVE**

> **Logging rule:** "TBD — needs clarification" flags get noted in `NCE-V2/admin/PASS-DECISION-NOTES.md`.

### Step 2 — Identify Internal Integrations

For each of the 27 systems in `FileTree-v2.md`:
- What other systems does it **SEND** data to?
- What other systems does it **RECEIVE** data from?
- What binding mediates the flow (service / D1 / R2 / KV / DO / Queue)?
- Direction (one-way / bi-directional)

### Step 3 — Document Each Integration

For each system-to-system connection:
- Source system
- Destination system
- Binding type (per Runtime Context)
- What data/responsibility crosses the boundary
- Whether the boundary respects the output-boundary rule

---

## WHAT WE'RE IDENTIFYING

| Question | Answer |
|----------|--------|
| Which systems talk to each other? | Integration map |
| What flows between them? | Data/responsibility summary |
| Who initiates? | Direction of control |
| One-way or two-way? | Integration type |
| What binding mediates? | service / D1 / R2 / KV / DO / Queue |
| Output-boundary respected? | Yes / Flag |

---

## AFTER COMPLETION

- Pause for user approval
- If approved: update Status to APPROVED, update Version, Last Updated
- If blocked: log in `PASS-DECISION-NOTES.md`; consult `PASS-RESTART-RULES.md`

---

## MANDATORY RULES

- Do **NOT** invent systems — use only the 27 in FileTree-v2.md
- Do **NOT** define contracts yet — only identify connections
- Do **NOT** add implementation details
- If a connection is unclear, flag it as "TBD — needs clarification" and log it
- Apply the output-boundary rule to every cross-system flow
- Do **NOT** self-assign the status "Approved"

---

## OUTPUT LOCATION

```
NCE-V2/pass-0/INTERNAL-INTEGRATIONS.md
```

Project-wide internal-flow map; lives at the top of the `pass-0/` drawer.

---

## END CONDITION

- [ ] All 27 systems' inbound/outbound flows identified
- [ ] Integration map created
- [ ] Each flow has a binding type recorded
- [ ] Output-boundary rule applied; any violations flagged
- [ ] `INTERNAL-INTEGRATIONS.md` approved
- [ ] `PASS-PROGRESS.md` updated: Phase 13d marked **COMPLETE**

**Next:** Proceed to Phase 13c (Pass 0 Checklist — final Pass 0 verification)

---

## TEMPLATE

- [INTERNAL-INTEGRATIONS-TEMPLATE.md](./INTERNAL-INTEGRATIONS-TEMPLATE.md)

---

## INPUTS

From Phase 5: All SYSTEM-NOTES.md
From Phase 8: All SUBSYSTEM-NOTES.md
From Phases 13a–b: Coverage reviews (for context, under `NCE-V2/pass-0/`)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- All prior TODOs resolved per user decisions on 2026-05-22.
- May this draft be promoted to "Approved"?
