# BrandPermissionResolver Module

> **Purpose:** Resolve brand-specific permission overrides
> **Type:** Access Module
> **Status:** Active
> **Version:** 1.0.0

---

## Overview

**BrandPermissionResolver** is responsible for applying **brand-level permission overrides** on top of base role capabilities.

It enables **per-brand isolation**, **graduated access**, and **governed progression** (e.g. draft-only → publish-ready), without contaminating global role definitions.

This module ensures that **roles remain generic**, while **brands retain sovereignty** over how those roles are allowed to operate within their own context.

---

## Core Responsibilities

### 1. Brand-Specific Permission Resolution

* Accept a **brand context** and **user role**
* Load brand-level permission rules
* Apply overrides to base role capabilities
* Produce a **brand-scoped capability set**

### 2. Permission Override Logic

* Allow **restrictive overrides** (deny capabilities)
* Allow **expansive overrides** (grant additional capabilities)
* Support **conditional overrides** (state, lifecycle, approval)

### 3. Multi-Brand Isolation

* Ensure permissions are **never leaked across brands**
* Guarantee that brand A’s overrides cannot affect brand B
* Enforce strict brand boundary checks

---

## What This Module Owns

* Brand-level permission configuration schemas
* Override precedence rules
* Brand isolation guarantees
* Capability resolution decisions **at brand scope**

It does **not** own:

* Role definitions
* Global access policy
* Capability semantics

---

## Inputs

| Input                  | Description                            |
| ---------------------- | -------------------------------------- |
| `brand_id`             | Target brand context                   |
| `role_id`              | User’s base role                       |
| `user_id`              | (Optional) for audit & exception logic |
| `requested_capability` | Capability being checked               |

---

## Outputs

| Output                  | Description                     |
| ----------------------- | ------------------------------- |
| `ResolvedCapabilitySet` | Final, brand-scoped permissions |
| `PermissionDecision`    | allow / deny / conditional      |
| `DecisionRationale`     | Human-readable explanation      |

---

## Resolution Order (Non-Negotiable)

1. **Base role capabilities** (from RoleManager)
2. **Brand-level overrides** (this module)
3. **Temporary exceptions** (if present)
4. **Final capability decision**

No other system may alter this order.

---

## Supported Override Types

### Restrictive

```txt
Role allows → Brand denies
```

Example:

* Role: Editor
* Capability: Publish
* Brand override: ❌ deny
  → Final result: **Denied**

---

### Expansive

```txt
Role denies → Brand allows
```

Example:

* Role: Contributor
* Capability: CreateDraft
* Brand override: ✅ allow
  → Final result: **Allowed**

---

### Conditional

```txt
Allowed only if condition is met
```

Conditions may include:

* Brand lifecycle state (draft / active / archived)
* Approval flags
* Brand confidence thresholds
* Partner / internal brand classification

---

## Use Cases (Expanded)

### 1. Full-Control Internal Brand

* All publishing capabilities enabled
* Access to all document templates
* No approval gates

### 2. New / Onboarding Brand

* Draft-only permissions
* Publishing disabled
* Limited template access
* Approval required for escalation

### 3. Partner / Client Brand

* Restricted template set
* No brand rule editing
* Publishing limited to approved channels

---

## Dependency Contracts

### Depends On

* **BrandManager**
  Provides brand identity, lifecycle state, and classification.
* **RoleManager**
  Provides base role → capability mappings.

### Feeds Into

* **CapabilityManager**
  Receives final, resolved capability sets for enforcement.

### Must NOT Depend On

* Content systems
* Channel systems (website, email, social)
* Rendering systems
* UI or workflow orchestration

This module is **pure access logic**.

---

## Failure & Escalation Behaviour

* Invalid brand → **hard deny**
* Missing override config → **fallback to role**
* Conflicting rules → **restrictive wins**
* Ambiguous state → escalate to **resilience/** (never guess)

---

## Audit & Explainability

Every resolution must emit:

* brand id
* role id
* applied overrides
* final decision
* rationale string

This enables:

* audit trails
* human review
* explainable access decisions

---

## Example (Conceptual)

```txt
User Role: Editor
Brand: NewBrand (state: draft)

Base role allows: Publish
Brand override: deny Publish until approved

→ Final decision: DENY
→ Rationale: "Publishing disabled for brands in draft state"
```

---

## Versioning Notes

* **v1.0.0** — Initial formalisation
* Future versions may add:

  * time-bound overrides
  * confidence-scored permissions
  * delegated brand admins

---

## Architectural Rule (Pin This)

> **Roles define potential.
> Brands define reality.**

BrandPermissionResolver is where that rule is enforced.

---


*Spec version: 0.0.0*
*Created: 2026-01-21*
*Status: Placeholder*
