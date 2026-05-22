# PHASE 16: PASS 3 — DEPENDENCIES

---
Phase: 16
Pass: 3
Name: Dependencies
Location: NCE-V2/NCE V2.0 Spec & Build/16. Pass-3-Dependencies/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PASS3PROMPT.MD (A's Pass-3 skeleton — relevant content absorbed; NOTE: A's Pass-3 covered full specs/schemas, which in B's chain maps to Phases 21–22, not 16. B's Phase 16 is dependency mapping.)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are mapping dependencies between all NCE-V2 components and determining implementation order.

This phase takes the contracts (from Pass 2) and produces a dependency graph that shows what depends on what, identifies problems, and determines build order.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md) — pass methodology
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker grouping; binding types; cold-start ordering
4. [FileTree-v2.md](../../FileTree-v2.md)
5. Pass 2 contracts under `NCE-V2/specs/<system>/CONTRACT.md` (primary input)
6. Pass 1 grounding under `NCE-V2/specs/<system>/SYSTEM.md` (for forbidden dependencies)
7. `NCE-V2/pass-0/INTERNAL-INTEGRATIONS.md` — Pass 0d output (cross-system flow inventory)

---

## RUNTIME CONTEXT

- **Binding types as dependency edges:** service binding (Worker → Worker), D1 binding (Worker → D1), R2, KV, DO, Queue, Vectorize
- **Cross-Worker calls** are remote-procedure-call-like (sub-ms in-region) — count as dependencies
- **In-Worker imports** (TypeScript imports within the same Worker bundle) — count as code dependencies
- **`platform` Worker** groups `services` + `system` + `state` + `library` — these systems share runtime; their inter-dependencies are in-Worker, not cross-Worker

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

A dependency that crosses the JSON/rendered boundary in the wrong direction is a violation:
- A JSON-emitting system (e.g. `website/`) should NOT depend on `renderers/`
- A renderer should depend on its source content system (consumer-of-JSON), not the reverse
- Flag any dependency edge that gets the direction backwards

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 16 as **ACTIVE**

> **Logging rule:** Circular dependencies, forbidden dependencies, and boundary violations all get logged in `NCE-V2/admin/PASS-DECISION-NOTES.md`.

### Step 2 — Build Dependency Graph
- List all 27 systems + their subsystems + integration providers/services
- For each component, list what it depends on
- Tag each dependency edge with binding type (service / D1 / R2 / KV / DO / Queue / in-Worker import)
- Tag each dependency edge with dependency type (Hard / Soft / Build / Runtime / External)

### Step 3 — Identify Problems
- **Circular dependencies** — flag and refuse to proceed
- **Missing dependencies** — flag (something a contract requires but no source declared)
- **Forbidden dependencies** — flag (something a SYSTEM.md explicitly forbade in Pass 1)
- **Output-boundary violations** — flag (wrong-direction edges per the boundary rule)
- **Library access bypassing `library/Librarian`** — flag (direct D1 binding from outside `library/`)

### Step 4 — Determine Implementation Order
- Identify leaf nodes (no dependencies, or only external dependencies)
- Build order from leaves up; `platform` Worker typically goes first
- Identify critical path
- Identify what can be parallelised

---

## WHAT THE DEPENDENCY MAP SHOWS

| Question | Map Section |
|----------|-------------|
| What depends on what? | Dependency Graph |
| Through what binding? | Binding-Type Tagged Edges |
| Are there circular deps? | Cycle Analysis |
| What can be built first? | Leaf Nodes |
| What must be ordered? | Implementation Order |
| What can be parallelised? | Parallel Groups |
| What's the critical path? | Critical Path |
| Any boundary violations? | Output-Boundary Audit |
| Any library bypasses? | Library Access Audit |

---

## DEPENDENCY TYPES

| Type | Meaning |
|------|---------|
| **Hard** | Cannot function without this |
| **Soft** | Can function in degraded mode without this |
| **Build** | Needed at compile time |
| **Runtime** | Needed at request time |
| **External** | Third-party service |

---

## MANDATORY RULES

- Do **NOT** invent dependencies not in contracts
- Do **NOT** ignore circular dependencies — they MUST be resolved
- Do **NOT** ignore forbidden dependencies — they MUST be flagged
- Do **NOT** ignore output-boundary violations — they MUST be flagged
- If order cannot be determined, flag the blocker and log
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## CIRCULAR DEPENDENCY HANDLING

1. Document the cycle clearly
2. Log in `PASS-DECISION-NOTES.md`
3. Consult `PASS-RESTART-RULES.md` (usually restart at Pass 1 or Pass 2)
4. Do NOT proceed without resolution

---

## OUTPUT LOCATION

```
NCE-V2/specs/DEPENDENCY-MAP.md
```

Single project-wide document. Pass 3+ outputs live in `NCE-V2/specs/` (Pass 0 work stays in `NCE-V2/pass-0/`).

---

## END CONDITION

- [ ] Dependency graph complete for all 27 systems
- [ ] Every edge tagged with binding type and dependency type
- [ ] No unresolved circular dependencies
- [ ] No forbidden dependencies violated
- [ ] No output-boundary violations
- [ ] No library access bypasses
- [ ] Implementation order determined
- [ ] `DEPENDENCY-MAP.md` approved
- [ ] `PASS-PROGRESS.md` updated: Phase 16 marked **COMPLETE**

**Next:** Proceed to Phase 17 (Pass 4 — Implementation Planning)

---

## PRE-SUBMISSION SELF-CHECK

- [ ] All Pass 2 contracts consulted
- [ ] Every edge has binding type + dependency type
- [ ] Output-boundary audit performed
- [ ] Library access audit performed (`library/Librarian` mediates all reads)
- [ ] No circular dependencies
- [ ] No forbidden dependencies
- [ ] Implementation order accounts for `platform` Worker priority
- [ ] Anything noteworthy logged in `PASS-DECISION-NOTES.md`
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")

---

## TEMPLATES (enriched with project-specific columns)

- [DEPENDENCY-MAP-TEMPLATE.md](./DEPENDENCY-MAP-TEMPLATE.md)

The template carries columns for binding type and output-boundary audit — fill per the rules in this prompt.

---

## INPUTS

From Pass 2: All CONTRACT.md files (under `NCE-V2/specs/`)
From Pass 1: All grounding docs (for forbidden dependencies)
From Pass 0d: `NCE-V2/pass-0/INTERNAL-INTEGRATIONS.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- All prior TODOs resolved per user decisions on 2026-05-22.
- May this draft be promoted to "Approved"?
