# Master Implementation Guide — Phases 43 to 53

---
Section: 0f. Implementation
Purpose: Complete workflow guide for implementing specifications as working code
Version: v1.0.0
---

## Overview

This guide covers the Implementation section (0f), which transforms all specifications from 0c-0e into working software.

**Core Principle:** 0f implements, does not decide. All design decisions were made in earlier sections.

**Execution Model:** Backend-first by default. Frontend begins after backend is verified.

---

## Prerequisites

Before starting 0f, verify:

### Documents Required

| Document | From | Contains |
|----------|------|----------|
| TECH-STACK.md | 0d | Frameworks, languages, tools |
| REPO-STRUCTURE.md | 0d | Folder layout |
| ENVIRONMENT.md | 0d | Environment variables |
| DATABASE-SCHEMA.md | 0c/0d | Tables, relationships |
| API-SURFACE.md | 0c/0d | All endpoints |
| TYPES.md | 0c | Data type definitions |
| ERROR-CODES.md | 0c | Error handling patterns |
| FRONTEND-BACKEND-CONTRACT.md | 0e | API contract for frontend |
| DESIGN-SYSTEM.md | 0e | Tokens, typography, colors |
| PAGES.md | 0e | Routes, guards, layouts |
| COMPONENTS.md | 0e | Props, events, states |
| STATE-MANAGEMENT.md | 0e | State architecture |
| API-MAPPING.md | 0e | Component-to-endpoint mapping |
| A11Y-CHECKLIST.md | 0e | Accessibility requirements |
| 0e-to-0f-HANDOFF.md | 0e | Entry contract |

### Approvals Required

- [ ] All 0e documents marked APPROVED
- [ ] FRONTEND-CHECKLIST.md shows all items complete
- [ ] 0e-to-0f-HANDOFF.md signed off

---

## Phase Summary

| Phase | Name | Purpose | Output | Collapse? |
|-------|------|---------|--------|-----------|
| 43 | Environment Setup | Dev environment, shared types | Project skeleton, shared-types/ | Yes |
| 44 | Database Implementation | Schema, migrations | Migration files, seed data | Yes |
| 45 | Backend Core | Auth, middleware, errors | Server infrastructure | Yes |
| 46 | API Implementation | All endpoints | Endpoint handlers | Yes |
| 47 | Backend Verification | Verify backend works | Verification report | **NEVER** |
| 48 | Frontend Setup | Framework, routing | Frontend skeleton | Yes |
| 49 | Design System Implementation | Tokens, styles | CSS/tokens files | Yes |
| 50 | Component Implementation | All components | Component files | Yes |
| 51 | Page Implementation | All pages | Page files | Yes |
| 52 | Integration | Wire frontend to backend | Working data flow | Yes |
| 53 | Implementation Verification | Final check | Checklist, handoff | **NEVER** |

### Minimum Required Artifacts

Even when phases are collapsed for small projects, these artifacts **must exist**:

| Artifact | Purpose | Required |
|----------|---------|----------|
| IMPLEMENTATION-LOG.md | Proof of work | Always |
| BACKEND-VERIFICATION-REPORT.md | Phase 47 evidence | Always |
| IMPLEMENTATION-CHECKLIST.md | Final verification | Always |
| shared-types/ | Type definitions | Always |

**Collapsing phases does not remove documentation requirements.**

Phases may execute together, but verification artifacts preserve traceability.

### Implementation Guidance

Phases should aim for **functional completeness, not perfection**. Optimisation belongs after verification, not during initial implementation. This protects momentum.

---

## Phase Details

### Phase 43: Environment Setup

**Purpose:** Create runnable project skeleton with shared types.

**Input:**
- TECH-STACK.md
- REPO-STRUCTURE.md
- ENVIRONMENT.md
- TYPES.md

**Process:**
1. Create folder structure per REPO-STRUCTURE.md
2. Initialize package managers (npm, pip, etc.)
3. Install all dependencies per TECH-STACK.md
4. Set up environment variables per ENVIRONMENT.md
5. Run Environment Gap Check (compare spec vs library requirements)
6. Create shared-types/ folder
7. Generate TypeScript types from TYPES.md
8. Configure IDE settings (.editorconfig, etc.)
9. Initialize git repository
10. Verify dev server starts

**Output:**
- Empty but runnable project
- shared-types/ with generated types
- All dependencies installed
- Dev server runs

