> ⚠️ This document is non-authoritative.
> It provides context, rationale, and explanation.
> Behaviour is defined only in SUBSYSTEM.md and SPEC.md.

# BrandPermissionResolver — Notes

## Conceptual Role

BrandPermissionResolver ensures that **roles define potential**, but **brands define reality**.

A user may be globally capable of an action, but the active brand ultimately determines whether that action is permitted at a given moment.

---

## Why This Exists

Without a brand-aware permission layer:

- Access rules leak across brands
- Lifecycle states become advisory instead of enforceable
- “Why was this allowed?” becomes unanswerable
- Systems begin embedding brand logic locally

This subsystem centralises brand governance for access decisions.

---

## Brand Lifecycle Examples

### Full-Control Brand
- All standard capabilities enabled
- Minimal overrides
- Intended for mature, internal brands

### Onboarding Brand
- Publishing disabled
- Destructive actions restricted
- Review-only capabilities enabled

### Partner Brand
- Capabilities filtered aggressively
- No administrative access
- Conditional permissions only

---

## Override Philosophy

Overrides should be:

- Explicit
- Auditable
- Restrictive by default

Expansive overrides are intentionally discouraged and should require explicit approval.

---

## Design Rationale

BrandPermissionResolver exists to prevent:
- Implicit permission escalation
- Brand state being “checked later”
- Content systems making access decisions

It also provides a natural place for future governance features without contaminating role or capability logic.

---

## Future Considerations (Non-Binding)

- Time-bound brand permissions
- Delegated brand administrators
- Confidence-gated capabilities
- Approval-required overrides

These are **not commitments** and must not be treated as behaviour.
