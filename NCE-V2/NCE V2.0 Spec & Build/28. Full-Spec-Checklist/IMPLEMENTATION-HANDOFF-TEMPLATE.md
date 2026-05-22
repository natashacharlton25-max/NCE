# Implementation Handoff

---
Project: {{project_name}}
Version: {{version}}
Date: {{timestamp}}
Target: Claude Code Implementation
---

## Purpose

This document provides Claude Code with everything needed to implement the specified system. Read this first, then dive into specific specs as needed.

---

## Project Overview

### What We're Building

{{2-3 sentences describing the project}}

### Target Stack

| Layer | Technology | Notes |
|-------|------------|-------|
| Frontend | {{Svelte/Astro/React}} | |
| Backend | {{Node/Python/etc}} | |
| Database | {{Supabase/PostgreSQL/etc}} | |
| Auth | {{Provider}} | |
| Hosting | {{Vercel/Fly.io/etc}} | |

### Key Outcomes

1. {{Primary outcome}}
2. {{Secondary outcome}}
3. {{Tertiary outcome}}

---

## Architecture Summary

### System Map

```
┌─────────────────────────────────────────────────────────────┐
│                        FRONTEND                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │   Pages     │  │ Components  │  │   Stores    │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└────────────────────────────┬────────────────────────────────┘
                             │ API Calls
┌────────────────────────────▼────────────────────────────────┐
│                        BACKEND                               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │ {{System1}} │  │ {{System2}} │  │ {{System3}} │          │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘          │
│         │                │                │                  │
│  ┌──────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐          │
│  │ Subsystems  │  │ Subsystems  │  │ Subsystems  │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└────────────────────────────┬────────────────────────────────┘
                             │
┌────────────────────────────▼────────────────────────────────┐
│                     INTEGRATIONS                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │  {{Prov1}}  │  │  {{Prov2}}  │  │  {{Prov3}}  │          │
│  │  └─Service  │  │  └─Service  │  │  └─Service  │          │
│  │  └─Service  │  │  └─Service  │  │  └─Service  │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
```

### Component Inventory

| Type | Count | Components |
|------|-------|------------|
| Systems | {{n}} | {{list}} |
| Subsystems | {{n}} | {{list}} |
| Integrations (Providers) | {{n}} | {{list}} |
| Integration Services | {{n}} | {{list}} |
| Libraries | {{n}} | {{list}} |

---

## Critical Information

### Must-Follow Rules

These rules are non-negotiable:

1. **Error Codes** — Use registered codes from ERROR-CODES.md only
2. **Shared Types** — Import from SCHEMAS.md, never redefine
3. **Naming** — Follow CONVENTIONS.md exactly
4. **Security** — Follow SECURITY-STANDARDS.md for all auth/data handling
5. **API Patterns** — Follow API-STANDARDS.md for all functions

### Key Architectural Decisions

| Decision | Choice | Rationale | Location |
|----------|--------|-----------|----------|
| {{decision}} | {{choice}} | {{why}} | {{component}}/spec/DECISIONS.md |
| {{decision}} | {{choice}} | {{why}} | |

### Critical Constraints

| Constraint | Limit | Consequence |
|------------|-------|-------------|
| File size | ~300 LOC max | Split if larger |
| Component size | ~1500 LOC max | Decompose if larger |
| API response time | {{ms}} | Optimize or cache |
| {{constraint}} | {{limit}} | {{consequence}} |

---

## Implementation Order

### Phase 1: Foundation (Week 1)

Build these first — no dependencies on other project code.

| Order | Component | Type | Spec Location |
|-------|-----------|------|---------------|
| 1.1 | lib-validation | Library | /libraries/lib-validation/spec/ |
| 1.2 | lib-errors | Library | /libraries/lib-errors/spec/ |
| 1.3 | lib-ids | Library | /libraries/lib-ids/spec/ |
| 1.4 | lib-datetime | Library | /libraries/lib-datetime/spec/ |

**Milestone:** All libraries pass unit tests

### Phase 2: Integrations (Week 2)

Build external service wrappers.

| Order | Provider | Service | Spec Location |
|-------|----------|---------|---------------|
| 2.1 | {{provider}} | {{service}} | /integrations/{{provider}}/{{service}}/spec/ |
| 2.2 | {{provider}} | {{service}} | |

