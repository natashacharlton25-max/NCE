# Project Intention

**Status:** Approved
**Project name:** NCE-V2
**Last reviewed:** 2026-05-23
**Owner:** Human (Natasha)

---

## Core Intention

This project exists to provide a **system-of-systems for controlled, explainable planning, generation, governance, and publishing of brand-aligned content and assets**.

It is designed as a long-lived architectural foundation, prioritising clarity of responsibility, traceable decision-making, and structural resilience over speed, convenience, or surface-level simplicity.

---

## Fundamental Nature of the Project

This is **not a single product or workflow**, but a **composable system-of-systems**.

Each system:
- owns a clearly bounded domain
- exposes explicit contracts
- can evolve independently without destabilising others

The project is intentionally designed to remain understandable and governable **years after its initial construction**.

---

## Problem Space (In Scope)

This project is explicitly designed to support:

- Structured planning and specification as first-class artefacts
- Brand-governed decision making across all outputs
- Multi-modal content and asset generation (text, documents, visuals, marks)
- Explicit access, permission, and capability control
- Failure, recovery, and escalation as governed domains
- Human intervention as an intentional, auditable mechanism
- Incremental system evolution without architectural rewrites

The system assumes **an expert operator**, not a casual user, and optimises for correctness and legibility over ease of use.

---

## Out of Scope (Explicit Non-Intent)

This project is NOT intended to:

- Be a rapid prototyping or experimentation playground
- Optimise for shortest time-to-feature
- Hide complexity behind implicit behaviour
- Operate as a generic CMS or low-governance content tool
- Allow systems to couple implicitly or share unclear ownership
- Treat AI decisions as opaque or unquestionable

If a feature primarily serves speed, abstraction, or convenience at the cost of structural clarity, it does not belong here.

---

## Design Principles (Non-Negotiable)

- Architecture before implementation
- Stage-based development (5 stages: Project Frame → Component Specs → Project-Wide Spec → Implementation → Release; see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
- Each module belongs to one authoritative system only
- Systems communicate via explicit, documented contracts
- Brand, access, and resilience are global constraints
- Failure states are explainable, recoverable, and auditable
- Human intervention is explicit and reviewable
- Documentation must remain truthful over time
- All library data access goes through `library/Librarian` (mediated; no direct database access from outside the library system)
- Output-boundary rule: NCE-V2 emits JSON for web (rendered by Astro consumer); NCE-V2 emits final rendered artefacts for PDF/DOCX/email/embedded marks
- No single build file should exceed ~2000 lines of runtime code  
  (file size is treated as a design signal, not an optimisation target; excludes test, debug, and type-only files; applies to TypeScript with a 1.3–1.5× verbosity multiplier when porting Python-era estimates)

---

## Intended Trade-offs

This project explicitly accepts:

- Slower early progress in exchange for long-term stability
- More files and documentation in exchange for bounded complexity
- Conservative architectural decisions in exchange for future freedom
- Explicit planning overhead in exchange for fewer systemic rewrites

---

## Success Criteria (Structural)

This project is successful when:

- New systems can be added without refactoring existing ones
- Subsystems remain bounded and understandable
- No subsystem becomes a de facto “god object”
- Failures are diagnosable and narratable
- Architectural intent remains legible over time
- Documentation continues to match real system behaviour

---

## Global Non-Goals

Regardless of system or phase:

- No hidden decision logic
- No silent AI behaviour
- No unbounded modules
- No undocumented shortcuts
- No implicit cross-system coupling

---

## Technical Substrate (locked)

The architectural intent above is implemented on this concrete substrate. These are not negotiable without revisiting this document.

- **Runtime:** TypeScript on Cloudflare Workers (V8 isolates; paid plan — 5-minute CPU limit per invocation, 128 MB memory)
- **Storage:**
  - **D1** for content libraries (SQLite at edge; text and JSON only; accessed by Worker binding, not file path)
  - **R2** for binary assets (handled by the `assets/` system; never stored in libraries directly)
  - **KV** for cache and feature flags
  - **Durable Objects** for stateful coordination
  - **Vectorize** for embeddings
- **System count:** 27 top-level systems plus `lib/` shared utilities (canonical list in `NCE-V2/FileTree-v2.md`)
- **Worker organisation:** one Worker per system, except `services`/`system`/`state`/`library` grouped together into a `platform` Worker (foundation tier)
- **Downstream:** Astro website platform consumes NCE-V2's JSON output for web rendering; NCE-V2 itself does not render web HTML

---

## Review & Evolution

This document is intended to be stable and slow-moving.

Changes require:
- Explicit justification
- Review against existing system contracts
- Confirmation that the core intention is still being served