**Files:**
- Prompt: `43. Environment-Setup/PHASE-43-PROMPT.md`

**Checklist:**
- [ ] Folder structure matches REPO-STRUCTURE.md
- [ ] All dependencies install without errors
- [ ] Environment variables configured
- [ ] Environment Gap Check passed
- [ ] shared-types/ generated from TYPES.md
- [ ] Types are importable from both backend and frontend
- [ ] Dev server starts
- [ ] Git initialized with .gitignore

---

### Phase 44: Database Implementation

**Purpose:** Create database schema matching specification.

**Input:**
- DATABASE-SCHEMA.md
- shared-types/ (from Phase 43)

**Process:**
1. Create migration files for each table
2. Define all columns, types, constraints
3. Create foreign key relationships
4. Add indexes per specification
5. Create seed data for development
6. Run migrations
7. Run schema validation
8. Update shared-types if ORM generates types

**Output:**
- Migration files
- Seed data files
- Working database
- Schema validation proof

**Files:**
- Prompt: `44. Database-Implementation/PHASE-44-PROMPT.md`

**Checklist:**
- [ ] All tables from DATABASE-SCHEMA.md created
- [ ] All columns match spec (name, type, constraints)
- [ ] Foreign keys configured
- [ ] Indexes created
- [ ] Seed data loads successfully
- [ ] Schema validation passes
- [ ] Shared types align with schema

---

### Phase 45: Backend Core

**Purpose:** Build server infrastructure before endpoints.

**Input:**
- TECH-STACK.md
- ERROR-CODES.md
- API-SURFACE.md (auth section)
- shared-types/

**Process:**
1. Set up web framework per TECH-STACK.md
2. Configure middleware (CORS, logging, body parsing)
3. Implement authentication system
4. Implement authorization/permissions
5. Set up error handling per ERROR-CODES.md
6. Create response formatters
7. Set up request validation
8. Import and use shared types

**Output:**
- Running server
- Auth system (login, logout, refresh)
- Error handling middleware
- Request validation

**Files:**
- Prompt: `45. Backend-Core/PHASE-45-PROMPT.md`

**Checklist:**
- [ ] Server starts and accepts requests
- [ ] Auth endpoints work (per API-SURFACE.md)
- [ ] Error responses match ERROR-CODES.md
- [ ] Middleware chain configured
- [ ] Request validation works
- [ ] Shared types used for all responses

---

### Phase 46: API Implementation

**Purpose:** Implement all API endpoints.

**Input:**
- API-SURFACE.md
- ERROR-CODES.md
- shared-types/
- Database (from Phase 44)
- Backend core (from Phase 45)

**Process:**
1. For each endpoint in API-SURFACE.md:
   - Create route handler
   - Add input validation
   - Add authorization check
   - Implement business logic
   - Connect to database
   - Return typed response
   - Handle all error cases
2. Group endpoints by resource
3. Use shared types for all requests/responses

**Output:**
- All endpoints functional
- Typed responses
- Error handling complete

**Files:**
- Prompt: `46. API-Implementation/PHASE-46-PROMPT.md`

**Checklist:**
- [ ] Every endpoint in API-SURFACE.md implemented
- [ ] Request validation per spec
- [ ] Authorization enforced per spec
- [ ] Response shapes match shared types
- [ ] Error responses match ERROR-CODES.md
- [ ] All CRUD operations work

---

### Phase 47: Backend Verification

**Purpose:** Verify backend is complete and correct before frontend. **NEVER COLLAPSE.**

**Input:**
- API-SURFACE.md
- ERROR-CODES.md
- All backend code

**Process:**
1. Test every endpoint:
   - Happy path (valid request → expected response)
   - Auth path (unauthorized → 401/403)
   - Validation path (invalid input → 400)
   - Error path (server error → 500 with code)
2. Verify response types match shared-types
3. Check auth flows end-to-end
4. Document any issues found
5. Fix issues or file DEVIATION-REPORT
6. Create BACKEND-VERIFICATION-REPORT.md

**Output:**
- BACKEND-VERIFICATION-REPORT.md
- All issues fixed or documented
- Backend ready for frontend integration

**Files:**
- Prompt: `47. Backend-Verification/PHASE-47-PROMPT.md`
- Template: `47. Backend-Verification/BACKEND-VERIFICATION-TEMPLATE.md`