**Milestone:** All integrations connect to sandboxes

### Phase 3: Core Systems (Week 3)

Build primary business logic.

| Order | System | Depends On | Spec Location |
|-------|--------|------------|---------------|
| 3.1 | {{system}} | lib-* | /systems/{{system}}/spec/ |
| 3.2 | {{system}} | {{deps}} | |

**Milestone:** Core CRUD operations work

### Phase 4: Subsystems (Week 4)

Build system internals.

| Order | Subsystem | Parent | Spec Location |
|-------|-----------|--------|---------------|
| 4.1 | {{subsystem}} | {{parent}} | /systems/{{parent}}/{{subsystem}}/spec/ |

**Milestone:** All business logic complete

### Phase 5: Integration & Testing (Week 5)

Wire everything together.

| Task | Components | Success Criteria |
|------|------------|------------------|
| E2E flow 1 | {{components}} | {{criteria}} |
| E2E flow 2 | {{components}} | {{criteria}} |

**Milestone:** All E2E tests pass

---

## Where to Find Things

### Spec File Locations

```
project/
├── 0. Admin/
│   └── 0c. Full Specs/
│       ├── SCHEMAS.md              ← Shared types
│       ├── ERROR-CODES.md          ← All error codes
│       ├── CONVENTIONS.md          ← Naming rules
│       ├── GLOSSARY.md             ← Domain terms
│       ├── API-STANDARDS.md        ← Function patterns
│       ├── SECURITY-STANDARDS.md   ← Security rules
│       └── REPO-SPEC.md            ← Repository setup
│
├── systems/
│   └── {{system}}/
│       ├── SYSTEM.md               ← System definition
│       ├── spec/                   ← Full specifications
│       │   ├── INDEX.md            ← Start here
│       │   ├── OVERVIEW.md         ← Purpose & boundaries
│       │   ├── FUNCTIONS.md        ← API details
│       │   ├── TYPES.md            ← Data structures
│       │   ├── BEHAVIOUR.md        ← Flows & states
│       │   ├── ERRORS.md           ← Error codes
│       │   ├── CONFIG.md           ← Configuration
│       │   ├── STORAGE.md          ← Database schema
│       │   ├── DECISIONS.md        ← Design rationale
│       │   └── EXAMPLES.md         ← Usage examples
│       └── {{subsystem}}/
│           └── spec/               ← Same structure
│
├── integrations/
│   └── {{provider}}/
│       ├── SYSTEM-NOTES.md         ← Provider overview
│       └── {{service}}/
│           └── spec/
│               ├── INDEX.md
│               ├── OVERVIEW.md
│               ├── API-SURFACE.md  ← External API
│               ├── OUR-WRAPPER.md  ← Our functions
│               ├── FIELD-MAPPING.md
│               ├── ERRORS.md
│               ├── CONSTRAINTS.md
│               ├── FAILURE-MODES.md
│               └── EXAMPLES.md
│
└── libraries/
    └── {{library}}/
        └── spec/                   ← Subset of system specs
```

### Quick Reference by Task

| If you need to... | Look at... |
|-------------------|------------|
| Understand a component | `spec/INDEX.md` → `spec/OVERVIEW.md` |
| Implement a function | `spec/FUNCTIONS.md` |
| Define a type | `spec/TYPES.md` + `SCHEMAS.md` |
| Handle an error | `spec/ERRORS.md` + `ERROR-CODES.md` |
| Understand behavior | `spec/BEHAVIOUR.md` |
| Set up database | `spec/STORAGE.md` |
| Configure component | `spec/CONFIG.md` |
| See examples | `spec/EXAMPLES.md` |
| Understand decisions | `spec/DECISIONS.md` |
| Call external API | `spec/API-SURFACE.md` |
| Map external data | `spec/FIELD-MAPPING.md` |
| Handle external failure | `spec/FAILURE-MODES.md` |

---

## Testing Strategy

### Test Locations

| Test Type | Location | Runs When |
|-----------|----------|-----------|
| Unit tests | `{{component}}/__tests__/` | Every commit |
| Integration tests | `/tests/integration/` | PR merge |
| E2E tests | `/tests/e2e/` | Pre-deploy |

### Test Requirements

| Component Type | Required Tests |
|----------------|----------------|
| Library | Unit: 100% of functions |
| System | Unit: 100%, Integration: key flows |
| Subsystem | Unit: 100% |
| Integration | Unit: wrappers, Integration: external calls |

