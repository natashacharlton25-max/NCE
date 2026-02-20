# PASS 0 — Integration Viability Review

Integration Name: Canva
Integration Type: External Provider Adapter
Status: Draft

---

## External Provider
- Provider: Canva
- Provider Domain: Design / Templates
- Provider Interface: API

---

## Internal Authority (CRITICAL)

This integration is **not authoritative**.

Authoritative internal system(s):
- template/
- visual/

The integration executes instructions from internal systems and reports provider-level events.

---

## Purpose

Provide a bounded adapter between internal systems and Canva for:
- Execution (create, export, sync designs)
- Configuration (API keys, workspace settings)
- Telemetry ingestion (design events, export statuses)

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

Provider concepts are treated as **operational mirrors only**.

---

## Scope Boundaries

### In Scope
- Canva API interaction
- Authentication / credentials
- Design export operations
- Template sync operations
- Webhook / event ingestion
- Normalisation of Canva responses

### Out of Scope
- Domain modelling
- Cross-system coordination
- Long-term analytics interpretation
- Policy enforcement
- User-facing logic

---

## Replaceability

This integration is:
- Provider-specific
- Swappable without changing internal domain logic

Internal systems must not depend on Canva-specific semantics.

---

## Size & Complexity Signal

- Expected size: 300–500 LOC
- Subsystems: None expected
- Complexity risk: Low

---

## Risks & Notes

- Rate limits on Canva API
- Design export format compatibility

---

## Verdict

☑ SAFE as an integration

**Status:** Approved
