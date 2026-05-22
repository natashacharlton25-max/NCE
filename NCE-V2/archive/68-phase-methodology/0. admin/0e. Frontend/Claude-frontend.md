# Claude Instructions — 0e. Frontend

---
Section: 0e. Frontend
Phases: 37-42
Purpose: Specify frontend from mockups through to implementation-ready specs
---

## Role

You are specifying the frontend layer of the application. You will:
- Create a binding contract between frontend and backend
- Capture design system from mockups
- Specify every page and component
- Define state management
- Verify everything is ready for implementation

---

## What's Already Done (DO NOT CHANGE)

These outputs from previous sections are **locked**:

| Section | Key Outputs | Status |
|---------|-------------|--------|
| 0c Full Specs | All component specs, FUNCTIONS.md, TYPES | LOCKED |
| 0d PreCode | API-SURFACE.md, DATABASE-SCHEMA.md, TECH-STACK.md | LOCKED |

**If 0e reveals issues in these:** Do NOT fix here. Document and escalate.

---

## Critical Rules

### Rule 0: Section Non-Goals (Explicitly Deferred)

These are intentionally NOT part of 0e Frontend:

| Deferred Item | Why Deferred | Where It Goes |
|---------------|--------------|---------------|
| CSS implementation | Implementation detail | 0f Implementation |
| Framework boilerplate | Implementation detail | 0f Implementation |
| Bundle configuration | Build tooling | 0f Implementation |
| Test code | Testing phase | 0g Testing |
| CI/CD pipeline | Prerelease | 0h Prerelease |
| Analytics integration code | Implementation detail | 0f Implementation |
| Performance optimization code | Implementation detail | 0f Implementation |

**If you find yourself writing code → STOP. You are specifying, not implementing.**

### Rule 1: Contract is Projection Only

The FRONTEND-BACKEND-CONTRACT.md may NOT introduce:
- New endpoints
- New parameters
- New behaviours
- New error codes

It is a **projection** of API-SURFACE.md, not a modification.

If gaps are found → Document in FRONTEND-DECISION-NOTES.md → Escalate to 0c/0d

### Rule 2: Design System Has Boundaries

DESIGN-SYSTEM.md includes:
- Design tokens (colors, typography, spacing)
- Component inventory
- Layout patterns
- Global UX patterns
- Assets reference

DESIGN-SYSTEM.md does NOT include:
- Pixel-perfect CSS
- Framework-specific class names
- Implementation code
- Component logic

### Rule 3: Specs Reference Patterns, Don't Duplicate

When specifying loading/error states:
- Define pattern ONCE in DESIGN-SYSTEM.md (e.g., `LOADING_SKELETON`)
- Reference by ID in component specs
- Don't repeat the pattern definition

### Rule 4: File Split Rules

| Default | Single PAGES.md, single COMPONENTS.md |
|---------|---------------------------------------|
| Split when | File approaches ~500 LOC |
| Split when | Different domains own different pages/components |
| Split when | Cognitive load is high |

### Rule 5: Every Component Has a Home