### Running Tests

```bash
# Unit tests
npm test

# Integration tests
npm run test:integration

# E2E tests
npm run test:e2e

# All tests
npm run test:all
```

---

## Development Setup

See `REPO-SPEC.md` for complete setup, but quick start:

```bash
# Clone and install
git clone {{repo_url}}
cd {{project_name}}
npm install

# Set up environment
cp .env.example .env
# Edit .env with your values

# Start development
npm run dev
```

### Required Environment Variables

| Variable | Purpose | Where to Get |
|----------|---------|--------------|
| `DATABASE_URL` | Database connection | {{provider}} dashboard |
| `{{PROVIDER}}_API_KEY` | External API | {{provider}} dashboard |
| `JWT_SECRET` | Auth tokens | Generate locally |

---

## Common Patterns

### Creating a New Entity

```typescript
// Follow this pattern from API-STANDARDS.md
async function createEntity(input: CreateInput): Promise<Result<Entity>> {
  // 1. Validate input
  const validation = validate(input, CreateSchema);
  if (!validation.success) {
    return { success: false, error: validation.error };
  }
  
  // 2. Check business rules
  // 3. Create in storage
  // 4. Return result
}
```

### Handling External API Calls

```typescript
// Follow this pattern from integration specs
async function callExternalApi(): Promise<Result<Data>> {
  try {
    const response = await provider.method();
    return { success: true, data: mapResponse(response) };
  } catch (error) {
    return { success: false, error: mapError(error) };
  }
}
```

---

## Getting Help

### If You're Stuck

1. **Check the spec** — Answer is usually in FUNCTIONS.md or BEHAVIOUR.md
2. **Check related specs** — Dependencies might clarify
3. **Check DECISIONS.md** — Explains why something is designed that way
4. **Check EXAMPLES.md** — Shows working code
5. **Flag it** — Add to INDEX.md "Questions" if truly unclear

### Reporting Spec Issues

If you find a spec gap or error:

1. Note the location: `{{component}}/spec/{{file}}.md`
2. Describe the issue
3. Add to component's INDEX.md "Spec Gaps" section
4. Continue with best judgment
5. Human will review and clarify

---

## Success Criteria

Implementation is complete when:

- [ ] All components implemented per specs
- [ ] All unit tests pass
- [ ] All integration tests pass
- [ ] All E2E tests pass
- [ ] No critical errors in logs
- [ ] Performance meets targets
- [ ] Security review passed
- [ ] Code matches conventions

---

## Notes

{{Any additional notes for the implementer}}

---
Phase: 28 (Full Spec Checklist)
Generated: {{timestamp}}
--- Target Stack

| Layer | Technology | Notes |
|-------|------------|-------|
| Frontend | {{Svelte/Astro/React/etc.}} | |
| Backend | {{Node/Python/etc.}} | |
| Database | {{Supabase/PlanetScale/etc.}} | |
| Hosting | {{Vercel/Fly.io/etc.}} | |

### Key Outcomes

1. {{Primary outcome}}
2. {{Secondary outcome}}
3. {{Tertiary outcome}}

---

## Architecture Summary

### Component Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        FRONTEND                              │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐        │
│  │  Pages  │  │ Components│ │  Stores │  │  Utils  │        │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘        │
└───────┼────────────┼────────────┼────────────┼──────────────┘
        │            │            │            │
        └────────────┴─────┬──────┴────────────┘
                           │ API Calls
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                        BACKEND                               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │ user-system │  │project-system│  │billing-system│        │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘         │
│         │                │                │                  │
│  ┌──────┴──────┐  ┌──────┴──────┐  ┌──────┴──────┐         │
│  │ subsystems  │  │ subsystems  │  │ subsystems  │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└───────────────────────────┬─────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│   Provider   │    │   Provider   │    │   Provider   │
│   (Stripe)   │    │  (Supabase)  │    │   (Resend)   │
│ ┌──────────┐ │    │ ┌──────────┐ │    │ ┌──────────┐ │
│ │ payments │ │    │ │   auth   │ │    │ │  email   │ │
│ │ billing  │ │    │ │ database │ │    │ └──────────┘ │
│ └──────────┘ │    │ │ storage  │ │    └──────────────┘
└──────────────┘    │ └──────────┘ │
                    └──────────────┘
