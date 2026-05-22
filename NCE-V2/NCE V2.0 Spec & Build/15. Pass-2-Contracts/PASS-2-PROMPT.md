# PHASE 15: PASS 2 — CONTRACTS

---
Phase: 15
Pass: 2
Name: Contracts
Location: NCE-V2/NCE V2.0 Spec & Build/15. Pass-2-Contracts/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PASS2PROMPT.MD (A's Pass-2 skeleton — content absorbed)
  - C:/Users/NCE/prompts/SUBSYSTEMPROMPT.MD (parts — input/output/dependency emphasis absorbed)
  - C:/Users/NCE/prompts/PRE-SPEC-NOTES-TEMPLATE.md (A's pre-spec notes — relevant sections folded in)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are defining how NCE-V2 components communicate with each other.

This phase takes the grounding documents (from Pass 1) and produces formal contracts that define inputs, outputs, data shapes, and error handling at each boundary.

You are NOT implementing the contracts — only defining them.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../../CLAUDE.md) §10:

1. **Project-Intent.md**
2. **CLAUDE.md** — pass methodology, status vocabulary
3. **STACK-AND-RUNTIME.md** — Worker boundaries, binding types
4. **REFINEMENTS.md**
5. **FileTree-v2.md** — canonical subsystem list
6. **NCE-V2/docs/templates/LIBRARY-TEMPLATE.md v2.0.0** — D1 library contracts
7. **NCE-V2/specs/<system>/SYSTEM.md, SUBSYSTEM.md, ADMIN.md** — Pass 1 grounding outputs

> **TODO (Path resolution):** See 13a.

---

## RUNTIME CONTEXT

- **Language:** TypeScript (strict mode)
- **Runtime:** Cloudflare Workers (V8 isolates)
- **Binding types as contract surfaces:** service bindings, D1, R2, KV, DO, Queues, Vectorize
- **External constraints:** 5-min CPU, 128 MB memory per invocation
- **D1 access:** async `await env.{BINDING}.prepare(...).all()` — contracts must reflect async nature
- **Type system:** TypeScript types are the contract artefact — schemas, interfaces, generics

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**

Contracts must reflect this:
- A `website/` system contract MUST output JSON, not rendered HTML
- A `renderers/` system contract MUST consume JSON and output a final rendered artefact (PDF/DOCX/Markdown/HTML for non-Astro use)
- An `email/` system contract MUST output rendered email HTML before handoff to `integrations/EmailitIntegration`
- A library's READ contract is mediated by `library/Librarian`; direct D1 binding access is forbidden outside `library/`

---

## STORAGE MODEL CONTEXT

Per [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md):

- Library entries are JSON in D1's `data TEXT` column
- D1 has no native JSON type — `json_*()` SQL functions used for querying
- `_index` table holds preview; `{items}` holds versioned data; `{items}_latest` view; optional `{items}_fts` FTS5 table
- Drafts: `{items}_drafts` table; promotion = INSERT INTO `{items}` + DELETE FROM `{items}_drafts`

Contracts on library boundaries must declare:
- D1 binding name(s)
- Read vs write side
- Whether the contract returns `_index` rows (preview) or `{items}_latest` rows (full data)

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 15 as **ACTIVE**

### Step 2 — Execute Contracts in Strict Order

#### 2a. SYSTEM CONTRACTS
- Process systems **alphabetically (A–Z)**
- For each system, create **CONTRACT.md** using **CONTRACT-TEMPLATE.md**
- Define inputs, outputs, error contracts

#### 2b. SUBSYSTEM CONTRACTS
- For each system (same order), process its subsystems
- For each subsystem, create **CONTRACT.md** with internal contracts (parent + siblings)

#### 2c. EXTERNAL INTEGRATION CONTRACTS
- For each provider (alphabetically), process its services
- For each service, create **CONTRACT.md** defining the external boundary
- Specify: protocol (REST/GraphQL/WebSocket/binary), auth (API key / OAuth / signed), rate limits, retry policy, cost-per-call (where relevant)

---

## WHAT CONTRACTS DEFINE

| Question | Contract Section |
|----------|------------------|
| What data comes in? | Inputs (with source binding type) |
| What data goes out? | Outputs (with destination binding type) |
| What shape is the data? | Data Definitions (TypeScript types) |
| What errors can occur? | Error Contract (categories, not codes yet) |
| Who validates? | Validation Responsibility |
| What happens on failure? | Failure Behaviour (recoverable / fatal / human escalation) |
| What binding is used? | Binding Type (service / D1 / R2 / KV / DO / Queue) |
| What output form? | JSON / rendered / library entry / metadata |

---

## AFTER EACH GROUP

- Pause for user approval
- If approved: update Status to APPROVED, update Version, Last Updated
- If blocked: log in `PASS-DECISION-NOTES.md`, consult `PASS-RESTART-RULES.md`

---

## MANDATORY RULES

- Do **NOT** invent new inputs/outputs not implied by Pass 1 grounding docs
- Do **NOT** write implementation code
- Do **NOT** define internal implementation details
- Contracts must be **consistent across boundaries**: A's output type = B's input type
- Do **NOT** define error codes yet (categories only — codes are Pass 3 territory)
- Apply the output-boundary rule to every contract surface
- Apply the storage model context to every library boundary
- If a contract cannot be defined, flag it — don't guess
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../../CLAUDE.md) §7

