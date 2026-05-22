# Implementation Plan

## Project
NCE-V2 (TypeScript on Cloudflare Workers)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **LOC Target** | ~2000 per runtime file (per Project-Intent.md, 2026-05-18) |
| **LOC Exclusions** | `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments |
| **TS Verbosity Multiplier (vs Python)** | 1.3–1.5× when porting Python-era estimates |
| **Worker Topology** | `platform` Worker (services/system/state/library) + 23 per-system Workers |
| **Foundation Milestone** | `platform` Worker (always built first) |

---

## Summary

| Metric | Value |
|--------|-------|
| Total Components | {{n}} (27 systems + lib/ + migrations/) |
| Total Files | {{n}} |
| Total Estimated LOC | ~{{n}} (runtime only, exclusions applied) |
| Total Tasks | {{n}} |
| Estimated Duration | {{time}} |
| Files exceeding 2000 LOC | {{n}} (logged exceptions in PASS-DECISION-NOTES.md) |

---

## File Structure

### {{System Name}} (Worker: {{worker-name}})

| File | Purpose | Output Form | Est. LOC (runtime) | Size Band |
|------|---------|-------------|---------------------|-----------|
| `{{filename}}.ts` | {{purpose}} | JSON / rendered / library / metadata | ~{{n}} | Low (<1500) / Medium (1500–2000) / High (>2000) |

**System Total Runtime LOC:** ~{{n}}

#### {{Subsystem Name}}

| File | Purpose | Output Form | Est. LOC | Size Band |
|------|---------|-------------|----------|-----------|
| `{{filename}}.ts` | {{purpose}} | | ~{{n}} | |

**Subsystem Total Runtime LOC:** ~{{n}}

---

### integrations/{{Provider}} (Worker: integrations OR per-provider)

#### {{Service Name}}

| File | Purpose | Direction | Est. LOC | Size Band |
|------|---------|-----------|----------|-----------|
| `{{filename}}.ts` | {{purpose}} | Inbound / Outbound / Renderer | ~{{n}} | |

**Service Total Runtime LOC:** ~{{n}}

---

## Size Verification

| Component | Est. LOC | Size Band | Status | Action |
|-----------|----------|-----------|--------|--------|
| {{component}} | ~{{n}} | Low / Medium / High | OK / Monitor / Over | {{action}} |

### Components Requiring Attention (>2000 LOC)

| Component | Est. LOC | Issue | Resolution | Logged in DECISION-NOTES? |
|-----------|----------|-------|------------|----------------------------|
| {{or None}} | | | Split / Accept exception | Yes / N/A |

---

## Output-Boundary Verification

| System | Output Form (per plan) | Boundary Respected? | Issues |
|--------|-------------------------|---------------------|--------|
| website/ | JSON only | Yes / Flag | |
| renderers/ | PDF/DOCX/HTML/Markdown final | Yes / Flag | |
| email/ | Rendered HTML for Emailit | Yes / Flag | |
| All library-touching systems | Access via `library/Librarian` | Yes / Direct D1 found | |

---

## Task Breakdown

### Foundation Milestone (platform Worker)
(Tasks that must complete before any other Worker can build)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| 1 | {{task}} | services/ | {{n}} | None |
| 2 | {{task}} | system/ | {{n}} | None |
| 3 | {{task}} | state/ | {{n}} | None |
| 4 | {{task}} | library/ | {{n}} | Task 1, 2, 3 |

### Phase 2: Core Systems
(Tasks depending on platform Worker)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| 5 | {{task}} | {{component}} | {{n}} | Foundation complete |

### Phase 3: Integration
(Tasks depending on Phase 2)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| | | | | |

### Phase 4: Polish
(Final tasks)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| | | | | |

---

## Task Sequence

```
(Visual task sequence)
```

---

## Parallel Streams

| Stream | Tasks | Owner |
|--------|-------|-------|
| Stream A | | |
| Stream B | | |

---

## Milestones

| Milestone | Tasks Complete | Deliverable | Target |
|-----------|----------------|-------------|--------|
| M0: Foundation (platform Worker) | 1–4 | platform Worker deployable | |
| M1: Core systems | | Brand, Content, AI, etc. deployable | |
| M2: Integrations | | External providers wired | |
| M3: Renderers + Email + Social | | All output paths working | |
| M4: Ready for testing | | All 27 systems deployed | |

---

## Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| {{risk}} | Low / Medium / High | Low / Medium / High | |

---

## Assumptions

- TS verbosity multiplier 1.3–1.5× over Python is accurate for our domain
- 2000 LOC per file ceiling holds without major exception waves
- Worker 5-min CPU / 128 MB memory sufficient for all runtime tasks

---

## Open Items

| Item | Logged in DECISION-NOTES? | Owner | Due |
|------|----------------------------|-------|-----|
| | Yes / No | | |

---

## Notes
(Clarifications, context, or considerations.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 17 (Pass 4)
---
