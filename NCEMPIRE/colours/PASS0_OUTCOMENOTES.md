# PASS 0 Outcome Notes — Colours

## Deferred System Requirement (IMPORTANT)

During PASS 0 review, it was identified that the Colours system
does NOT own palette generation from:

- semantic tokens
- sparse inputs (1–2 colours)
- external inspiration sources (e.g. colours.pizza)

This capability is required but represents a **distinct responsibility**
from colour validation and canonical palette management.

Decision:
- Palette generation will be handled by a **separate system**
- Colours will remain the **authoritative validator and approver**

Action required in PASS 1:
- Explicitly state this as a non-goal in Colours/SYSTEM.md
- Acknowledge and name a new system responsible for palette generation

Status: Deferred to PASS 1

