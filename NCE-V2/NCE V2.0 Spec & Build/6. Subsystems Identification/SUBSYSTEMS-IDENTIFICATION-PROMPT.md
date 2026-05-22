# SUBSYSTEM IDENTIFICATION — PROMPT

## ROLE
You decompose a system into internal responsibility boundaries.
Your job is to propose subsystems that divide the system's work into manageable, independent pieces.

You are NOT designing implementation, choosing technologies, or expanding system scope.

---

## TASK

For the given system (from its approved SYSTEM-NOTES.md), propose a minimal set of subsystems using the **Subsystems Clarification** template.

**Process one system at a time.** Complete subsystem identification for System A before moving to System B.

---

## WHAT IS A "SUBSYSTEM"?

A subsystem is an **internal responsibility boundary** within a system. It:
- Owns a specific part of the system's overall responsibility
- Could be developed and tested somewhat independently
- Has clear inputs and outputs relative to sibling subsystems
- Is expected to stay under ~1,500 LOC (just like systems)

**Subsystems are NOT:**
- Classes or modules (too granular)
- Features (features cut across subsystems)
- Services or APIs (implementation details)
- Duplicates of the parent system's responsibility

**Subsystems ARE:**
- Internal divisions of labour
- Cohesive chunks of related logic
- Separately testable units

---

## WHEN TO CREATE SUBSYSTEMS

**Create subsystems when:**
- The system's scope clearly exceeds ~1,500 LOC
- There are distinct internal responsibilities that change independently
- Different parts of the system have different dependencies
- Testing would be simpler with clear internal boundaries

**Do NOT create subsystems when:**
- The system is already small enough (~1,500 LOC or less)
- The proposed split would create artificial boundaries
- Subsystems would be too tightly coupled to be meaningful
- You're just creating subsystems because "every system needs them"

**It's OK for a system to have zero subsystems.** Small, focused systems don't need internal decomposition.

---

## NAMING RULES

| Good Names | Bad Names | Why Bad |
|------------|-----------|---------|
| Validation | ValidatorService | Sounds like a class |
| Storage | DatabaseHandler | Technology-specific |
| Parsing | JSONParser | Implementation detail |
| Routing | Router | Too generic |
| Rate Control | RateLimiter | Sounds like a class |

**Pattern:** Use 1-2 word noun phrases that describe **what responsibility is owned**, not how it works.

**Relationship to parent:** Subsystem names should make sense in the context of the parent system. "Validation" inside "User Identity" means user validation. No need to prefix with parent name.

---

## HOW TO IDENTIFY SUBSYSTEMS

### Step 1: Review the system's In Scope responsibilities
Look at the SYSTEM-NOTES.md:
- What distinct activities does this system own?
- Could any be developed independently?

### Step 2: Look for natural seams
- Different data being handled → possible split
- Different lifecycles (setup vs runtime) → possible split
- Different external touchpoints → possible split

### Step 3: Apply the ~1,500 LOC test
- If the system is ~1,500 LOC or less, you probably don't need subsystems
- If a proposed subsystem is only ~200 LOC, it might not deserve to be separate

### Step 4: Minimize
- Prefer fewer subsystems over many
- Every subsystem adds coordination overhead
- Aim for 2-4 subsystems per system, or zero if not needed

---

## RULES

1. **Subsystems represent internal responsibilities, not technologies**
2. **Do NOT invent new system responsibilities** — only divide existing scope
3. **Do NOT expand the parent system's scope** — subsystems must fit within In Scope
4. **Each subsystem must justify why it is separate** — "it's cleaner" is not enough
5. **Splits must be motivated by maintainability and size**
6. **Prefer conservative, stable boundaries** — when in doubt, don't split
7. **No technologies, frameworks, or implementation details**

---

## COMMON MISTAKES

| Mistake | Example | Fix |
|---------|---------|-----|
| Splitting too small | 5 subsystems for a simple system | Merge or don't split at all |
| Technology-based splits | "Database Subsystem" | Name by responsibility: "Persistence" |
| Expanding scope | Adding new responsibilities as subsystems | Stick to parent's In Scope |
| Arbitrary splits | Splitting just to have subsystems | Only split if justified |
| Duplicating parent | Subsystem purpose = system purpose | Be more specific |

---

## PROCESS

1. Read the approved SYSTEM-NOTES.md for the target system
2. Determine if subsystems are needed (system > ~1,500 LOC expected?)
3. If yes, identify natural seams in the system's responsibilities
4. Propose minimal subsystem set
5. Write up using the template
6. Present and ask for approval
7. Repeat for next system

---

## OUTPUT FORMAT

Return a completed Subsystems Clarification document using the template.

**If subsystems are needed:**
Present the document and ask:
```
Please review the proposed subsystems for {{System Name}}.
- Reply APPROVE to accept and proceed to Subsystem Pre-build Check
- Reply with specific changes if revisions are needed
```

**If NO subsystems are needed:**
```
## Subsystem Assessment: {{System Name}}

After reviewing the SYSTEM-NOTES.md, this system does not require subsystem decomposition.

**Reasoning:**
- Expected LOC: ~{{estimate}}
- Responsibilities are cohesive and do not have natural seams
- Splitting would create artificial boundaries

**Recommendation:** Proceed without subsystems for this system.

Reply APPROVE to accept this assessment, or explain why subsystems are needed.
```

---

## ON APPROVAL

When approved:
- Update Status to: **APPROVED**
- Update Last Updated to: **{{current_timestamp}}**
- If subsystems exist → proceed to: **Subsystem Pre-build Check**
- If no subsystems → proceed to next system, or to **Pass 0** if all systems done

---

## TEMPLATE

```markdown
# Subsystems Clarification

---
Status: DRAFT | APPROVED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Parent System
{{System Name}}

## System Intent Reminder
(One short paragraph restating the system's responsibility.)

---

## Proposed Subsystems

### {{Subsystem Name}}

**Purpose:** (One sentence describing the internal responsibility this subsystem owns.)

**Justification:**
- Why this responsibility must be separated from other subsystems
- How this separation helps keep each unit under ~1,500 LOC

---

(repeat for each subsystem)

---

## Subsystem Count Summary

| Parent System | Subsystem Count | Expected LOC per Subsystem |
|---------------|-----------------|---------------------------|
| {{System Name}} | {{n}} | ~{{estimate}} |

---

## Dependency
This document requires approved: **SYSTEM-NOTES.md** for {{System Name}}

## Next Step
When this document is APPROVED, proceed to: **Subsystem Pre-build Check**
```

---

## INPUTS

Parent System SYSTEM-NOTES.md:
{{system_notes}}

Project Intention (for reference):
{{project_intention}}

Timestamp:
{{current_timestamp}}