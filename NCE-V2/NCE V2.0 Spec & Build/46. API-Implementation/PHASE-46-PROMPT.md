# Phase 46: API Implementation

---
Phase: 46
Section: 0f. Implementation
Name: API Implementation
Purpose: Implement all API endpoints per specification
---

## Role

You are implementing the API endpoints. Your job is to create every endpoint specified in API-SURFACE.md with correct validation, authorization, and responses.

---

## Inputs

Read these documents before starting:

| Document | Location | What to Extract |
|----------|----------|-----------------|
| API-SURFACE.md | 0c/0d outputs | All endpoints, methods, auth |
| ERROR-CODES.md | 0c outputs | Error handling |
| shared-types/ | From Phase 43 | Request/response types |
| Backend core | From Phase 45 | Middleware, auth, errors |
| Database | From Phase 44 | Models, queries |

---

## Process

### Step 1: Extract Endpoint List

From API-SURFACE.md, create a complete endpoint list:

| # | Method | Path | Auth | Description |
|---|--------|------|------|-------------|
| 1 | POST | /api/auth/login | Public | User login |
| 2 | POST | /api/auth/register | Public | User registration |
| 3 | POST | /api/auth/logout | Auth | User logout |
| 4 | GET | /api/users/me | Auth | Get current user |
| 5 | PUT | /api/users/me | Auth | Update current user |
| 6 | GET | /api/projects | Auth | List user's projects |
| 7 | POST | /api/projects | Auth | Create project |
| 8 | GET | /api/projects/:id | Auth | Get project |
| 9 | PUT | /api/projects/:id | Auth+Owner | Update project |
| 10 | DELETE | /api/projects/:id | Auth+Owner | Delete project |
| ... | ... | ... | ... | ... |

### Step 2: Create Route Files

Organize routes by resource:

```
src/backend/routes/
├── index.ts          # Route aggregator
├── auth.ts           # /api/auth/*
├── users.ts          # /api/users/*
└── projects.ts       # /api/projects/*
```

**Route aggregator:**
```typescript
// src/backend/routes/index.ts

import { Router } from 'express';
import { authRouter } from './auth';
import { usersRouter } from './users';
import { projectsRouter } from './projects';

const router = Router();

router.use('/auth', authRouter);
router.use('/users', usersRouter);
router.use('/projects', projectsRouter);

export { router as apiRouter };
```

**Register in server:**
```typescript
// src/backend/server.ts

import { apiRouter } from './routes';

app.use('/api', apiRouter);
```

### Step 3: Implement Each Endpoint

For each endpoint in API-SURFACE.md:

#### Pattern: List Endpoint (GET collection)

```typescript
// src/backend/routes/projects.ts

import { Router } from 'express';
import { z } from 'zod';
import { authenticate } from '../middleware/authenticate';
import { validateQuery } from '../middleware/validate';
import { db } from '@/lib/db';
import { sendPaginated } from '../utils/response';
import type { Project } from '@/shared/types';

const router = Router();

// Validation schema
const listQuerySchema = z.object({
  page: z.coerce.number().int().positive().default(1),
  limit: z.coerce.number().int().positive().max(100).default(20),
  status: z.enum(['active', 'archived', 'all']).optional(),
});

// GET /api/projects
router.get('/',
  authenticate,
  validateQuery(listQuerySchema),
  async (req, res, next) => {
    try {
      const { page, limit, status } = req.query as z.infer<typeof listQuerySchema>;
      
      const where = {
        userId: req.user!.id,
        ...(status && status !== 'all' ? { status } : {}),
      };
      
      const [projects, total] = await Promise.all([
        db.project.findMany({
          where,
          skip: (page - 1) * limit,
          take: limit,
          orderBy: { createdAt: 'desc' },
        }),
        db.project.count({ where }),
      ]);
      
      sendPaginated(res, projects, { page, limit, total });
    } catch (error) {
      next(error);
    }
  }
);
```

#### Pattern: Get Single (GET by ID)

