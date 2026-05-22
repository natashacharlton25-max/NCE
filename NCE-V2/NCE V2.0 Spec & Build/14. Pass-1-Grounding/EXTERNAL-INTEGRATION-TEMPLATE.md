# {{Service Name}} Integration

## Provider
{{Provider Name}}

## Service
{{Service Name}} ({{API Version}})

## Purpose
(1–2 sentences. Why we integrate with this service.)

## Scope — What This Integration Handles
- …
- …

## Explicit Non-Goals
(What we will NOT use this service for, even if it supports it.)
- …
- …

## Consuming Systems
(Which of our internal systems use this integration.)

| System | How It Uses This |
|--------|------------------|
| | |

## Data Boundary

### Data We Send

| Data | Sensitivity | Description |
|------|-------------|-------------|
| | Low / Medium / High | |

### Data We Receive

| Data | Validation Required | Description |
|------|---------------------|-------------|
| | Yes / No | |

## Failure Contract
(What failures can occur and how we must handle them.)

| Failure Type | Our Response |
|--------------|--------------|
| Service unavailable | |
| Auth failure | |
| Rate limit exceeded | |
| Timeout | |
| Invalid response | |

## External Constraints

| Constraint | Value | Impact |
|------------|-------|--------|
| Rate limit | | |
| Quota | | |
| Payload size | | |

## Dependencies

### Internal Dependencies
(Our systems/subsystems this integration depends on.)

| Dependency | Reason |
|------------|--------|
| | |

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
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 14 (Pass 1)
---
