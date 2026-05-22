# Section 0f: Complete Document Reference

---
**Section:** 0f Implementation
**Phases:** 43-53
**Location:** `0. Admin/0f. Implementation/` + Phase folders
**Audit:** ⭐ MANDATORY after Phase 53
---

## Admin Folder Documents

Location: `0. Admin/0f. Implementation/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-implementation.md** | Instructions for Claude. Defines rules for code writing, deviation handling. | Claude reads this |
| **Master-Implementation-Guide 43to53.md** | Detailed guide for implementation phases. | Reference for both |
| **IMPLEMENTATION-PROGRESS-TEMPLATE.md** | Template to track which components are built. | You fill this in |
| **IMPLEMENTATION-DECISION-NOTES-TEMPLATE.md** | Template to log decisions and deviations. | You fill this in |

---

## Phase-by-Phase Documents

### Phase 43: Implementation Planning

**Folder:** `43. Implementation-Planning/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-43-PROMPT.md** | Instructions for Claude. Tells Claude to plan the build order based on dependencies. | Give to Claude |
| **IMPLEMENTATION-ORDER-TEMPLATE.md** | Template for build sequence - what to build first, second, etc. | Claude creates IMPLEMENTATION-ORDER.md |

**Output:** `IMPLEMENTATION-ORDER.md` — Sequence for building components

---

### Phases 44-47: Backend Implementation

**Folders:** `44. Backend-Core/`, `45. Backend-Services/`, `46. Backend-Integrations/`, `47. Backend-Review/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-44-PROMPT.md** | Instructions to build core backend systems (database, auth). | Give to Claude |
| **PHASE-45-PROMPT.md** | Instructions to build backend services (business logic). | After Phase 44 |
| **PHASE-46-PROMPT.md** | Instructions to build external integrations. | After Phase 45 |
| **PHASE-47-PROMPT.md** | Instructions to review all backend code. | After Phase 46 |

**Outputs:**
- Working backend code
- `BACKEND-REVIEW.md` — Code review findings

---

### Phases 48-50: Frontend Implementation

**Folders:** `48. Frontend-Core/`, `49. Frontend-Components/`, `50. Frontend-Integration/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-48-PROMPT.md** | Instructions to build core frontend (routing, layout, state). | Give to Claude |
| **PHASE-49-PROMPT.md** | Instructions to build UI components. | After Phase 48 |
| **PHASE-50-PROMPT.md** | Instructions to integrate frontend with backend APIs. | After Phase 49 |

**Outputs:**
- Working frontend code
- Components connected to APIs

---

### Phase 51: Integration

**Folder:** `51. Integration/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-51-PROMPT.md** | Instructions to verify frontend and backend work together. | Give to Claude |
| **INTEGRATION-VERIFICATION-TEMPLATE.md** | Template for verifying all integrations work. | Claude creates INTEGRATION-VERIFICATION.md |

**Output:** `INTEGRATION-VERIFICATION.md` — All connections verified

---

### Phase 52: Implementation Review

**Folder:** `52. Implementation-Review/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-52-PROMPT.md** | Instructions to review all implementation against specs. | Give to Claude |
| **DEVIATION-REPORT-TEMPLATE.md** | Template documenting any differences between code and specs. | Claude creates DEVIATION-REPORT.md |

**Output:** `DEVIATION-REPORT.md` — All deviations documented

---

### Phase 53: Implementation Complete

**Folder:** `53. Implementation-Complete/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-53-PROMPT.md** | Instructions to verify implementation is complete. | Give to Claude |
| **IMPLEMENTATION-CHECKLIST-TEMPLATE.md** | Final checklist - all specs implemented? All deviations documented? | Claude creates IMPLEMENTATION-CHECKLIST.md |

**Output:** `IMPLEMENTATION-CHECKLIST.md` — Final verification

---

## Order to Run Section 0f

```
Step 1:  Read Claude-implementation.md (understand section)
Step 2:  Phase 43 - Create IMPLEMENTATION-ORDER.md
Step 3:  Phases 44-47 - Build and review backend
Step 4:  Phases 48-50 - Build frontend and integrate
Step 5:  Phase 51 - Verify integration
Step 6:  Phase 52 - Document deviations
Step 7:  Phase 53 - Complete IMPLEMENTATION-CHECKLIST.md
Step 8:  ⭐ MANDATORY: Run 0f audit before proceeding!
Step 9:  Address audit findings
Step 10: Proceed to Section 0g
```

---

## Critical Rule: Specs Are Truth

If code needs to differ from specs:

| Step | What To Do |
|------|------------|
| 1 | STOP |
| 2 | Document the proposed deviation |
| 3 | Get approval from human |
| 4 | Update the spec in 0c |
| 5 | Continue implementation |

**Never silently deviate.** Undocumented deviations are serious problems.

---

## Deviation Types

| Type | Example | Required Action |
|------|---------|-----------------|
| **Documented** | "Spec said X, but Y works better because..." | Approve, update spec |
| **Undocumented** | Code just does something different | 🚨 Critical issue! |

---

## What You Should Have When Done

| Document | Location |
|----------|----------|
| IMPLEMENTATION-ORDER.md | Phase 43 |
| Working backend code | /src/ or /app/ |
| BACKEND-REVIEW.md | Phase 47 |
| Working frontend code | /src/ or /app/ |
| INTEGRATION-VERIFICATION.md | Phase 51 |
| DEVIATION-REPORT.md | Phase 52 |
| IMPLEMENTATION-CHECKLIST.md | Phase 53 |

---

*Give PHASE-XX-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
