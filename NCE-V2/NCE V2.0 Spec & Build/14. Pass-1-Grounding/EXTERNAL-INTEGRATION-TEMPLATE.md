# {{Service Name}} Integration

## Provider
{{Provider Name}}

## Service
{{Service Name}} ({{API Version}})

## Purpose
(1–2 sentences. Why we integrate with this service.)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Direction** | Outbound receiver / Inbound source / Downstream renderer (Astro) |
| **Output Form (if outbound)** | Rendered HTML / PDF blob / DOCX blob / JSON / Other |
| **Asset Sink (if inbound binary)** | R2 via `assets/` system |
| **Library Sink (if inbound metadata)** | D1 binding {{NAME}} via `library/` |
| **Auth Method** | API key / OAuth / Signed request |
| **Worker Secret Binding Name** | `{{SECRET_NAME}}` |

## Scope — What This Integration Handles
- …
- …

## Explicit Non-Goals
(What we will NOT use this service for, even if it supports it.)
- …
- …

## Consuming Systems
(Which of our internal systems use this integration.)

| System | How It Uses This | Direction |
|--------|------------------|-----------|
| | | Sends to / Receives from |

## Data Boundary

### Data We Send

| Data | Sensitivity | Form | Description |
|------|-------------|------|-------------|
| | Low / Medium / High | JSON / rendered HTML / binary / metadata | |

### Data We Receive

| Data | Validation Required | Lands In | Description |
|------|---------------------|----------|-------------|
| | Yes / No | `assets/` (R2) / library D1 / metadata-only | |

## Failure Contract
(What failures can occur and how we must handle them.)

| Failure Type | Our Response | resilience/ Involvement |
|--------------|--------------|--------------------------|
| Service unavailable | | Yes / No |
| Auth failure | | |
| Rate limit exceeded | | |
| Timeout (Worker 5-min CPU limit) | | |
| Invalid response | | |

## External Constraints

| Constraint | Value | Impact |
|------------|-------|--------|
| Rate limit | | |
| Quota | | |
| Payload size (Worker 128MB memory) | | |
| CPU-time per request | | |

## Dependencies

### Internal Dependencies
(Our systems/subsystems this integration depends on.)

| Dependency | Binding | Reason |
|------------|---------|--------|
| | service / D1 / R2 | |

### Other Services (Same Provider)

| Service | Reason |
|---------|--------|
| | |

## Lifecycle
- **API Version:** {{version}}
- **Deprecation Status:** Active / Deprecated / Sunset: {{date}}
- **Migration Plan:** (if deprecated)

## Notes
(Clarifications, assumptions, or context.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 14 (Pass 1)
---
