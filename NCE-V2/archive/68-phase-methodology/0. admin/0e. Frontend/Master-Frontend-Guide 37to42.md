# Master Frontend Guide — Phases 37-42

---
Section: 0e. Frontend
Phases: 37-42
Purpose: Guide for specifying frontend from mockups to implementation-ready specs
---

## Overview

This section transforms visual mockups and the backend API surface into complete frontend specifications. By the end, a developer (or Claude) can build the entire frontend with zero ambiguity.

---

## Prerequisites

Before starting 0e Frontend:

- [ ] 0d PreCode COMPLETE
- [ ] API-SURFACE.md exists and approved
- [ ] ERROR-CODES.md exists and approved
- [ ] TECH-STACK.md exists (frontend framework chosen)
- [ ] User has mockups/wireframes ready

---

## Phase Summary

| Phase | Name | Input | Output | Est. Time |
|-------|------|-------|--------|-----------|
| 37 | Frontend-Backend Contract | API-SURFACE.md, TYPES, ERRORS | FRONTEND-BACKEND-CONTRACT.md | 1-2 hours |
| 38a | Design Request | PAGES.md (if exists), requirements | DESIGN-REQUEST.md | 30 min |
| 38 | Design System | Mockups from design team | DESIGN-SYSTEM.md | 2-3 hours |
| 39 | Page Specs | Design system, contract | PAGES.md | 2-4 hours |
| 40 | Component Specs | Design system, pages | COMPONENTS.md | 3-5 hours |
| 41 | State Management | All above | STATE-MANAGEMENT.md | 1-2 hours |
| 42 | Frontend Ready Check | All above | FRONTEND-CHECKLIST.md | 30 min |

---

## Phase 37: Frontend-Backend Contract

### Purpose
Create the single binding artifact between frontend and backend. This is the ONLY document frontend should reference for API communication.

### Input
- API-SURFACE.md (Phase 30)
- ERROR-CODES.md (Phase 26)
- Shared TYPES (Phase 26)

### Output
- FRONTEND-BACKEND-CONTRACT.md

### Process
1. Extract endpoints relevant to frontend (exclude internal/admin if separate)
2. Extract types used in requests/responses
3. Extract error codes frontend must handle
4. Add mock data examples for each endpoint
5. Get approval

### Critical Rule
**Contract is projection only.** May NOT introduce new endpoints, parameters, or behaviours. If gaps found, escalate to 0c/0d.

### Files
- Prompt: `37-Frontend-Backend-Contract/PHASE-37-PROMPT.md`
- Template: `37-Frontend-Backend-Contract/FRONTEND-BACKEND-CONTRACT-TEMPLATE.md`

---

## Phase 38a: Design Request

### Purpose
Generate a design brief for the design team, written in plain English with no technical jargon.

### Input
- PAGES.md (if exists)
- Requirements / user stories
- Any existing brand guidelines

### Output
- DESIGN-REQUEST.md
- `/mockups/` folder (empty, with README)

### Process
1. Identify all screens needed
2. Identify key reusable elements
3. List required states (loading, error, empty)
4. Ask style/colour questions
5. Generate checklist with exact filenames
6. Create `/mockups/` folder

### Key Principle
**Write for designers, not developers.** No jargon. Explain why, not just what.

### Files
- Prompt: `38. Design-System/PHASE-38a-DESIGN-REQUEST-PROMPT.md`
- Template: `0. Admin/0e. Frontend/MOCKUP-REQUEST-TEMPLATE.md`

### Next Step
Wait for design team to deliver mockups, then proceed to Phase 38.

---

## Phase 38: Design System

### Purpose
Capture visual design from mockups in a structured, implementation-agnostic format.

### Input
- User-provided mockups (images)
- User-provided component breakdown (text describing structure)
- TECH-STACK.md (framework context)

### Output
- DESIGN-SYSTEM.md

### Process
1. User provides mockups with component breakdown
2. Extract design tokens (colors, typography, spacing)
3. Create component inventory
4. Define layout logical groups (spatial relationships in text)
5. Define global UX patterns (loading, error, empty states)
6. Note asset locations
7. Get approval