- No orphaned components (must be used by at least one page)
- No missing components (pages can't reference undefined components)

### Rule 6: Every API Call is Mapped

- Pages define which endpoints they call
- Components define which endpoints they call
- Every endpoint in contract must be called somewhere

---

## AI Role Boundaries

### Claude MAY:
- Extract design tokens from mockups
- Identify components from visual inspection
- Propose state management patterns
- Flag gaps and inconsistencies
- Suggest patterns from best practices
- Ask clarifying questions

### Claude MUST NOT:
- Add endpoints not in API-SURFACE.md
- Invent features not in mockups
- Make architectural decisions without approval
- Skip approval gates
- Write implementation code
- Choose libraries not in TECH-STACK.md
- "Fix" perceived issues in locked 0c/0d documents

### Claude MUST:
- Document every gap found
- Escalate rather than assume
- Get explicit APPROVE before proceeding
- Reference sources for all specifications
- Flag when information is missing

---

## Workflow

```
Phase 37: Create Frontend-Backend Contract
    ↓
Phase 38: Capture Design System from mockups
    ↓
Phase 39: Specify all pages
    ↓
Phase 40: Specify all components
    ↓
Phase 41: Define state management
    ↓
Phase 42: Final verification
```

---

## Phase Overview

| Phase | Name | Purpose | Output |
|-------|------|---------|--------|
| 37 | Frontend-Backend Contract | Create binding contract | FRONTEND-BACKEND-CONTRACT.md |
| 38a | Design Request | Brief design team | DESIGN-REQUEST.md |
| 38 | Design System | Capture design from mockups | DESIGN-SYSTEM.md |
| 39 | Page Specs | Define all pages/routes | PAGES.md |
| 40 | Component Specs | Define all UI components | COMPONENTS.md |
| 41 | State Management | Define state architecture | STATE-MANAGEMENT.md |
| 42 | Frontend Ready Check | Final verification | FRONTEND-CHECKLIST.md |

---

## Processing Order

1. **Phase 37** — Must complete first (contract grounds everything)
2. **Phase 38** — Requires user mockups
3. **Phase 39** — Can reference design system
4. **Phase 40** — Can reference design system and pages
5. **Phase 41** — Can reference all above
6. **Phase 42** — Final check

---

## Progress & Decision Tracking

Throughout this section, maintain:

### Progress Tracking

**Create and maintain `FRONTEND-PROGRESS.md`** using `FRONTEND-PROGRESS-TEMPLATE.md`:
- Mark phases complete
- Track page/component spec completion
- Note blockers

**Location:** `{{project}}/admin/FRONTEND-PROGRESS.md`

### Decision Logging

**Create and maintain `FRONTEND-DECISION-NOTES.md`** using `FRONTEND-DECISION-NOTES-TEMPLATE.md`:
- UI/UX decisions
- Component boundary decisions
- State management choices
- Deferred items

**Location:** `{{project}}/admin/FRONTEND-DECISION-NOTES.md`

**Create these files at the start of Phase 37 if they don't exist.**

---

## Input Requirements

### Phase 37 Inputs
- API-SURFACE.md (from Phase 30)
- ERROR-CODES.md (from Phase 26)
- Shared TYPES (from Phase 26)

### Phase 38 Inputs
- User-provided mockups (images)
- User-provided component breakdown (text)
- TECH-STACK.md (for framework context)

### Phase 39+ Inputs
- All previous phase outputs
- FRONTEND-BACKEND-CONTRACT.md
- DESIGN-SYSTEM.md

---

## Approval Gates

**Every phase requires explicit approval.**

| Phase | Gate |
|-------|------|
| 37 | Contract approved — "APPROVE" |
| 38 | Design system approved — "APPROVE" |
| 39 | Pages approved — "APPROVE" |
| 40 | Components approved — "APPROVE" |
| 41 | State management approved — "APPROVE" |
| 42 | Final checklist approved — "APPROVE" |

**Do NOT proceed without approval.**

---

## When Issues Arise

### Contract Gap Found (Phase 37)
1. Document the gap
2. Note which spec file should have it
3. Add to FRONTEND-DECISION-NOTES.md
4. Ask user: "This endpoint is needed but not in API-SURFACE.md. Should we roll back to Phase 30?"

### Design Ambiguity (Phase 38)
1. Ask user for clarification
2. Document decision in FRONTEND-DECISION-NOTES.md
3. Continue

### Component Needs New Endpoint (Phase 40)
1. **Do NOT add endpoint to contract**
2. Document the need
3. Escalate: "Component X needs endpoint Y which doesn't exist"
4. User decides: roll back or defer

---

## Output Locations

```
{{project}}/
├── frontend/
│   ├── FRONTEND-BACKEND-CONTRACT.md
│   ├── DESIGN-REQUEST.md          ← Phase 38a
│   ├── DESIGN-SYSTEM.md
│   ├── PAGES.md
│   ├── COMPONENTS.md
│   ├── STATE-MANAGEMENT.md
│   ├── API-MAPPING.md             ← Phase 42
│   └── A11Y-CHECKLIST.md          ← Phase 42
├── mockups/                        ← Design team delivers here
│   └── (design files)
└── admin/
    └── FRONTEND-CHECKLIST.md
```

---

## End Condition

0e Frontend is COMPLETE when:

- [ ] FRONTEND-BACKEND-CONTRACT.md approved
- [ ] DESIGN-REQUEST.md created (for design team)
- [ ] DESIGN-SYSTEM.md approved
- [ ] PAGES.md approved (all pages specified)
- [ ] COMPONENTS.md approved (all components specified)
- [ ] STATE-MANAGEMENT.md approved
- [ ] API-MAPPING.md approved
- [ ] FRONTEND-CHECKLIST.md approved
- [ ] No orphaned components
- [ ] No missing API mappings
- [ ] No missing endpoints in API mapping
- [ ] A11Y-CHECKLIST.md approved
- [ ] Accessibility tests pass
- [ ] "Different AI" test passed

**Next:** 0f Implementation (Phase 43+)

---
Version: 1.0.0
Last Updated: {{timestamp}}
---
