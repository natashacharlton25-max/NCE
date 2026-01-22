# Access System

## Purpose

The Access system provides a **deterministic, auditable, and brand-aware authority for deciding which actions are permitted across the platform**.

It centralises permission logic so that no other system is required to infer, embed, or duplicate access decisions.

---

## Scope (What This System Owns)

The Access system owns:

- Role definitions and role hierarchies
- Capability definitions (what actions exist)
- Resolution of effective permissions
- Brand-scoped permission overrides
- Precedence and conflict resolution rules
- Explainable allow / deny / conditional decisions

The Access system is the **single source of truth** for action-level permissioning.

---

## Explicit Non-Goals

The Access system does **not**:

- Authenticate users or manage identity verification
- Define brand identity, values, or lifecycle
- Implement business or content logic
- Orchestrate workflows or state transitions
- Render UI or present permissions visually
- Make autonomous approval decisions

It answers **“is this action allowed?”**, not **“how does this work?”**.

---

## Core Responsibilities

- Define roles and their inherited capabilities
- Define the universe of valid capabilities
- Apply brand-specific permission constraints
- Resolve effective permissions deterministically
- Enforce restrictive precedence on conflicts
- Produce human-readable explanations for decisions
- Fail closed on ambiguity and escalate when required

---

## System Guarantees

Other systems may rely on the Access system to guarantee that:

- Access decisions are deterministic and repeatable
- Brand boundaries are strictly enforced
- Permission escalation is explicit and auditable
- Conflicting rules resolve predictably (restrictive wins)
- No silent permission inference occurs elsewhere

---

## System Boundaries

### Allowed Dependencies

The Access system MAY depend on:

- `brand/` — for brand state and classification
- `state/` — for lifecycle or contextual signals
- `audit/` — to record access decisions
- `resilience/` — to escalate ambiguous or invalid states

### Forbidden Dependencies

The Access system MUST NOT depend on:

- Content systems
- Channel systems (website, email, social)
- Rendering or document systems
- Workflow orchestration
- UI layers
- AI generation systems

---

## Internal Composition

The Access system is composed of the following subsystems:

- **RoleManager** — defines user roles and role inheritance
- **CapabilityManager** — defines and enforces action-level capabilities
- **BrandPermissionResolver** — applies brand-specific permission overrides

Each subsystem owns a single, bounded responsibility and must not overlap with others.

---

## Failure & Escalation Behaviour

- Unknown role → deny
- Unknown capability → deny
- Missing brand context → deny
- Conflicting rules → restrictive rule wins
- Ambiguous state → escalate via `resilience/`

The Access system **fails closed by default**.

---

## Lifecycle / Maturity

- Status: Draft
- Expected evolution:
  - Delegated brand administrators
  - Time-bound permissions
  - Confidence- or approval-gated capabilities

Core responsibility boundaries are expected to remain stable.

---

## Notes

The Access system is intentionally conservative.

Any attempt to bypass or duplicate access logic in other systems is considered an architectural violation.
