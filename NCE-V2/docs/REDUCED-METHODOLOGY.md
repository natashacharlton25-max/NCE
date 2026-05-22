# NCE-V2 Reduced Methodology

**Status:** Approved
**Created:** 2026-05-22
**Replaces (in practice):** 68-phase methodology in `NCE-V2/NCE V2.0 Spec & Build/` (preserved as reference)

---

## Why this exists

The 68-phase methodology in `NCE-V2/NCE V2.0 Spec & Build/` was designed when AI handled less context and was more prone to scope creep. Modern AI is better at both. This reduced methodology preserves the human approval gates and per-subsystem discipline but cuts ~88% of the artefact overhead.

---

## The 5 Stages

| Stage | Scope | Output | Gate |
|---|---|---|---|
| 1. **Project Frame** | One-time | Validates existing intent + tree + stack | Project-wide approval |
| 2. **Component Specs** | Per system + per subsystem + per library + per integration service | One comprehensive doc per component | Per-component approval; cross-cutting audit at end |
| 3. **Project-Wide Spec** | One-time | `PROJECT-SPEC.md` + `platform/WORKER.md` | Pre-implementation go/no-go |
| 4. **Implementation** | Per Worker | Code + tests | Per-Worker code review |
| 5. **Release** | One-time | Deployed production | Pre-deploy + post-deploy gates |

---

## Stage 1 — Project Frame

**Purpose:** Confirm the project foundation is locked.

**Inputs (already exist):**
- `NCE-V2/Project-Intent.md`
- `NCE-V2/FileTree-v2.md`
- `NCE-V2/STACK-AND-RUNTIME.md`

**Output:** `NCE-V2/admin/PROJECT-FRAME.md` — one-page validation record + links to the three source docs.

**Gate:** Human signs off; system list and stack don't change without returning here.

---

## Stage 2 — Component Specs

Four artefact types. Every subsystem gets its own doc.

### 2a. System Spec — 27 docs

`NCE-V2/specs/<system>/SPEC.md`

Sections: Identity, Purpose, System Scope, Output Form, Output Destination, Library Ownership, **Subsystem Inventory**, System-Wide Rules, Inputs, Outputs, Dependencies, Worker Section (for own-Worker systems), LOC Total Estimate, Open Questions, Hardening Self-Check.

### 2b. Subsystem Spec — ~226 docs

`NCE-V2/specs/<system>/<subsystem>/SPEC.md`

21 sections covering: identity, scope, boundaries, flow, output form, storage, D1 binding, inputs/outputs (with binding types + TS types), dependencies (Hard/Soft/Build/Runtime/External), forbidden deps, behaviour, public functions (TS signatures), types, errors (codes + categories + resilience patterns), config (Worker bindings), examples, rules/non-negotiables, LOC estimate + size band, open questions, hardening self-check.

### 2c. Library Spec — ~16 docs

`NCE-V2/specs/<owning-system>/libraries/<library>.library.md`