```typescript
// GET /api/projects/:id
router.get('/:id',
  authenticate,
  async (req, res, next) => {
    try {
      const project = await db.project.findUnique({
        where: { id: req.params.id },
      });
      
      if (!project) {
        throw new AppError('NOT_FOUND', 'Project not found', 404);
      }
      
      // Check ownership
      if (project.userId !== req.user!.id) {
        throw new AppError('FORBIDDEN', 'Access denied', 403);
      }
      
      res.json(project);
    } catch (error) {
      next(error);
    }
  }
);
```

#### Pattern: Create (POST)

```typescript
// Validation schema
const createProjectSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().max(1000).optional(),
});

// POST /api/projects
router.post('/',
  authenticate,
  validate(createProjectSchema),
  async (req, res, next) => {
    try {
      const project = await db.project.create({
        data: {
          ...req.body,
          userId: req.user!.id,
        },
      });
      
      res.status(201).json(project);
    } catch (error) {
      next(error);
    }
  }
);
```

#### Pattern: Update (PUT/PATCH)

```typescript
// Validation schema
const updateProjectSchema = z.object({
  name: z.string().min(1).max(100).optional(),
  description: z.string().max(1000).optional(),
  status: z.enum(['active', 'archived']).optional(),
});

// PUT /api/projects/:id
router.put('/:id',
  authenticate,
  validate(updateProjectSchema),
  async (req, res, next) => {
    try {
      // Check exists and ownership
      const existing = await db.project.findUnique({
        where: { id: req.params.id },
      });
      
      if (!existing) {
        throw new AppError('NOT_FOUND', 'Project not found', 404);
      }
      
      if (existing.userId !== req.user!.id) {
        throw new AppError('FORBIDDEN', 'Access denied', 403);
      }
      
      const project = await db.project.update({
        where: { id: req.params.id },
        data: req.body,
      });
      
      res.json(project);
    } catch (error) {
      next(error);
    }
  }
);
```

#### Pattern: Delete (DELETE)

```typescript
// DELETE /api/projects/:id
router.delete('/:id',
  authenticate,
  async (req, res, next) => {
    try {
      // Check exists and ownership
      const existing = await db.project.findUnique({
        where: { id: req.params.id },
      });
      
      if (!existing) {
        throw new AppError('NOT_FOUND', 'Project not found', 404);
      }
      
      if (existing.userId !== req.user!.id) {
        throw new AppError('FORBIDDEN', 'Access denied', 403);
      }
      
      await db.project.delete({
        where: { id: req.params.id },
      });
      
      res.status(204).send();
    } catch (error) {
      next(error);
    }
  }
);

export { router as projectsRouter };
```

### Step 4: Implement All Endpoints

Work through API-SURFACE.md systematically:

| Resource | Endpoints | Status |
|----------|-----------|--------|
| Auth | 3 | ☐ |
| Users | 2 | ☐ |
| Projects | 5 | ☐ |
| Tasks | 5 | ☐ |
| ... | ... | ☐ |

### Step 5: Add Input Validation

For every endpoint with a request body:

```typescript
// Create schemas that match TYPES.md

const schemas = {
  createProject: z.object({
    name: z.string().min(1).max(100),
    description: z.string().max(1000).optional(),
  }),
  
  updateProject: z.object({
    name: z.string().min(1).max(100).optional(),
    description: z.string().max(1000).optional(),
    status: z.enum(['active', 'archived']).optional(),
  }),
};
```

### Step 6: Add Authorization Checks

For endpoints requiring ownership or roles:

```typescript
// Check ownership pattern
async function checkOwnership(
  req: Request,
  resourceId: string,
  model: any,
  ownerField = 'userId'
) {
  const resource = await model.findUnique({
    where: { id: resourceId },
  });
  
  if (!resource) {
    throw new AppError('NOT_FOUND', 'Resource not found', 404);
  }
  
  if (resource[ownerField] !== req.user!.id) {
    throw new AppError('FORBIDDEN', 'Access denied', 403);
  }
  
  return resource;
}
```

### Step 7: Handle All Error Cases

For each endpoint, ensure proper errors:

