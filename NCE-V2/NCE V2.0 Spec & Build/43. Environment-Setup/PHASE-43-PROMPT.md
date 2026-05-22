# Phase 43: Environment Setup

---
Phase: 43
Section: 0f. Implementation
Name: Environment Setup
Purpose: Create runnable project skeleton with shared types
---

## Role

You are setting up the development environment. Your job is to create a working project skeleton that matches the specifications exactly.

---

## Inputs

Read these documents before starting:

| Document | Location | What to Extract |
|----------|----------|-----------------|
| TECH-STACK.md | 0d outputs | Frameworks, languages, package managers |
| REPO-STRUCTURE.md | 0d outputs | Folder layout |
| ENVIRONMENT.md | 0d outputs | Environment variables |
| TYPES.md | 0c outputs | Type definitions to generate |

---

## Process

### Step 1: Create Folder Structure

Create all folders per REPO-STRUCTURE.md:

```bash
# Example (adapt to your REPO-STRUCTURE.md)
mkdir -p src/{backend,frontend,shared}
mkdir -p src/backend/{routes,middleware,models,services}
mkdir -p src/frontend/{components,pages,stores,lib}
mkdir -p src/shared/types
mkdir -p migrations seeds docs
```

**Verify:** Every folder in REPO-STRUCTURE.md exists.

### Step 2: Initialize Package Managers

Based on TECH-STACK.md:

```bash
# Node.js
npm init -y
# or
pnpm init

# Python
python -m venv venv
pip install -r requirements.txt
```

**Verify:** Package manager initialized, lockfile created.

### Step 3: Install Dependencies

Install all dependencies listed in TECH-STACK.md:

```bash
# Example
npm install express cors helmet
npm install -D typescript @types/node @types/express
```

**Verify:** All dependencies install without errors.

### Step 4: Configure Environment Variables

Create `.env.example` from ENVIRONMENT.md:

```env
# Database
DATABASE_URL=

# Auth
JWT_SECRET=
JWT_EXPIRES_IN=

# API
API_PORT=
API_BASE_URL=
```

Create `.env` for local development with actual values.

**Verify:** All variables from ENVIRONMENT.md are present.

### Step 5: Environment Gap Check

Compare ENVIRONMENT.md against library requirements:

| Library | Required Variables | In ENVIRONMENT.md? |
|---------|-------------------|---------------------|
| {{library}} | {{variables}} | Yes / **NO - ADD** |

If gaps found:
1. Document in IMPLEMENTATION-LOG.md
2. Add to `.env.example`
3. File DEVIATION-REPORT if significant

### Step 6: Create shared-types/ Folder

This is the **single source of truth** for all type definitions:

```
src/shared/types/
├── index.ts        # Re-exports all types
├── user.ts         # User-related types
├── project.ts      # Project-related types
├── api.ts          # API request/response types
└── errors.ts       # Error types
```

### Type Categories

| Category | Location | Shared With | Examples |
|----------|----------|-------------|----------|
| **Shared/DTO** | shared-types/ | Frontend + Backend | User (without password), Project, ApiResponse |
| **Backend Internal** | backend/types/ or models/ | Backend only | UserWithPassword, DatabaseRow, InternalConfig |
| **Frontend Internal** | frontend/types/ | Frontend only | UIState, FormState, ComponentProps |

**Rule:** Only DTO types (what crosses the API boundary) belong in shared-types/.

```
shared-types/user.ts:
  ✅ User { id, name, email, createdAt }
  ❌ UserWithPassword { ..., passwordHash }  // Backend internal only
```

### Step 7: Generate Types from TYPES.md

For each type in TYPES.md, create the TypeScript definition:

```typescript
// src/shared/types/user.ts

export interface User {
  id: string;
  email: string;
  name: string;
  createdAt: string;
  updatedAt: string;
}

export interface UserCreate {
  email: string;
  name: string;
  password: string;
}

export interface UserUpdate {
  name?: string;
  email?: string;
}
```

Create index.ts to re-export:

```typescript
// src/shared/types/index.ts

export * from './user';
export * from './project';
export * from './api';
export * from './errors';
```