### What Design System Includes
- Design tokens
- Component inventory
- Layout patterns
- Global UX patterns (by ID)
- Assets reference
- Responsive breakpoints
- Accessibility baseline

### What Design System Excludes
- Pixel-perfect CSS
- Framework-specific class names
- Implementation code
- Component logic

### Files
- Prompt: `38-Design-System/PHASE-38-PROMPT.md`
- Template: `38-Design-System/DESIGN-SYSTEM-TEMPLATE.md`

---

## Phase 39: Page Specs

### Purpose
Define every page/route in the application.

### Input
- DESIGN-SYSTEM.md
- FRONTEND-BACKEND-CONTRACT.md

### Output
- PAGES.md (or individual PAGE-{name}.md if >500 LOC)

### Process
1. List all routes from mockups
2. For each page, define:
   - Route path and parameters
   - Purpose
   - Layout structure
   - Components used
   - API calls made
   - Route guards (auth requirements)
   - Navigation (where user can go)
3. Get approval

### Files
- Prompt: `39-Page-Specs/PHASE-39-PROMPT.md`
- Template: `39-Page-Specs/PAGES-TEMPLATE.md`

---

## Phase 40: Component Specs

### Purpose
Define every reusable UI component.

### Input
- DESIGN-SYSTEM.md
- PAGES.md

### Output
- COMPONENTS.md (or individual COMPONENT-{name}.md if >500 LOC)

### Process
1. Collect all components referenced in PAGES.md
2. Add any shared/atomic components from design system
3. For each component, define:
   - Props (inputs)
   - Internal state
   - Events (outputs)
   - Variants
   - Loading/error states (reference global patterns)
   - Accessibility
   - API calls (if any)
4. Verify no orphaned components
5. Get approval

### Files
- Prompt: `40-Component-Specs/PHASE-40-PROMPT.md`
- Template: `40-Component-Specs/COMPONENTS-TEMPLATE.md`

### Optional: Deep Component Specs

For complex components (data tables, modals, form wizards), use the optional Deep Spec pack.

**When to use:**
- Component has many variants/states
- Critical to user experience
- Significant accessibility requirements
- Complex keyboard/focus behaviour

**Location:** `0. Admin/0e. Frontend/optional-deep-specs/`

**Files:**
- DEEP-SPEC-INDEX.md (always start here)
- DEEP-SPEC-PROPS.md
- DEEP-SPEC-EVENTS.md
- DEEP-SPEC-STATE.md
- DEEP-SPEC-SLOTS.md
- DEEP-SPEC-STYLES.md
- DEEP-SPEC-A11Y.md
- DEEP-SPEC-EXAMPLES.md

**Usage:** Create `deep-spec/` folder inside component folder, add relevant templates.

---

## Phase 41: State Management

### Purpose
Define how state flows through the frontend application.

### Input
- All previous outputs
- TECH-STACK.md (state library choice)

### Output
- STATE-MANAGEMENT.md

### Process
1. Categorize state:
   - Server state (cached API responses)
   - Client state (UI state)
   - URL state (route params)
   - Form state (user input)
   - Persisted state (survives refresh)
2. Define global state shape
3. Define state update patterns
4. Document anti-patterns to avoid
5. Get approval

### Files
- Prompt: `41-State-Management/PHASE-41-PROMPT.md`
- Template: `41-State-Management/STATE-MANAGEMENT-TEMPLATE.md`

---

## Phase 42: Frontend Ready Check

### Purpose
Final verification that frontend is ready for implementation.

### Input
- All 0e outputs

### Output
- FRONTEND-CHECKLIST.md

### Process
1. Verify all documents exist and are approved
2. Verify no orphaned components
3. Verify all API calls mapped
4. Create API-MAPPING.md (component → endpoint mapping)
5. Verify no missing or orphaned endpoints
6. Complete A11Y checklist
7. Run accessibility tests (automated + manual)
8. Ask critical question
9. Run "Different AI" test
10. Get final approval