```

### Systems

| System | Purpose | Subsystems |
|--------|---------|------------|
| {{system}} | {{one-line purpose}} | {{list}} |
| {{system}} | {{one-line purpose}} | {{list}} |

### External Integrations

| Provider | Services | Purpose |
|----------|----------|---------|
| {{provider}} | {{service1, service2}} | {{purpose}} |
| {{provider}} | {{service1}} | {{purpose}} |

### Libraries

| Library | Purpose | Used By |
|---------|---------|---------|
| {{library}} | {{purpose}} | {{components}} |

---

## Key Decisions

Architectural decisions that affect implementation:

| Decision | Choice | Rationale | See |
|----------|--------|-----------|-----|
| Database | {{choice}} | {{why}} | DECISIONS.md |
| Auth | {{choice}} | {{why}} | DECISIONS.md |
| State management | {{choice}} | {{why}} | DECISIONS.md |
| Error handling | Result types | Explicit errors | API-STANDARDS.md |
| ID format | {{format}} | {{why}} | CONVENTIONS.md |

---

## Implementation Order

Build in this order to respect dependencies:

### Phase 1: Foundation (Week 1)

```
1. Project setup (REPO-SPEC.md)
   └── Initialize repo, install deps, configure tools

2. Libraries (no dependencies)
   ├── lib-validation
   ├── lib-errors
   ├── lib-ids
   └── lib-datetime

3. Shared types (SCHEMAS.md)
   └── Result<T>, ListResult<T>, base types
```

### Phase 2: Integrations (Week 2)

```
4. Integration providers (shared config)
   ├── stripe/PROVIDER.md
   ├── supabase/PROVIDER.md
   └── {{provider}}/PROVIDER.md

5. Integration services
   ├── stripe/payments/
   ├── supabase/auth/
   ├── supabase/database/
   └── {{provider}}/{{service}}/
```

### Phase 3: Core Systems (Week 3)

```
6. Core systems (order matters)
   ├── user-system (depends on: lib-*, supabase/auth)
   ├── auth-system (depends on: user-system)
   └── {{system}}
```

### Phase 4: Business Logic (Week 4)

```
7. Business systems
   ├── project-system
   ├── billing-system (depends on: stripe/payments)
   └── {{system}}

8. Subsystems (per parent)
   └── Each system's subsystems
```

### Phase 5: Frontend (Week 5)

```
9. Frontend foundation
   ├── Layout components
   ├── Auth flow
   └── Navigation

10. Feature pages
    ├── Dashboard
    ├── {{feature}}
    └── {{feature}}
```

### Phase 6: Integration & Polish (Week 6)

```
11. End-to-end integration
12. Error handling polish
13. Performance optimization
14. Testing completion
```

---

## Critical Constraints

### Must Follow

| Constraint | Rule | Consequence |
|------------|------|-------------|
| File size | Max ~300 LOC per file | Split if exceeded |
| Component size | Max ~1500 LOC per component | Decompose if exceeded |
| Error handling | Always use Result<T> | Never throw for expected errors |
| Types | Use shared types from SCHEMAS.md | Don't duplicate |
| Error codes | Register in ERROR-CODES.md | Must be unique |

### Security Requirements

| Requirement | Implementation |
|-------------|----------------|
| Auth | {{method}} — see SECURITY-STANDARDS.md |
| Input validation | All inputs validated — see lib-validation |
| Secrets | Environment variables only — never commit |
| SQL | Parameterized queries only — no string concat |

---

## Where to Find Things

### Spec File Locations

| What | Where |
|------|-------|
| System specs | `systems/{{name}}/spec/` |
| Subsystem specs | `systems/{{parent}}/{{name}}/spec/` |
| Provider config | `integrations/{{provider}}/PROVIDER.md` |
| Service specs | `integrations/{{provider}}/{{service}}/spec/` |
| Library specs | `libraries/{{name}}/spec/` |
| Shared types | `0. Admin/0c. Full Specs/SCHEMAS.md` |
| Error codes | `0. Admin/0c. Full Specs/ERROR-CODES.md` |
| Conventions | `0. Admin/0c. Full Specs/CONVENTIONS.md` |

### Quick Reference

| Need | Look In |
|------|---------|
| Function signature | FUNCTIONS.md |
| Type definition | TYPES.md |
| Error code meaning | ERRORS.md |
| Config options | CONFIG.md |
| Database schema | STORAGE.md |
| Usage example | EXAMPLES.md |
| Why decision made | DECISIONS.md |

---

## Common Patterns

### Creating a New Entity

```typescript
// 1. Validate input (lib-validation)
const validation = validate(input, CreateUserSchema);
if (!validation.success) {
  return { success: false, error: validation.error };
}