---

## BUILD SIZE CONSTRAINT

- Contracts inform LOC estimates but don't determine them
- Flag any contract that implies a runtime file >2000 LOC — design smell

### File Size Risk Bands
- **Low:** <1500 LOC
- **Medium:** 1500–2000 LOC
- **High:** >2000 LOC

---

## CONTRACT TEST

A contract is complete when:
> "Could a developer implement this boundary without asking questions?"

If no, the contract is incomplete.

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{system}}/
├── CONTRACT.md                        ← NEW
└── {{subsystem}}/
    └── CONTRACT.md                    ← NEW

NCE-V2/specs/integrations/{{provider}}/{{service}}/
└── CONTRACT.md                        ← NEW
```

---

## END CONDITION

PASS 2 is COMPLETE only when:
- [ ] All system CONTRACT.md created and approved (27 systems)
- [ ] All subsystem CONTRACT.md created and approved
- [ ] All external integration CONTRACT.md created and approved (or N/A)
- [ ] Contracts are consistent across boundaries (output type = downstream input type)
- [ ] Binding type and output form declared on every contract surface
- [ ] Library boundaries declare D1 binding name and read/write side
- [ ] PASS-PROGRESS.md updated: Phase 15 marked **COMPLETE**

**Next:** Proceed to Phase 16 (Pass 3 — Dependencies)

---

## PASS 2 CHECKLIST

### Ownership Clarity
- [ ] Purpose clearly stated (1 short paragraph)
- [ ] "Owns" list explicit
- [ ] "Does NOT own" list exists

### Inputs & Outputs
- [ ] Inputs listed with source system + binding type
- [ ] Outputs listed with destination system + binding type
- [ ] No "magic" data appearing from nowhere
- [ ] Output form (JSON / rendered / library entry / metadata) stated

### Dependencies
- [ ] All system dependencies declared
- [ ] D1 bindings declared for library access
- [ ] Any questionable dependency challenged
- [ ] TS type/interface assumptions noted lightly

### Errors (Categories Only)
- [ ] Error types identified (not codes yet)
- [ ] Recoverable vs fatal distinguished
- [ ] Resilience involvement noted if applicable

### Tests (Intent Only)
- [ ] Happy path described
- [ ] At least one failure path identified
- [ ] At least one boundary condition noted

### Sanity Test
- [ ] Could you prevent another system from stealing this module's responsibility?

### DO NOT
- Lock schemas
- Write full logic
- Add error codes (categories only)

---

## PRE-SUBMISSION SELF-CHECK

- [ ] Pass 1 grounding consulted; no contradictions
- [ ] All contract surfaces have binding type + output form declared
- [ ] Library access contracts go through `library/Librarian`, not direct D1
- [ ] Output-boundary rule respected (no rendered web output from JSON-emitter contracts)
- [ ] No error codes defined (categories only)
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")

---

## TEMPLATES

- [CONTRACT-TEMPLATE.md](./CONTRACT-TEMPLATE.md)

> **TODO (Template enrichment):** B's CONTRACT-TEMPLATE.md doesn't have binding-type, output-form, or library-D1-binding columns. Decide enrichment vs prompt enforcement.

---

## INPUTS

From Pass 1: All SYSTEM.md, SUBSYSTEM.md, EXTERNAL-INTEGRATION.md, ADMIN.md

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Binding type as a first-class contract field — keep or simplify?
- Library D1-binding contract pattern — does it match how you want library access expressed?
- May I proceed with 16–17?
