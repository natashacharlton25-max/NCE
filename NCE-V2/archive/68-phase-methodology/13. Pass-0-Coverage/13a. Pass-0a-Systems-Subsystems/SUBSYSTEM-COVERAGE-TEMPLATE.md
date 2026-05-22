# Subsystem Coverage Review — {{Subsystem Name}}

## Parent System
{{System Name}}

## Subsystem Role Summary
(1–2 sentences.)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered artefact / library entry / metadata |
| **Output Destination** | parent / sibling / external system / Astro / external provider / human |
| **Storage Touch** | none / D1 (library) / R2 (asset) / KV (cache) / DO (state) / Queue |
| **D1 Binding (if library-touching)** | N/A / {{BINDING_NAME}} |
| **Output-Boundary Direction Correct?** | Yes / Flag — see notes |

## Overall Verdict
- Coverage: Yes / Partial / No
- Risk level: Low / Medium / High
- Est. total LOC: ~{{number}}
- Largest expected file: ~{{number}} LOC
- Review date: {{timestamp}}

## Responsibility Coverage

| Area | Coverage | Notes |
|------|----------|-------|
| | Covered / Partial / Missing | |

## Size & Sufficiency Assessment

| Signal | Assessment | Notes |
|--------|------------|-------|
| Responsibility clarity | Clear / Partial / Unclear | |
| Estimated LOC | ~{{number}} | TS multiplier 1.3–1.5× applied if porting Python-era estimate |
| Size band | Low (<1500) / Medium (1500–2000) / High (>2000) | High = design smell — log exception or split |

## Boundary Issues

- Output-boundary check: does this subsystem produce rendered web output? (should not, unless its parent is `renderers/`)
- Storage-model check: filesystem assumptions present? (flag if yes)
- Library bypass check: direct D1 access from outside `library/`? (flag if yes)
- Responsibility overlap with parent system or sibling subsystems? (list)

## LOC Estimation Method
- TypeScript multiplier: 1.3–1.5× over Python
- Runtime LOC excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments

## Verdict
CAN / CANNOT fulfil its role within size and scope constraints.

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---