### Critical Question
> "Is there anything required to build the frontend that is not documented?"

### Different AI Test
> "Could a new AI with no conversation history build this frontend using only the 0e documents?"

### Files
- Prompt: `42-Frontend-Ready-Check/PHASE-42-PROMPT.md`
- Template: `42-Frontend-Ready-Check/FRONTEND-CHECKLIST-TEMPLATE.md`

---

## Folder Structure After 0e

```
{{project}}/
├── 0. Admin/
│   └── 0e. Frontend/
│       ├── Claude-frontend.md
│       ├── Master-Frontend-Guide 37to42.md
│       ├── FRONTEND-PROGRESS-TEMPLATE.md
│       └── FRONTEND-DECISION-NOTES-TEMPLATE.md
│
├── frontend/
│   ├── FRONTEND-BACKEND-CONTRACT.md   ← Phase 37
│   ├── DESIGN-SYSTEM.md               ← Phase 38
│   ├── PAGES.md                       ← Phase 39
│   ├── COMPONENTS.md                  ← Phase 40
│   └── STATE-MANAGEMENT.md            ← Phase 41
│
└── admin/
    └── FRONTEND-CHECKLIST.md          ← Phase 42
```

---

## File Split Rules

| Condition | Action |
|-----------|--------|
| Default | Single PAGES.md, single COMPONENTS.md |
| File > ~500 LOC | Split into individual files |
| Different domains | Split by domain |
| High cognitive load | Split logically |

**When split:**
```
frontend/
├── pages/
│   ├── PAGE-home.md
│   ├── PAGE-dashboard.md
│   └── ...
└── components/
    ├── COMPONENT-button.md
    ├── COMPONENT-card.md
    └── ...
```

---

## Completion Criteria

0e Frontend is COMPLETE when:

- [ ] Phase 37: FRONTEND-BACKEND-CONTRACT.md — APPROVED
- [ ] Phase 38: DESIGN-SYSTEM.md — APPROVED
- [ ] Phase 39: PAGES.md — APPROVED
- [ ] Phase 40: COMPONENTS.md — APPROVED
- [ ] Phase 41: STATE-MANAGEMENT.md — APPROVED
- [ ] Phase 42: FRONTEND-CHECKLIST.md — APPROVED
- [ ] No unresolved gaps or escalations
- [ ] "Different AI" test passed

---

## Next Section

After 0e Frontend → **0f Implementation** (Phase 43+)

---

## Quick Reference

| Need | Location |
|------|----------|
| Claude instructions | `Claude-frontend.md` |
| Progress tracking | `FRONTEND-PROGRESS-TEMPLATE.md` |
| Decision logging | `FRONTEND-DECISION-NOTES-TEMPLATE.md` |
| Phase 37 prompt | `37-Frontend-Backend-Contract/PHASE-37-PROMPT.md` |
| Phase 38 prompt | `38-Design-System/PHASE-38-PROMPT.md` |
| Phase 39 prompt | `39-Page-Specs/PHASE-39-PROMPT.md` |
| Phase 40 prompt | `40-Component-Specs/PHASE-40-PROMPT.md` |
| Phase 41 prompt | `41-State-Management/PHASE-41-PROMPT.md` |
| Phase 42 prompt | `42-Frontend-Ready-Check/PHASE-42-PROMPT.md` |
| Design request template | `0. Admin/0e. Frontend/MOCKUP-REQUEST-TEMPLATE.md` |
| Phase 38a prompt | `38. Design-System/PHASE-38a-DESIGN-REQUEST-PROMPT.md` |
| API mapping template | `42. Frontend-Ready-Check/API-MAPPING-TEMPLATE.md` |
| A11Y checklist template | `42. Frontend-Ready-Check/A11Y-CHECKLIST-TEMPLATE.md` |
| Deep spec pack (optional) | `0. Admin/0e. Frontend/optional-deep-specs/` |

---
Version: 1.0.0
Last Updated: {{timestamp}}
---
