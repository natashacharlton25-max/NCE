# {{Subsystem Name}}

## Parent System
{{System Name}}

## Purpose
(1–2 sentences. Why this subsystem exists within the parent.)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered artefact / library entry / metadata |
| **Output Destination** | parent / sibling / external system / Astro / external provider / human |
| **Storage Touch** | none / D1 (library) / R2 (asset) / KV / DO / Queue |
| **D1 Binding (if library-touching)** | N/A / {{BINDING_NAME}} |
| **Library Access via Librarian?** | Yes / N/A / Direct D1 (flag if outside `library/`) |

## Scope — What This Subsystem Owns
- …
- …

## Explicit Non-Goals
(What this subsystem will never be responsible for.)
- …
- …

## Role Within Parent
(How this subsystem contributes to the parent system's purpose.)

## Responsibilities
(What this subsystem does.)
- …
- …

## Guarantees
(What the parent system and siblings can rely on.)
- …
- …

## Boundaries

### Inputs
(What this subsystem receives.)

| Input | Source | Binding Type | Description |
|-------|--------|--------------|-------------|
| | Parent / Sibling / External | service / D1 / R2 / KV / DO / Queue / in-Worker | |

### Outputs
(What this subsystem produces.)

| Output | Destination | Form (JSON / rendered / library / metadata) | Description |
|--------|-------------|----------------------------------------------|-------------|
| | Parent / Sibling / External | | |

### Allowed Dependencies

| Dependency | Type | Binding | Why Allowed |
|------------|------|---------|-------------|
| | Sibling / Parent / External | service / D1 / etc. | |

### Forbidden Dependencies
(What this subsystem must never depend on.)
- …

## Lifecycle
- **Status:** Draft / Active / Stable / Deprecated
- **Expected Evolution:** (how this subsystem might change)

## Notes
(Clarifications, assumptions, or context.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 14 (Pass 1)
---