// 2. Generate ID (lib-ids)
const id = generateId('user');

// 3. Create entity
const entity = {
  id,
  ...validation.data,
  createdAt: now(),
  updatedAt: now(),
};

// 4. Save to storage
const saved = await storage.save(entity);
if (!saved.success) {
  return { success: false, error: mapStorageError(saved.error) };
}

// 5. Return result
return { success: true, data: saved.data };
```

### Calling External Service

```typescript
// 1. Get provider client (configured with auth)
const stripe = getProvider('stripe');

// 2. Call service
const result = await stripe.payments.createCharge({
  amount: input.amount,
  currency: input.currency,
});

// 3. Map response to our types
if (!result.success) {
  return { success: false, error: mapStripeError(result.error) };
}

return { 
  success: true, 
  data: mapStripeChargeToOurCharge(result.data) 
};
```

### Handling Errors

```typescript
// Always return Result<T>, never throw
function doSomething(): Result<Data> {
  // Validation error
  if (!valid) {
    return { 
      success: false, 
      error: { code: 'USR_001', message: 'Invalid input' } 
    };
  }

  // Success
  return { success: true, data: result };
}

// Caller handles result
const result = doSomething();
if (!result.success) {
  // Handle error based on code
  return result; // Propagate up
}
// Use result.data
```

---

## Getting Started

### First Steps

1. **Read REPO-SPEC.md** — Set up the project
2. **Read CONVENTIONS.md** — Understand naming and style
3. **Read SCHEMAS.md** — Understand shared types
4. **Start with libraries** — No dependencies, easiest to build

### When Implementing a Component

1. Read `spec/INDEX.md` — Get overview
2. Read `spec/OVERVIEW.md` — Understand purpose and boundaries
3. Read `spec/TYPES.md` — Define types first
4. Read `spec/FUNCTIONS.md` — Implement functions
5. Read `spec/EXAMPLES.md` — Verify behavior matches
6. Read `spec/ERRORS.md` — Implement error handling

### When Stuck

1. Check DECISIONS.md — Why was it designed this way?
2. Check EXAMPLES.md — See expected behavior
3. Check parent component — Context from system/provider level
4. Flag in code — `// TODO: Spec unclear on X`

---

## Success Criteria

Implementation is complete when:

- [ ] All systems implemented and tested
- [ ] All subsystems implemented and tested
- [ ] All integrations working
- [ ] All libraries implemented
- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] Error handling complete
- [ ] Security requirements met
- [ ] Documentation updated if specs changed

---

## Notes

{{Any additional context for implementers}}

---
Phase: 28 (Full Spec Checklist)
Generated: {{timestamp}}
--- Core Problem

{{What problem this solves}}

### Target Users

{{Who will use this}}

### Key Constraints

- {{constraint 1}}
- {{constraint 2}}
- {{constraint 3}}

---

## Architecture Summary

### System Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        {{Project Name}}                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   Frontend   │  │   Backend    │  │  Integrations │          │
│  │              │  │              │  │               │          │
│  │  - Pages     │  │  - Systems   │  │  - Stripe     │          │
│  │  - Components│  │  - APIs      │  │  - Email      │          │
│  │  - State     │  │  - Storage   │  │  - Storage    │          │
│  └──────────────┘  └──────────────┘  └───────────────┘          │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Systems

| System | Purpose | Specs Location |
|--------|---------|----------------|
| {{system}} | {{purpose}} | `systems/{{system}}/spec/` |
| {{system}} | {{purpose}} | `systems/{{system}}/spec/` |
| {{system}} | {{purpose}} | `systems/{{system}}/spec/` |

### Subsystems

| Subsystem | Parent | Purpose | Specs Location |
|-----------|--------|---------|----------------|
| {{subsystem}} | {{parent}} | {{purpose}} | `systems/{{parent}}/{{subsystem}}/spec/` |

### External Integrations

