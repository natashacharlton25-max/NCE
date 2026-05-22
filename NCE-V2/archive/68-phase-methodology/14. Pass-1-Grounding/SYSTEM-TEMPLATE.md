# {{System Name}}

## Purpose
(1–2 sentences. Why this system exists.)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered artefact / library entry / metadata / mixed |
| **Output Destination** | internal system / Astro / external provider / human / library |
| **Library Ownership** | None / {{D1_BINDING_NAME(s)}} |
| **Worker Grouping** | own Worker / `platform` Worker |
| **Storage Touch** | none / D1 (library) / R2 (asset) / KV / DO / Queue |

## Scope — What This System Owns
- …
- …

## Explicit Non-Goals
(What this system will never be responsible for. Include any output-boundary non-goals e.g. "Does NOT render web HTML — that is Astro's job.")
- …
- …

## Core Responsibilities
(High-level behaviours, not modules or code.)
- …
- …

## System Guarantees
(What other systems can rely on from this system.)
- …
- …

## System Boundaries

### Inputs
(What this system receives and from where.)

| Input | Source | Binding Type | Description |
|-------|--------|--------------|-------------|
| | | service / D1 / R2 / KV / DO / Queue | |

### Outputs
(What this system produces and for whom.)

| Output | Destination | Form (JSON / rendered / library / metadata) | Description |
|--------|-------------|----------------------------------------------|-------------|
| | | | |

### Allowed Dependencies
(Systems this system MAY depend on.)
- …

### Forbidden Dependencies
(Systems this system must NEVER depend on. Include output-boundary forbiddens — e.g. JSON-emitters MUST NOT depend on `renderers/`.)
- …

## Library Ownership (if applicable)

| Library | D1 Binding | Type (core/derived/generated/reference) | Read/Write Owner |
|---------|-----------|------------------------------------------|------------------|
| {{name}} | `{{BINDING_NAME}}` | core / derived / generated / reference | This system / Other |

## Subsystems
(List subsystems if any, or "None")

| Subsystem | Purpose |
|-----------|---------|
| | |

## Lifecycle
- **Status:** Draft / Active / Stable / Deprecated
- **Expected Evolution:** (how this system might change)

## Notes
(Clarifications, assumptions, or context.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 14 (Pass 1)
---
