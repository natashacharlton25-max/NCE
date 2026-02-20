# PASS 0 — Integration Viability Review

Integration Name: Emaillit
Integration Type: External Provider Adapter
Status: Draft

---

## External Provider
- Provider: Emaillit
- Provider Domain: Email Delivery
- Provider Interface: API

---

## Internal Authority (CRITICAL)

This integration is **not authoritative**.

Authoritative internal system(s):
- email/

The integration executes instructions from internal systems and reports provider-level events.

---

## Purpose

Provide a bounded adapter between internal systems and Emaillit for:
- Execution (send emails, manage lists)
- Configuration (API keys, sender domains)
- Telemetry ingestion (delivery events, bounce/open tracking)

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
- Emaillit API interaction
- Authentication / credentials
- Email send operations
- Delivery status tracking
- Webhook / event ingestion
- Normalisation of Emaillit responses

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

Internal systems must not depend on Emaillit-specific semantics.

---

## Size & Complexity Signal

- Expected size: 300–450 LOC
- Subsystems: None expected
- Complexity risk: Low

---

## Risks & Notes

- Rate limits on sending
- Deliverability reputation management

---

## Verdict

☑ SAFE as an integration

**Status:** Approved