| Provider | Service | Purpose | Specs Location |
|----------|---------|---------|----------------|
| {{provider}} | {{service}} | {{purpose}} | `integrations/{{provider}}/{{service}}/spec/` |

### Libraries

| Library | Purpose | Specs Location |
|---------|---------|----------------|
| {{library}} | {{purpose}} | `libraries/{{library}}/spec/` |

---

## Implementation Order

Build in this order to respect dependencies:

### Phase 1: Foundation (Week 1)

```
1. Libraries (no dependencies)
   ├── lib-validation
   ├── lib-errors
   ├── lib-ids
   └── lib-datetime
```

**Why first:** Everything else depends on these utilities.

### Phase 2: Integrations (Week 2)

```
2. External Integrations
   ├── int-stripe
   ├── int-email
   └── int-storage
```

**Why second:** Systems need these to function.

### Phase 3: Core Systems (Week 3)

```
3. Core Systems
   ├── user-system
   └── auth-system
```

**Why third:** Foundation for all business logic.

### Phase 4: Business Systems (Week 4)

```
4. Business Systems
   ├── project-system
   ├── task-subsystem
   └── billing-subsystem
```

**Why fourth:** Depends on core systems.

### Phase 5: Frontend (Week 5)

```
5. Frontend
   ├── Pages
   ├── Components
   └── State management
```

**Why last:** Consumes all backend systems.

---

## Key Decisions

Decisions that affect implementation:

### Architecture Decisions

| Decision | Choice | Rationale | Location |
|----------|--------|-----------|----------|
| Database | SQLite | Embedded, no external deps | REPO-SPEC.md |
| Auth | JWT | Stateless, scalable | auth-system/DECISIONS.md |
| API Style | Async/Promise | Consistent, future-proof | API-STANDARDS.md |
| Error Handling | Result type | No thrown exceptions | CONVENTIONS.md |

### Technology Choices

| Category | Choice | Version |
|----------|--------|---------|
| Language | TypeScript | 5.x |
| Runtime | Node.js | 20.x |
| Framework | {{framework}} | {{version}} |
| Database | SQLite | 3.x |
| Testing | Vitest | 1.x |

---

## Critical Constraints

### MUST Follow

| Constraint | Description | Spec Reference |
|------------|-------------|----------------|
| File size | No file > 300 LOC | CONVENTIONS.md |
| Component size | No component > 1,500 LOC | All OVERVIEW.md |
| Error codes | Use registered codes only | ERROR-CODES.md |
| Types | Use shared types | SCHEMAS.md |
| Security | Follow security standards | SECURITY-STANDARDS.md |

### MUST NOT Do

| Constraint | Reason |
|------------|--------|
| Throw for expected errors | Use Result type instead |
| Hardcode secrets | Use environment variables |
| Skip validation | All input must be validated |
| Circular imports | Use dependency injection |

---

## Where to Find Things

### Project-Wide Standards

| Document | Contains | Location |
|----------|----------|----------|
| SCHEMAS.md | Shared types | `0. Admin/0c. Full Specs/` |
| ERROR-CODES.md | All error codes | `0. Admin/0c. Full Specs/` |
| CONVENTIONS.md | Coding standards | `0. Admin/0c. Full Specs/` |
| API-STANDARDS.md | API patterns | `0. Admin/0c. Full Specs/` |
| SECURITY-STANDARDS.md | Security rules | `0. Admin/0c. Full Specs/` |
| GLOSSARY.md | Term definitions | `0. Admin/0c. Full Specs/` |

### Per-Component Specs

| File | Contains |
|------|----------|
| INDEX.md | Quick reference, status |
| OVERVIEW.md | Purpose, boundaries, guarantees |
| FUNCTIONS.md | All function signatures |
| TYPES.md | All data types |
| BEHAVIOUR.md | Process flows, state machines |
| ERRORS.md | Error codes for this component |
| CONFIG.md | Configuration options |
| STORAGE.md | Database schema (if applicable) |
| DECISIONS.md | Design rationale |
| EXAMPLES.md | Request/response examples |

### Integration Specs (External Services)

| File | Contains |
|------|----------|
| API-SURFACE.md | What we use from provider |
| OUR-WRAPPER.md | Our abstraction layer |
| FIELD-MAPPING.md | Their fields ↔ our fields |
| CONSTRAINTS.md | Rate limits, quotas |
| FAILURE-MODES.md | What can go wrong |

