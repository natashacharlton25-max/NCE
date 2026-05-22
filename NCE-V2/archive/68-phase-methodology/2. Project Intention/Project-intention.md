# Project Intention

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Human
---

> **Note for NCE-V2:** This template is used by the Phase 2 CHECK artefact to validate that `NCE-V2/Project-Intent.md` contains all required sections. The canonical intent lives at `NCE-V2/Project-Intent.md`.

## Purpose
Why this project exists at a fundamental level.

## Intended Outcome
What must be true if this project succeeds?

## Success Criteria
Clear, observable indicators that the project has achieved its purpose.

## Non-Goals
Explicitly state what this project is NOT trying to do. Include any output-boundary non-goals (e.g. "Does NOT render web HTML — that is Astro's job").

## Guiding Principles
Rules or values that should guide decisions when trade-offs arise.

## Trade-Offs Accepted
What this project is intentionally willing to sacrifice.

| We accept... | In order to... |
|--------------|----------------|
| | |

## Runtime & Stack Commitment (NCE-V2-specific)
- TypeScript on Cloudflare Workers
- D1 + R2 + KV + DO + Vectorize as storage primitives
- 2000 LOC band per runtime file
- Output-boundary rule: NCE-V2 owns authoring + final rendered artefacts; web rendering is Astro's

## Assumptions
- …

## Notes
(Clarifications or philosophical constraints.)

---

## Dependency
Requires approved: **Project Overview**

## Next Step
When APPROVED, proceed to: **Phase 3 — System Identification**
