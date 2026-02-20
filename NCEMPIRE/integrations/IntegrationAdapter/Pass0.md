# PASS 0 — Integration Viability Review

Integration Name: IntegrationAdapter
Integration Type: Base Adapter Pattern
Status: Draft

---

## External Provider
- Provider: (Generic — base pattern for all integrations)
- Provider Domain: N/A
- Provider Interface: Abstract

---

## Internal Authority (CRITICAL)

This integration is **not authoritative**.

Authoritative internal system(s):
- (Varies by concrete implementation)

The adapter provides shared patterns for all integrations to follow.

---

## Purpose

Provide a base adapter pattern for:
- Common authentication patterns
- Standard error handling
- Response normalisation
- Rate limiting patterns
- Retry logic

No domain meaning or decision logic is defined here.

---

## Explicit Non-Goals (MANDATORY)

This integration does **NOT**:
- Define business intent
- Own consent, permissions, or policy
- Define domain entities as sources of truth
- Make allow/deny decisions
- Enforce brand, legal, or ethical rules
- Replace internal systems of record

---

## Scope Boundaries

### In Scope
- Base adapter interface/abstract class
- Common HTTP client patterns
- Authentication helpers
- Error normalisation utilities
- Retry/backoff patterns

### Out of Scope
- Provider-specific logic
- Domain modelling
- Cross-system coordination
- Policy enforcement

---

## Replaceability

This adapter is:
- Internal infrastructure
- Provides patterns, not provider-specific code

Concrete integrations extend or follow this pattern.

---

## Size & Complexity Signal

- Expected size: 200–350 LOC
- Subsystems: None expected
- Complexity risk: Low

---

## Risks & Notes

- Must remain generic and not leak provider-specific concerns
- Changes here affect all integrations

---

## Verdict

☑ SAFE as an integration

**Status:** Approved