Uses existing `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 (D1 binding, tables, entry schema, validation, write policy, migration). Already drafted.

### 2d. Integration Service Spec — ~30 docs

`NCE-V2/specs/integrations/<provider>/<service>/SPEC.md`

17 sections: identity, direction (inbound/outbound/renderer), purpose, scope, consuming systems, auth (Worker secret binding), API surface, our wrapper, field mapping, errors, constraints, failure modes, sink targets (assets/R2 for binaries; library D1 for metadata), examples, rules, LOC estimate, hardening self-check.

### Per-component process

1. For each system (A–Z):
   - Draft `SPEC.md` for the system
   - Draft `SPEC.md` for each subsystem
   - Draft `.library.md` for each library it owns
2. For each integration provider (A–Z):
   - Draft `SPEC.md` for each service
3. **Per-component approval gate** after each draft
4. **Cross-cutting audit** at end of stage:
   - No overlapping subsystem responsibilities
   - No circular dependencies (Worker-level or subsystem-level)
   - Output-boundary respected
   - Library access via `library/Librarian`
   - TS types consistent across boundaries
   - Error codes unique

---

## Stage 3 — Project-Wide Spec

Cross-cutting concerns consolidated. Two artefacts:

### `NCE-V2/specs/PROJECT-SPEC.md`

Sections: API Surface, Error Codes Registry, Project-Wide Schemas, Conventions, Security Standards, Tech Stack (locked), Repo Structure, Environment, Dependency Map, **Worker Topology**, Implementation Plan.

### `NCE-V2/specs/platform/WORKER.md`

Multi-system Worker rules: which 4 systems share it (services + system + state + library), why grouped, shared bindings, in-Worker module structure, cold-start considerations, coordination patterns.

For the 23 own-Worker systems, Worker rules live inside their system SPEC.md (Worker section).

**Gate:** Go/no-go for implementation.

---

## Stage 4 — Implementation

Per Worker (foundation first):
1. Scaffold per repo structure
2. Implement subsystems per their SPEC.md FUNCTIONS + TYPES + BEHAVIOUR
3. Write tests alongside (vitest + miniflare)
4. Verify against SPEC.md
5. Code review gate

Order: `platform` Worker first, then others per dependency map.

---

## Stage 5 — Release

CI/CD setup → performance + security audit → pre-deploy verification → production deployment (Golden Hour) → post-deploy verification → stabilization + operations handoff.

---

## Worker rules — where they live

| Worker | Doc | Stage |
|---|---|---|
| `platform` (4 systems together) | `NCE-V2/specs/platform/WORKER.md` | 3 |
| Own-Worker systems (23 others) | Worker section inside system SPEC.md | 2 |

---

## Artefact count

| Stage | Docs |
|---|---|
| 1. Project Frame | 1 |
| 2a. System specs | 27 |
| 2b. Subsystem specs | ~226 |
| 2c. Library specs | ~16 |
| 2d. Integration service specs | ~30 |
| 3. PROJECT-SPEC + platform/WORKER | 2 |
| 4. Code + tests | — |
| 5. Release docs | ~8 |
| **Total methodology docs** | **~310** |

vs the 68-phase methodology's ~2,500+ artefacts. **~88% reduction.**

---

## Methodology files (the prompts + templates themselves)

| File | Stage | Purpose |
|---|---|---|
| `methodology/STAGE-1-PROMPT.md` + `PROJECT-FRAME-TEMPLATE.md` | 1 | Validation |
| `methodology/STAGE-2-PROMPT.md` + `SYSTEM-SPEC-TEMPLATE.md` + `SUBSYSTEM-SPEC-TEMPLATE.md` + `INTEGRATION-SERVICE-SPEC-TEMPLATE.md` (LIBRARY-TEMPLATE v2.0.0 already exists) | 2 | Per-component specs |
| `methodology/STAGE-3-PROMPT.md` + `PROJECT-SPEC-TEMPLATE.md` + `PLATFORM-WORKER-TEMPLATE.md` | 3 | Project-wide |
| `methodology/STAGE-4-PROMPT.md` | 4 | Implementation |
| `methodology/STAGE-5-PROMPT.md` + sub-templates | 5 | Release |

~10 new methodology files (vs ~200 in the 68-phase).

---

## Migration

Old 68-phase methodology preserved at `NCE-V2/NCE V2.0 Spec & Build/` — kept as reference rather than deleted.

New 5-stage methodology lives at `NCE-V2/methodology/`.

If executing Stage 2 produces information that wasn't captured by Stage 1, return to Stage 1 and update. Same pattern at every gate.

---

## NCE-V2-specific rules (apply throughout)

- **Output-boundary:** NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer. Web rendering is Astro's. PDF/DOCX/email/marks rendering is NCE-V2's.
- **Library access:** all reads via `library/Librarian`; only library write-owner module writes its library.
- **Storage model:** D1 for libraries (text + JSON); R2 for binaries; KV for cache/config; DO for stateful coordination; Vectorize for embeddings.
- **2000 LOC band:** per runtime `.ts` file, excluding `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments. TS verbosity 1.3–1.5× Python.
- **TypeScript strict:** no `any` without justification. ESM only.
- **Worker secrets:** never in repo; always via `wrangler secret`.
