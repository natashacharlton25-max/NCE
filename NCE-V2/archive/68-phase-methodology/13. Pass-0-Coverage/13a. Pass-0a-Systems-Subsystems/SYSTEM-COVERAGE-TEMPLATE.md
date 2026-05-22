# System Coverage Review — {{System Name}}

## System Role Summary
(1–2 sentences.)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered artefact / library entry / metadata / mixed |
| **Output Destination** | internal system / Astro / external provider / human / library |
| **Library Ownership** | None / {{D1_BINDING_NAME(s)}} |
| **Worker Grouping** | own Worker / `platform` Worker / `lib/` (no Pass 0) |
| **Output-Boundary Direction Correct?** | Yes / Flag — see notes |

## Overall Verdict
- Coverage: Yes / Partial / No
- Risk level: Low / Medium / High
- Est. total LOC: ~{{number}}
- Largest expected file: ~{{number}} LOC
- Review date: {{timestamp}}

## Responsibility Coverage

| Area | Coverage | Owner Subsystem | Notes |
|------|----------|------------------|-------|
| | Covered / Partial / Missing | | |

## Subsystem Sufficiency & Size

| Subsystem | Sufficiency | Output Form | Est. LOC | Size Band | Notes |
|-----------|-------------|-------------|----------|-----------|-------|
| | Sufficient / Partial / Insufficient | JSON / rendered / library entry / metadata | ~{{number}} | Low (<1500) / Medium (1500–2000) / High (>2000) | |

## Missing Capability Areas

| Capability | Impact | Est. LOC | Notes |
|------------|--------|----------|-------|
| | Low / Medium / High | ~{{number}} | |

## Boundary & Classification Issues

- Output-boundary check: any subsystem operating on rendered web output? (should be flagged — belongs to Astro)
- Storage-model check: any subsystem assuming filesystem storage instead of D1+R2?
- Library access check: any subsystem accessing D1 directly instead of via `library/Librarian`?
- …

## LOC Estimation Method
- TypeScript multiplier applied to Python-era estimates: 1.3–1.5×
- Runtime LOC excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments

## Verdict
CAN / CANNOT fulfil role as currently structured.

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---
