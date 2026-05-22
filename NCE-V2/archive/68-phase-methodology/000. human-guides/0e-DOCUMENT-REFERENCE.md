# Section 0e: Complete Document Reference

---
**Section:** 0e Frontend
**Phases:** 37-42
**Location:** `0. Admin/0e. Frontend/` + Phase folders
**Status:** Phase structure defined, templates being built
---

## Admin Folder Documents

Location: `0. Admin/0e. Frontend/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-frontend.md** | Instructions for Claude. Defines how to process mockups and create frontend specs. | Claude reads this |
| **Master-Frontend-Guide 37to42.md** | Detailed guide for all frontend phases. | Reference for both |
| **FRONTEND-PROGRESS-TEMPLATE.md** | Template to track progress through phases. | You fill this in |
| **FRONTEND-DECISION-NOTES-TEMPLATE.md** | Template to log decisions. | You fill this in |

---

## Phase-by-Phase Documents

### Phase 37: Frontend Design Input

**Folder:** `37. Frontend-Design-Input/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-37-PROMPT.md** | Instructions for Claude. Tells Claude to capture your mockups and design system. | Give to Claude |
| **DESIGN-INPUT-TEMPLATE.md** | Template for capturing design tokens, color palettes, typography, spacing, UI patterns. | Claude creates DESIGN-INPUT.md |

**Output:** `DESIGN-INPUT.md` — Your designs documented

**How to provide mockups:**
- Upload screenshots/images
- Share Figma/Sketch exports
- Describe layouts in words
- Provide component lists

---

### Phase 38: Page Specs

**Folder:** `38. Page-Specs/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-38-PROMPT.md** | Instructions for Claude. Tells Claude to define each page/route in the application. | Give to Claude |
| **PAGE-SPEC-TEMPLATE.md** | Template for individual page - route, layout, components used, data needed. | Claude creates PAGE-SPEC-{name}.md per page |

**Output:** `PAGE-SPEC-{name}.md` (one per page)

---

### Phase 39: Component Specs

**Folder:** `39. Component-Specs/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-39-PROMPT.md** | Instructions for Claude. Tells Claude to define each UI component. | Give to Claude |
| **COMPONENT-SPEC-TEMPLATE.md** | Template for individual component - props, states, variants, accessibility. | Claude creates COMPONENT-SPEC-{name}.md per component |

**Output:** `COMPONENT-SPEC-{name}.md` (one per component)

---

### Phase 40: State Management

**Folder:** `40. State-Management/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-40-PROMPT.md** | Instructions for Claude. Tells Claude to define how data flows in the frontend. | Give to Claude |
| **STATE-SPEC-TEMPLATE.md** | Template for state management - stores, actions, selectors, persistence. | Claude creates STATE-SPEC.md |

**Output:** `STATE-SPEC.md` — How frontend state is managed

---

### Phase 41: API Integration Mapping

**Folder:** `41. API-Integration-Mapping/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-41-PROMPT.md** | Instructions for Claude. Tells Claude to map frontend actions to backend API calls. | Give to Claude |
| **API-MAPPING-TEMPLATE.md** | Template for mapping - which component calls which endpoint, data transformations. | Claude creates API-MAPPING.md |
| **FRONTEND-BACKEND-CONTRACT-TEMPLATE.md** | Template for the binding agreement between frontend and backend. | Claude creates FRONTEND-BACKEND-CONTRACT.md |

**Outputs:**
- `API-MAPPING.md` — Component → API endpoint mapping
- `FRONTEND-BACKEND-CONTRACT.md` — The official contract

---

### Phase 42: Frontend Ready Check

**Folder:** `42. Frontend-Ready-Check/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-42-PROMPT.md** | Instructions for Claude. Tells Claude to verify all frontend specs are complete. | Give to Claude |
| **FRONTEND-CHECKLIST-TEMPLATE.md** | Final checklist - all pages? All components? Accessibility? | Claude creates FRONTEND-CHECKLIST.md |
| **A11Y-CHECKLIST-TEMPLATE.md** | Accessibility checklist - WCAG A/AA compliance. | Claude creates A11Y-CHECKLIST.md |

**Outputs:**
- `FRONTEND-CHECKLIST.md` — Final verification
- `A11Y-CHECKLIST.md` — Accessibility requirements

---

## Order to Run Section 0e

```
Step 1:  Read Claude-frontend.md (understand section)
Step 2:  Gather your designs (mockups, wireframes, descriptions)
Step 3:  Phase 37 - Create DESIGN-INPUT.md
Step 4:  Phase 38 - Create PAGE-SPEC-*.md for each page
Step 5:  Phase 39 - Create COMPONENT-SPEC-*.md for each component
Step 6:  Phase 40 - Create STATE-SPEC.md
Step 7:  Phase 41 - Create API-MAPPING.md + FRONTEND-BACKEND-CONTRACT.md
Step 8:  Phase 42 - Create FRONTEND-CHECKLIST.md + A11Y-CHECKLIST.md
Step 9:  (Optional) Run 0e audit
Step 10: Proceed to Section 0f
```

---

## Key Document: FRONTEND-BACKEND-CONTRACT.md

This document is critical. It defines:

| Section | What It Contains |
|---------|------------------|
| API Endpoints | All endpoints frontend will call |
| Request Formats | What data frontend sends |
| Response Formats | What data frontend receives |
| Error Handling | How errors are returned |
| Authentication | How auth works |
| Rate Limits | Any limits to respect |

This becomes the **binding agreement** between frontend and backend teams.

---

## What You Should Have When Done

| Document | Location | Count |
|----------|----------|-------|
| DESIGN-INPUT.md | Phase 37 | 1 |
| PAGE-SPEC-*.md | Phase 38 | 1 per page |
| COMPONENT-SPEC-*.md | Phase 39 | 1 per component |
| STATE-SPEC.md | Phase 40 | 1 |
| API-MAPPING.md | Phase 41 | 1 |
| FRONTEND-BACKEND-CONTRACT.md | Phase 41 | 1 |
| FRONTEND-CHECKLIST.md | Phase 42 | 1 |
| A11Y-CHECKLIST.md | Phase 42 | 1 |

---

*Give PHASE-XX-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