**Checklist:**
- [ ] All endpoints tested
- [ ] Happy paths work
- [ ] Error paths return correct errors
- [ ] Auth flows complete
- [ ] Type contracts honoured
- [ ] No blocking issues
- [ ] BACKEND-VERIFICATION-REPORT.md created

---

### Phase 48: Frontend Setup

**Purpose:** Initialize frontend application with routing.

**Input:**
- TECH-STACK.md
- REPO-STRUCTURE.md
- PAGES.md
- shared-types/

**Process:**
1. Initialize frontend framework per TECH-STACK.md
2. Set up routing per PAGES.md
3. Create base layout structure
4. Configure build tooling
5. Set up CSS/styling approach
6. Import shared-types into frontend
7. Create placeholder pages for all routes

**Output:**
- Frontend app runs
- All routes navigate
- Base layout in place
- Shared types available

**Files:**
- Prompt: `48. Frontend-Setup/PHASE-48-PROMPT.md`

**Checklist:**
- [ ] Frontend dev server runs
- [ ] All routes from PAGES.md exist
- [ ] Navigation between routes works
- [ ] Base layout renders
- [ ] Build completes without errors
- [ ] Shared types imported and usable

---

### Phase 49: Design System Implementation

**Purpose:** Implement visual foundation from design specs.

**Input:**
- DESIGN-SYSTEM.md
- Mockups (if available)

**Process:**
1. Create CSS variables for all tokens
2. Implement typography scale
3. Implement color system (light + dark if specified)
4. Implement spacing system
5. Create base component styles
6. Set up responsive breakpoints
7. Verify against DESIGN-SYSTEM.md

**Output:**
- tokens.css or equivalent
- Base styles applied
- Theme system working (if specified)

**Files:**
- Prompt: `49. Design-System-Implementation/PHASE-49-PROMPT.md`

**Checklist:**
- [ ] All tokens from DESIGN-SYSTEM.md implemented
- [ ] Typography matches spec
- [ ] Colors match spec
- [ ] Spacing matches spec
- [ ] Responsive breakpoints work
- [ ] Dark mode works (if specified)

---

### Phase 50: Component Implementation

**Purpose:** Build all UI components per specification.

**Input:**
- COMPONENTS.md
- DESIGN-SYSTEM.md
- A11Y-CHECKLIST.md
- shared-types/
- optional-deep-specs/ (for complex components)

**Process:**
1. For each component in COMPONENTS.md:
   - Create component file
   - Add all props (typed from shared-types)
   - Add all events
   - Implement all variants
   - Implement all states
   - Add accessibility attributes
2. Use deep specs for complex components
3. Follow component patterns from ARCHITECTURE-PATTERNS.md

**Output:**
- All components functional
- Props/events match spec
- Accessibility requirements met

**Files:**
- Prompt: `50. Component-Implementation/PHASE-50-PROMPT.md`

**Checklist:**
- [ ] Every component in COMPONENTS.md implemented
- [ ] Props match spec (types, required/optional)
- [ ] Events match spec
- [ ] All variants work
- [ ] All states work (hover, focus, disabled, etc.)
- [ ] Accessibility attributes present
- [ ] Deep spec requirements met (where applicable)

---

### Phase 51: Page Implementation

**Purpose:** Build all pages using components.

**Input:**
- PAGES.md
- COMPONENTS.md
- STATE-MANAGEMENT.md

**Process:**
1. For each page in PAGES.md:
   - Create page file
   - Compose from components
   - Add route guards (if specified)
   - Set up page-level state
   - Add loading state
   - Add error state
   - Add empty state (for lists)
2. Follow state patterns from STATE-MANAGEMENT.md

**Output:**
- All pages functional
- Route guards working
- States handled

**Files:**
- Prompt: `51. Page-Implementation/PHASE-51-PROMPT.md`

**Checklist:**
- [ ] Every page in PAGES.md implemented
- [ ] Pages composed from specified components
- [ ] Route guards work
- [ ] Loading states show
- [ ] Error states show
- [ ] Empty states show (where applicable)

---

### Phase 52: Integration

**Purpose:** Connect frontend to backend.

**Input:**
- FRONTEND-BACKEND-CONTRACT.md
- API-MAPPING.md
- STATE-MANAGEMENT.md
- shared-types/

**Process:**
1. Set up API client/fetch utilities
2. For each entry in API-MAPPING.md:
   - Wire component to endpoint
   - Handle loading state
   - Handle error state
   - Handle success state