**Verify:** Every type in TYPES.md has a TypeScript definition.

### Step 8: Configure IDE Settings

Create configuration files:

```
.editorconfig
.prettierrc
.eslintrc.js (or eslint.config.js)
tsconfig.json
```

Example `.editorconfig`:
```ini
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
```

### Step 9: Initialize Git

```bash
git init
```

Create `.gitignore`:
```gitignore
# Dependencies
node_modules/
venv/
__pycache__/

# Environment
.env
.env.local

# Build
dist/
build/
.svelte-kit/

# IDE
.idea/
.vscode/
*.swp

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/
```

Make initial commit:
```bash
git add .
git commit -m "chore: initial project setup

- Create folder structure per REPO-STRUCTURE.md
- Install dependencies per TECH-STACK.md
- Configure environment per ENVIRONMENT.md
- Generate shared types from TYPES.md

Refs: PHASE-43"
```

### Step 10: Verify Dev Server Runs

Start the development server:

```bash
# Backend
npm run dev:backend
# or
python manage.py runserver

# Frontend
npm run dev:frontend
# or
npm run dev
```

**Verify:** 
- Server starts without errors
- Can access in browser (if applicable)
- No TypeScript errors
- No missing dependencies

---

## Output

By the end of this phase:

| Output | Location | Status |
|--------|----------|--------|
| Folder structure | Per REPO-STRUCTURE.md | ☐ Created |
| Dependencies | package.json / requirements.txt | ☐ Installed |
| Environment | .env.example, .env | ☐ Configured |
| shared-types/ | src/shared/types/ | ☐ Generated |
| IDE config | .editorconfig, etc. | ☐ Created |
| Git repo | .git/ | ☐ Initialized |
| Dev server | Running | ☐ Verified |

---

## Checklist

Before moving to Phase 44:

- [ ] Folder structure matches REPO-STRUCTURE.md exactly
- [ ] All dependencies from TECH-STACK.md installed
- [ ] All environment variables from ENVIRONMENT.md configured
- [ ] Environment Gap Check completed (no missing vars)
- [ ] shared-types/ generated from TYPES.md
- [ ] Types are importable from both backend and frontend paths
- [ ] IDE settings configured
- [ ] Git initialized with .gitignore
- [ ] Dev server starts without errors
- [ ] Initial commit made

---

## Implementation Log Entry

Add to IMPLEMENTATION-LOG.md:

```markdown
## Phase 43: Environment Setup

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Created project skeleton with shared types.

### Tasks Completed
- [x] Created folder structure per REPO-STRUCTURE.md
- [x] Installed dependencies per TECH-STACK.md
- [x] Configured environment per ENVIRONMENT.md
- [x] Ran Environment Gap Check
- [x] Generated shared-types/ from TYPES.md
- [x] Configured IDE settings
- [x] Initialized git
- [x] Verified dev server runs

### Environment Gap Check Results
| Variable | In Spec | Required By | Action |
|----------|---------|-------------|--------|
| (none found) | | | |

### Files Created
- src/shared/types/*.ts
- .env.example
- .editorconfig
- .gitignore
- tsconfig.json

### Deviations
- None

### Notes
{{Any observations or decisions}}
```

---

## Rules

1. **Follow REPO-STRUCTURE.md exactly** — Don't add folders not in spec
2. **Follow TECH-STACK.md exactly** — Don't add dependencies not in spec
3. **Generate ALL types** — Every type in TYPES.md must exist
4. **Single source of truth** — Types only in shared-types/, nowhere else
5. **Document gaps** — Environment Gap Check must be thorough

---

## When Issues Arise

| Issue | Action |
|-------|--------|
| Dependency conflict | Document, try to resolve, file DEVIATION-REPORT if can't |
| Missing type in TYPES.md | Check if it's in another spec file, escalate if truly missing |
| Environment variable unclear | Make reasonable assumption, document in log |
| Dev server won't start | Debug, fix, document what was wrong |

---

## Next Phase

After completing Phase 43, proceed to:

**Phase 44: Database Implementation**

---