---

## Getting Started

### Step 1: Set Up Environment

```bash
# Clone repository
git clone {{repo_url}}
cd {{project_name}}

# Install dependencies
npm install

# Copy environment template
cp .env.example .env

# Configure environment variables
# See REPO-SPEC.md for required variables
```

### Step 2: Verify Setup

```bash
# Run tests to verify setup
npm test

# Start development server
npm run dev
```

### Step 3: Begin Implementation

1. Start with `libraries/lib-validation/`
2. Read `spec/INDEX.md` first
3. Implement functions from `spec/FUNCTIONS.md`
4. Write tests alongside code
5. Move to next component

---

## Implementation Workflow

### For Each Component

```
1. Read INDEX.md (overview, dependencies)
       ↓
2. Read OVERVIEW.md (purpose, boundaries)
       ↓
3. Read TYPES.md (define types first)
       ↓
4. Read FUNCTIONS.md (implement functions)
       ↓
5. Read BEHAVIOUR.md (process flows)
       ↓
6. Read ERRORS.md (error handling)
       ↓
7. Read EXAMPLES.md (verify with examples)
       ↓
8. Run tests
       ↓
9. Move to next component
```

### Testing Requirements

| Type | When | Coverage |
|------|------|----------|
| Unit tests | With each function | All functions |
| Integration tests | After component done | Cross-component |
| E2E tests | After phase done | User flows |

---

## Common Patterns

### Creating a New Entity

```typescript
// See API-STANDARDS.md for full pattern
async function createEntity(input: CreateInput): Promise<Result<Entity>> {
  // 1. Validate input
  const validation = validate(input);
  if (!validation.success) {
    return { success: false, error: validation.error };
  }
  
  // 2. Generate ID
  const id = generateId('entity');
  
  // 3. Build entity
  const entity = {
    id,
    ...input,
    createdAt: now(),
    updatedAt: now(),
  };
  
  // 4. Save
  await storage.save(entity);
  
  // 5. Return
  return { success: true, data: entity };
}
```

### Handling Errors

```typescript
// See CONVENTIONS.md for full pattern
// NEVER throw for expected errors
// ALWAYS return Result type

// Good
return { success: false, error: { code: 'USR_101', message: '...' } };

// Bad
throw new Error('User not found');
```

### Calling External Services

```typescript
// See integration specs for service-specific patterns
// ALWAYS use our wrapper, never call provider directly

// Good
const result = await stripe.createPayment(params);

// Bad
const result = await fetch('https://api.stripe.com/...');
```

---

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Circular dependency | Wrong import order | Check dependency graph in IMPLEMENTABILITY-REVIEW.md |
| Type mismatch | Using wrong type | Check SCHEMAS.md for shared types |
| Unknown error code | Code not registered | Check ERROR-CODES.md, add if missing |
| Test failure | Spec mismatch | Check EXAMPLES.md for expected behavior |

### Getting Help

1. **Check specs** — Answer is usually in the spec files
2. **Check GLOSSARY.md** — Term definitions
3. **Check DECISIONS.md** — Design rationale
4. **Flag for human** — If truly blocked

---

## Success Criteria

Implementation is complete when:

- [ ] All components implemented per specs
- [ ] All unit tests pass
- [ ] All integration tests pass
- [ ] All error codes work as documented
- [ ] All examples from EXAMPLES.md work
- [ ] Performance meets targets
- [ ] Security standards met
- [ ] Human review passed

---

## Quick Reference

### Error Code Ranges

| Prefix | Component | Range |
|--------|-----------|-------|
| USR | user-system | 001-999 |
| AUTH | auth-system | 001-999 |
| PROJ | project-system | 001-999 |
| _See ERROR-CODES.md for complete list_ |

### Shared Types

| Type | Purpose |
|------|---------|
| `Result<T>` | Function return wrapper |
| `ListResult<T>` | Paginated list wrapper |
| `EntityId` | ID format |
| `Timestamp` | ISO 8601 date |
| _See SCHEMAS.md for complete list_ |

---

## Contact

**Questions about specs:** Flag in implementation notes
**Blocking issues:** Escalate to human reviewer
**Spec errors found:** Document and continue, fix later

---
Phase: 28 (Full Spec Checklist)
Generated: {{timestamp}}
---