3. Set up global state per STATE-MANAGEMENT.md
4. Test full data flows
5. Verify type safety end-to-end

**Output:**
- Frontend fetches from backend
- Data flows correctly
- State updates properly
- No type mismatches

**Files:**
- Prompt: `52. Integration/PHASE-52-PROMPT.md`

**Checklist:**
- [ ] API client configured
- [ ] Every API call in API-MAPPING.md connected
- [ ] Data displays correctly
- [ ] Mutations work (create, update, delete)
- [ ] State updates on data changes
- [ ] Error handling works end-to-end
- [ ] No type errors or `any` leaks

---

### Phase 53: Implementation Verification

**Purpose:** Final verification before handoff to testing. **NEVER COLLAPSE.**

**Input:**
- All specs from 0c, 0d, 0e
- All implemented code
- IMPLEMENTATION-LOG.md

**Process:**
1. Walk through every spec document
2. Verify implementation matches
3. Check performance against DESIGN-SYSTEM.md
4. Check accessibility against A11Y-CHECKLIST.md
5. Audit commit history
6. Document any deviations
7. Complete IMPLEMENTATION-CHECKLIST.md
8. Compile final IMPLEMENTATION-LOG.md

**Output:**
- IMPLEMENTATION-CHECKLIST.md (filled)
- IMPLEMENTATION-LOG.md (complete)
- Ready for 0g Testing

**Files:**
- Prompt: `53. Implementation-Verification/PHASE-53-PROMPT.md`
- Template: `53. Implementation-Verification/IMPLEMENTATION-CHECKLIST-TEMPLATE.md`

**Checklist:**
- [ ] All backend endpoints match API-SURFACE.md
- [ ] All frontend pages match PAGES.md
- [ ] All components match COMPONENTS.md
- [ ] State matches STATE-MANAGEMENT.md
- [ ] Performance constraints met (or documented)
- [ ] Accessibility basics pass
- [ ] Commit history clean and meaningful
- [ ] No undocumented deviations
- [ ] IMPLEMENTATION-CHECKLIST.md complete

---

## Folder Structure After 0f

```
project/
├── src/                          # Per REPO-STRUCTURE.md
│   ├── backend/
│   │   ├── routes/
│   │   ├── middleware/
│   │   ├── models/
│   │   └── ...
│   ├── frontend/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── stores/
│   │   └── ...
│   └── shared/
│       └── types/                # shared-types/
├── migrations/
├── seeds/
├── docs/
│   ├── IMPLEMENTATION-LOG.md
│   ├── IMPLEMENTATION-CHECKLIST.md
│   └── deviations/
│       └── DEV-001.md (if any)
└── ...
```

---

## Completion Criteria

0f Implementation is complete when:

1. **Backend Complete**
   - [ ] All tables exist per DATABASE-SCHEMA.md
   - [ ] All endpoints exist per API-SURFACE.md
   - [ ] Auth system works
   - [ ] Error handling works
   - [ ] BACKEND-VERIFICATION-REPORT.md shows all pass

2. **Frontend Complete**
   - [ ] All pages exist per PAGES.md
   - [ ] All components exist per COMPONENTS.md
   - [ ] Design system implemented per DESIGN-SYSTEM.md
   - [ ] State management per STATE-MANAGEMENT.md

3. **Integration Complete**
   - [ ] Frontend connects to backend
   - [ ] All API calls mapped per API-MAPPING.md
   - [ ] Data flows correctly

4. **Verification Complete**
   - [ ] IMPLEMENTATION-CHECKLIST.md all checked
   - [ ] IMPLEMENTATION-LOG.md complete
   - [ ] All deviations documented
   - [ ] Commit history audited

---

## Next Section

After 0f Implementation:

**0g Testing** — Create and execute test plans

Use `0f-to-0g-HANDOFF.md` to verify handoff requirements.

---

## Quick Reference

| Phase | Key Output | Never Collapse |
|-------|------------|----------------|
| 43 | shared-types/ | |
| 44 | migrations, seeds | |
| 45 | server, auth | |
| 46 | all endpoints | |
| 47 | verification report | ✅ |
| 48 | frontend skeleton | |
| 49 | tokens.css | |
| 50 | all components | |
| 51 | all pages | |
| 52 | working integration | |
| 53 | checklist complete | ✅ |

---
