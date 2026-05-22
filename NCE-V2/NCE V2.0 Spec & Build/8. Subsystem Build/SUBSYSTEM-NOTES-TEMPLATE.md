# {{Subsystem Name}}

---
Status: DRAFT | APPROVED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD
Parent System: {{System Name}}
---

## Purpose
(Why this subsystem exists inside the parent system. What responsibility it owns that should not live elsewhere.)

## Intent
(What problem this subsystem solves within the system. Focus on outcomes, not implementation.)

## Justification
- Why this responsibility must be separated from other subsystems
- How this separation helps maintain clarity and size limits

---

## In Scope
(Responsibilities this subsystem explicitly owns. Be specific.)

- …
- …

## Out of Scope
(Responsibilities this subsystem must NOT take. Include sibling subsystems and parent system references.)

- …
- …

## Non-Goals
(Things this subsystem will never do, even if they seem related.)

- …
- …

---

## Inputs
(What this subsystem receives. Name sources conceptually — sibling subsystems, parent system, or external systems.)

| Input | Source | Description |
|-------|--------|-------------|
| … | … | … |

## Outputs
(What this subsystem provides. Name consumers.)

| Output | Consumer | Description |
|--------|----------|-------------|
| … | … | … |

## Dependencies
(Other subsystems or systems this subsystem relies on.)

| Dependency | Type | Reason |
|------------|------|--------|
| … | Sibling / Parent / External | … |

---

## Size Constraint
This subsystem is intended to remain under **~1,500 lines** of implementation code.
If this limit is at risk, the subsystem must be further decomposed.

---

## Notes
(Freeform clarifications, assumptions, or future considerations. No implementation details permitted.)

---

## Pass Tracking

| Pass | Status | Owner | Date |
|------|--------|-------|------|
| Build | COMPLETE | Claude | {{timestamp}} |
| Pass 0 | PENDING | — | — |
| Pass 1 | PENDING | — | — |
| Pass 2 | PENDING | — | — |
| Pass 3 | PENDING | — | — |
| Pass 4 | PENDING | — | — |

## Pass Notes
(Record decisions, changes, or issues discovered during each pass.)