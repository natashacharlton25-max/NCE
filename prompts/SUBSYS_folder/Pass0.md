You are acting as a senior systems architect performing a PASS 0 SYSTEM REVIEW.

This is an ASSESSMENT-ONLY task.
You are NOT designing, refactoring, or proposing solutions.

Target system: access

You are given:
- The system’s intended role/purpose
- The current list of subsystems/modules (if available)

Your task:
Evaluate whether the existing subsystems are sufficient for the system to fulfil its role,
AND estimate whether the system can be built while respecting maintainability constraints.

---

MANDATORY RULES:

- Do NOT invent new subsystems
- Do NOT propose fixes or designs
- Do NOT rename modules
- Do NOT write specs or schemas
- If information is missing, say so explicitly
- Treat file size as a DESIGN SIGNAL, not an optimisation problem

---

BUILD SIZE CONSTRAINT:

- No single file should exceed ~1500 lines of code
- If a responsibility implies a file >1500 LOC, flag it as a risk
- Estimate LOC conservatively (order-of-magnitude is sufficient)

---

OUTPUT FORMAT (STRICT):

## System Role Summary
- Restate what this system is responsible for (1–2 sentences)

## Coverage Assessment

### Covered Responsibilities
- …

### Partially Covered Responsibilities
- …

### Uncovered Responsibilities
- …

## Subsystem Sufficiency Review

For each subsystem:

- <Subsystem Name>
  - Sufficiency: Sufficient / Overloaded / Underdefined
  - Reasoning: (1–2 sentences)
  - Estimated LOC: ~<number>
  - File Size Risk: Low / Medium / High

File Size Risk Rules:
- Low: <1000 LOC
- Medium: 1000–1500 LOC
- High: >1500 LOC (design smell)

## Missing Capability Areas
(List conceptual capability areas with no clear owner.
Do NOT propose subsystems.)

- Capability:
  - Impact: Low / Medium / High
  - Estimated LOC: ~<number>

## Boundary & Classification Issues
(List any responsibility that appears to leak across systems or be misclassified.)

## Overall Build Size Estimate
- Estimated total system LOC: ~<number>
- Largest expected file: ~<number> LOC
- Overall size risk: Low / Medium / High

## Risk Assessment
- Risk level: Low / Medium / High
- Reasoning:

## Verdict
- This system CAN / CANNOT reasonably fulfil its role
  with the current subsystem set and size constraints.

If anything is unclear, state it explicitly.
Do NOT suggest how to fix it.