| Scenario | Error Code | HTTP Status |
|----------|------------|-------------|
| Not authenticated | UNAUTHORIZED | 401 |
| Not authorized | FORBIDDEN | 403 |
| Resource not found | NOT_FOUND | 404 |
| Validation failed | VALIDATION_ERROR | 400 |
| Duplicate resource | ALREADY_EXISTS | 409 |
| Server error | INTERNAL_ERROR | 500 |

### Step 8: Verify Type Consistency

Ensure all responses match shared-types/:

```typescript
// Response types should match
import type { Project, ProjectCreate, PaginatedResponse } from '@/shared/types';

// GET /projects returns PaginatedResponse<Project>
// POST /projects accepts ProjectCreate, returns Project
// PUT /projects/:id accepts Partial<ProjectCreate>, returns Project
// DELETE /projects/:id returns void (204)
```

### Step 9: Schema Sync Audit

Before completing Phase 46, verify no schema drift occurred:

| Check | Status |
|-------|--------|
| All DB fields used in endpoints exist in DATABASE-SCHEMA.md | ☐ |
| All types used match shared-types/ | ☐ |
| No "implementation-only" fields were added to DB | ☐ |

**If schema changes were needed during implementation:**

1. **STOP** — This indicates a spec gap
2. File DEVIATION-REPORT documenting the change
3. Update DATABASE-SCHEMA.md (or escalate to 0c/0d owner)
4. Regenerate shared-types/ if affected
5. Document in IMPLEMENTATION-LOG.md

**Never:** Add database fields without updating specs.

---

## Output

By the end of this phase:

| Output | Location | Status |
|--------|----------|--------|
| Route files | src/backend/routes/ | ☐ Created |
| All endpoints | Per API-SURFACE.md | ☐ Implemented |
| Validation schemas | Per route file | ☐ Created |
| Authorization checks | Per endpoint | ☐ Implemented |

---

## Checklist

Before moving to Phase 47:

- [ ] Every endpoint in API-SURFACE.md implemented
- [ ] Request validation on all endpoints with body
- [ ] Authorization enforced per spec
- [ ] Response shapes match shared-types/
- [ ] Error responses match ERROR-CODES.md
- [ ] All CRUD operations work
- [ ] Pagination works (where applicable)
- [ ] Filtering works (where applicable)
- [ ] Sorting works (where applicable)

---

## Endpoint Tracking

Track each endpoint:

| # | Endpoint | Validation | Auth | Logic | Tested | Status |
|---|----------|------------|------|-------|--------|--------|
| 1 | POST /auth/login | ☐ | N/A | ☐ | ☐ | |
| 2 | POST /auth/register | ☐ | N/A | ☐ | ☐ | |
| 3 | GET /projects | ☐ | ☐ | ☐ | ☐ | |
| 4 | POST /projects | ☐ | ☐ | ☐ | ☐ | |
| 5 | GET /projects/:id | ☐ | ☐ | ☐ | ☐ | |
| 6 | PUT /projects/:id | ☐ | ☐ | ☐ | ☐ | |
| 7 | DELETE /projects/:id | ☐ | ☐ | ☐ | ☐ | |

---

## Implementation Log Entry

Add to IMPLEMENTATION-LOG.md:

```markdown
## Phase 46: API Implementation

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Implemented all API endpoints per API-SURFACE.md.

### Endpoints Implemented

| Resource | Count | Status |
|----------|-------|--------|
| Auth | 3 | ✅ |
| Users | 2 | ✅ |
| Projects | 5 | ✅ |
| **Total** | 10 | ✅ |

### Files Created
- src/backend/routes/index.ts
- src/backend/routes/auth.ts
- src/backend/routes/users.ts
- src/backend/routes/projects.ts

### Deviations
- None

### Notes
{{Any observations or decisions}}
```

---

## Rules

1. **Implement ALL endpoints** — Nothing missing from API-SURFACE.md
2. **Validate ALL inputs** — Never trust client data
3. **Check ALL authorization** — Every protected endpoint
4. **Return correct types** — Match shared-types/ exactly
5. **Handle ALL errors** — Every error case covered

---

## Next Phase

After completing Phase 46, proceed to:

**Phase 47: Backend Verification** (MANDATORY - DO NOT SKIP)

---
