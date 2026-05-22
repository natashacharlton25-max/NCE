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

1. **Project-Intent.md** — Project intention
2. **CLAUDE.md** — Pass methodology
3. **STACK-AND-RUNTIME.md** — Worker boundaries; D1 bindings; cross-Worker communication patterns
4. **FileTree-v2.md** — Canonical 27-system list
5. **NCE-V2/docs/templates/LIBRARY-TEMPLATE.md v2.0.0** — D1-based library access patterns

> **TODO (Path resolution):** See 13a.

---

## RUNTIME CONTEXT

- **Inter-system communication** on Cloudflare Workers happens via:
  - **Service bindings** (Worker → Worker, in-region, sub-ms)
  - **D1 bindings** (Worker → D1 SQLite for library reads/writes)
  - **R2 bindings** (Worker → R2 for asset blobs)
  - **KV bindings** (Worker → KV for cache/config)
  - **Durable Objects** (Worker → DO for stateful coordination)
  - **Queues** (Worker → Queue for async/decoupled)
- Treat each binding type as a distinct integration kind in the mapping

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**

When mapping internal flows, flag any system pair where:
- A system passes rendered web output to another internal system (suggests `website/` produced HTML instead of JSON — boundary violation)
- A renderer is invoked from a system that should produce structured JSON only
- A library access bypasses `library/Librarian` (suggests direct DB binding from outside the library system — flag for review)

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 13d as **ACTIVE**

### Step 2 — Identify Internal Integrations

For each of the 27 systems in `FileTree-v2.md`:
- What other systems does it **SEND** data to?
- What other systems does it **RECEIVE** data from?
- What kind of binding mediates the flow (service / D1 / R2 / KV / DO / Queue)?
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
- If blocked: log in `PASS-DECISION-NOTES.md`; consult PASS-RESTART-RULES.md

---

## MANDATORY RULES

- Do **NOT** invent systems — use only the 27 in FileTree-v2.md
- Do **NOT** define contracts yet — only identify connections
- Do **NOT** add implementation details
- If a connection is unclear, flag it as "TBD — needs clarification"
- Apply the output-boundary rule to every cross-system flow
- Do **NOT** self-assign the status "Approved"

---

## OUTPUT LOCATION

```
NCE-V2/specs/INTERNAL-INTEGRATIONS.md
```

> **TODO (Output destination):** Same as 13a/c — confirm `NCE-V2/specs/` vs `NCE-V2/admin/` for project-wide docs.

---

## END CONDITION

PASS 0d is COMPLETE only when:
- [ ] All 27 systems' inbound/outbound flows identified
- [ ] Integration map created
- [ ] Each flow has a binding type recorded
- [ ] Output-boundary rule applied; any violations flagged
- [ ] INTERNAL-INTEGRATIONS.md approved
- [ ] PASS-PROGRESS.md updated: Phase 13d marked **COMPLETE**

**Next:** Proceed to Phase 14 (Pass 1 — Grounding) — or Phase 13c if substage order is resolved that way.

---

## TEMPLATE

- [INTERNAL-INTEGRATIONS-TEMPLATE.md](./INTERNAL-INTEGRATIONS-TEMPLATE.md)

---

## INPUTS

From Phase 5 (B's chain): All SYSTEM-NOTES.md
From Phase 8 (B's chain): All SUBSYSTEM-NOTES.md
From Phases 13a–c: Coverage reviews (for context)

> **TODO (Inputs):** Phases 5/8 outputs don't exist for NCE-V2. Substitute: FileTree-v2.md `Used By` and `Depends On` columns from `_LIBRARIES.md`. Confirm.

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Binding-type categorisation — does the service / D1 / R2 / KV / DO / Queue list match how you want flows classified?
- Output-boundary integration into the flow map — useful or redundant?
- May I proceed with 14–17?
