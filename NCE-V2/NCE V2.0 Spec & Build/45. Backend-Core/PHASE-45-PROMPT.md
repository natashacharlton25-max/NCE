# Phase 45: Backend Core

---
Phase: 45
Section: 0f. Implementation
Name: Backend Core
Purpose: Build server infrastructure before implementing endpoints
---

## Role

You are building the backend foundation. Your job is to set up the web framework, middleware, authentication, and error handling before any business logic endpoints.

---

## Inputs

Read these documents before starting:

| Document | Location | What to Extract |
|----------|----------|-----------------|
| TECH-STACK.md | 0d outputs | Web framework, auth method |
| ERROR-CODES.md | 0c outputs | Error format, codes |
| API-SURFACE.md | 0c/0d outputs | Auth requirements, base URL |
| shared-types/ | From Phase 43 | Type definitions |
| Database | From Phase 44 | Database connection |

---

## Process

### Step 1: Set Up Web Framework

Based on TECH-STACK.md, initialize the server:

**Express Example:**
```typescript
// src/backend/server.ts

import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import { errorHandler } from './middleware/errorHandler';
import { requestLogger } from './middleware/requestLogger';

const app = express();

// Security middleware
app.use(helmet());
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true,
}));

// Body parsing
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Request logging
app.use(requestLogger);

// Routes (added in Phase 46)
// app.use('/api/auth', authRouter);
// app.use('/api/projects', projectsRouter);

// Error handling (must be last)
app.use(errorHandler);

export { app };
```

**Start script:**
```typescript
// src/backend/index.ts

import { app } from './server';

const PORT = process.env.API_PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### Step 2: Configure Middleware

Create middleware in order:

| Order | Middleware | Purpose |
|-------|------------|---------|
| 1 | helmet | Security headers |
| 2 | cors | Cross-origin requests |
| 3 | body-parser | Parse JSON/form data |
| 4 | requestLogger | Log requests |
| 5 | authenticate | Verify JWT (on protected routes) |
| 6 | errorHandler | Catch and format errors |

**Request Logger:**
```typescript
// src/backend/middleware/requestLogger.ts

import type { Request, Response, NextFunction } from 'express';

export function requestLogger(req: Request, res: Response, next: NextFunction) {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(`${req.method} ${req.path} ${res.statusCode} ${duration}ms`);
  });
  
  next();
}
```

### Step 3: Implement Authentication

Based on API-SURFACE.md auth requirements:

**JWT Authentication:**
```typescript
// src/backend/middleware/authenticate.ts

import type { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';
import { AppError } from './errorHandler';
import type { User } from '@/shared/types';

// Extend Express Request
declare global {
  namespace Express {
    interface Request {
      user?: User;
    }
  }
}

export function authenticate(req: Request, res: Response, next: NextFunction) {
  const authHeader = req.headers.authorization;
  
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    throw new AppError('UNAUTHORIZED', 'Missing or invalid token', 401);
  }
  
  const token = authHeader.split(' ')[1];
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as { userId: string };
    // Attach user to request (fetch from DB or decode from token)
    req.user = { id: decoded.userId } as User;
    next();
  } catch (error) {
    throw new AppError('TOKEN_EXPIRED', 'Token is invalid or expired', 401);
  }
}

// Optional authentication (doesn't fail if no token)
export function optionalAuth(req: Request, res: Response, next: NextFunction) {
  const authHeader = req.headers.authorization;
  
  if (authHeader && authHeader.startsWith('Bearer ')) {
    return authenticate(req, res, next);
  }
  
  next();
}
```

**Auth Service:**
```typescript
// src/backend/services/auth.ts

import jwt from 'jsonwebtoken';
import bcrypt from 'bcrypt';
import { db } from '@/lib/db';
import { AppError } from '../middleware/errorHandler';
import type { User } from '@/shared/types';

const JWT_SECRET = process.env.JWT_SECRET!;
const JWT_EXPIRES_IN = process.env.JWT_EXPIRES_IN || '7d';

export const authService = {
  async login(email: string, password: string) {
    const user = await db.user.findUnique({ where: { email } });
    
    if (!user) {
      throw new AppError('INVALID_CREDENTIALS', 'Invalid email or password', 401);
    }
    
    const isValid = await bcrypt.compare(password, user.password);
    
    if (!isValid) {
      throw new AppError('INVALID_CREDENTIALS', 'Invalid email or password', 401);
    }
    
    const token = jwt.sign({ userId: user.id }, JWT_SECRET, {
      expiresIn: JWT_EXPIRES_IN,
    });
    
    return {
      token,
      user: this.sanitizeUser(user),
    };
  },
  
  async register(email: string, password: string, name: string) {
    const existing = await db.user.findUnique({ where: { email } });
    
    if (existing) {
      throw new AppError('EMAIL_EXISTS', 'Email already registered', 400);
    }
    
    const hashedPassword = await bcrypt.hash(password, 10);
    
    const user = await db.user.create({
      data: { email, password: hashedPassword, name },
    });
    
    const token = jwt.sign({ userId: user.id }, JWT_SECRET, {
      expiresIn: JWT_EXPIRES_IN,
    });
    
    return {
      token,
      user: this.sanitizeUser(user),
    };
  },
  
  sanitizeUser(user: User) {
    const { password, ...sanitized } = user;
    return sanitized;
  },
};
```

### Step 4: Implement Authorization

If API-SURFACE.md specifies roles/permissions:

```typescript
// src/backend/middleware/authorize.ts

import type { Request, Response, NextFunction } from 'express';
import { AppError } from './errorHandler';

type Role = 'admin' | 'user' | 'guest';

export function authorize(...allowedRoles: Role[]) {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!req.user) {
      throw new AppError('UNAUTHORIZED', 'Authentication required', 401);
    }
    
    if (!allowedRoles.includes(req.user.role as Role)) {
      throw new AppError('FORBIDDEN', 'Insufficient permissions', 403);
    }
    
    next();
  };
}

// Resource ownership check
export function authorizeOwner(resourceField: string = 'userId') {
  return (req: Request, res: Response, next: NextFunction) => {
    const resourceOwnerId = req.body[resourceField] || req.params[resourceField];
    
    if (req.user?.id !== resourceOwnerId && req.user?.role !== 'admin') {
      throw new AppError('FORBIDDEN', 'Access denied', 403);
    }
    
    next();
  };
}
```

### Step 5: Set Up Error Handling

Based on ERROR-CODES.md:

```typescript
// src/backend/middleware/errorHandler.ts

import type { Request, Response, NextFunction } from 'express';
import type { ApiError } from '@/shared/types';

// Custom error class
export class AppError extends Error {
  constructor(
    public code: string,
    public message: string,
    public statusCode: number = 400,
    public details?: unknown
  ) {
    super(message);
    this.name = 'AppError';
  }
}

// Error codes from ERROR-CODES.md
export const ERROR_CODES = {
  // Auth errors
  UNAUTHORIZED: 'UNAUTHORIZED',
  FORBIDDEN: 'FORBIDDEN',
  TOKEN_EXPIRED: 'TOKEN_EXPIRED',
  INVALID_CREDENTIALS: 'INVALID_CREDENTIALS',
  
  // Validation errors
  VALIDATION_ERROR: 'VALIDATION_ERROR',
  INVALID_INPUT: 'INVALID_INPUT',
  
  // Resource errors
  NOT_FOUND: 'NOT_FOUND',
  ALREADY_EXISTS: 'ALREADY_EXISTS',
  
  // Server errors
  INTERNAL_ERROR: 'INTERNAL_ERROR',
  DATABASE_ERROR: 'DATABASE_ERROR',
} as const;

// Error handler middleware
export function errorHandler(
  error: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  // Log error (skip in tests)
  if (process.env.NODE_ENV !== 'test') {
    console.error('Error:', {
      name: error.name,
      message: error.message,
      stack: error.stack,
    });
  }
  
  // Handle known AppError
  if (error instanceof AppError) {
    const response: ApiError = {
      error_code: error.code,
      message: error.message,
      details: error.details,
    };
    return res.status(error.statusCode).json(response);
  }
  
  // Handle Zod validation errors
  if (error.name === 'ZodError') {
    const response: ApiError = {
      error_code: ERROR_CODES.VALIDATION_ERROR,
      message: 'Validation failed',
      details: (error as any).errors,
    };
    return res.status(400).json(response);
  }
  
  // Handle Prisma errors
  if (error.name === 'PrismaClientKnownRequestError') {
    const prismaError = error as any;
    if (prismaError.code === 'P2002') {
      const response: ApiError = {
        error_code: ERROR_CODES.ALREADY_EXISTS,
        message: 'Resource already exists',
      };
      return res.status(409).json(response);
    }
    if (prismaError.code === 'P2025') {
      const response: ApiError = {
        error_code: ERROR_CODES.NOT_FOUND,
        message: 'Resource not found',
      };
      return res.status(404).json(response);
    }
  }
  
  // Handle unknown errors
  const response: ApiError = {
    error_code: ERROR_CODES.INTERNAL_ERROR,
    message: process.env.NODE_ENV === 'production'
      ? 'An unexpected error occurred'
      : error.message,
  };
  return res.status(500).json(response);
}
```

### Step 6: Create Response Formatters

Ensure consistent response shapes:

```typescript
// src/backend/utils/response.ts

import type { Response } from 'express';

export function sendSuccess<T>(res: Response, data: T, statusCode = 200) {
  return res.status(statusCode).json(data);
}

export function sendCreated<T>(res: Response, data: T) {
  return sendSuccess(res, data, 201);
}

export function sendNoContent(res: Response) {
  return res.status(204).send();
}

export function sendPaginated<T>(
  res: Response,
  data: T[],
  pagination: { page: number; limit: number; total: number }
) {
  return res.json({
    data,
    pagination: {
      page: pagination.page,
      limit: pagination.limit,
      total: pagination.total,
      totalPages: Math.ceil(pagination.total / pagination.limit),
    },
  });
}
```

### Step 7: Set Up Request Validation

```typescript
// src/backend/middleware/validate.ts

import type { Request, Response, NextFunction } from 'express';
import { z } from 'zod';

export function validate<T extends z.ZodSchema>(schema: T) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      schema.parse(req.body);
      next();
    } catch (error) {
      next(error); // Will be caught by errorHandler
    }
  };
}

export function validateQuery<T extends z.ZodSchema>(schema: T) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      req.query = schema.parse(req.query);
      next();
    } catch (error) {
      next(error);
    }
  };
}

export function validateParams<T extends z.ZodSchema>(schema: T) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      req.params = schema.parse(req.params);
      next();
    } catch (error) {
      next(error);
    }
  };
}
```

### Step 8: Verify Backend Core

Test that infrastructure works:

```bash
# Start server
npm run dev:backend

# Test health endpoint
curl http://localhost:3000/health

# Test error handling
curl http://localhost:3000/nonexistent  # Should return 404

# Test auth (if implemented)
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"wrong"}'
# Should return 401 with error_code
```

---

## Output

By the end of this phase:

| Output | Location | Status |
|--------|----------|--------|
| Server setup | src/backend/server.ts | ☐ Created |
| Middleware | src/backend/middleware/ | ☐ Created |
| Auth service | src/backend/services/auth.ts | ☐ Created |
| Error handler | src/backend/middleware/errorHandler.ts | ☐ Created |
| Validation | src/backend/middleware/validate.ts | ☐ Created |
| Response utils | src/backend/utils/response.ts | ☐ Created |

---

## Checklist

Before moving to Phase 46:

- [ ] Server starts and accepts requests
- [ ] CORS configured per spec
- [ ] Body parsing works (JSON, form data)
- [ ] Request logging works
- [ ] Auth middleware implemented
- [ ] Auth endpoints work (login, register, logout)
- [ ] Error responses match ERROR-CODES.md format
- [ ] Validation middleware works
- [ ] Shared types used for all responses
- [ ] Health endpoint returns 200

---

## Implementation Log Entry

Add to IMPLEMENTATION-LOG.md:

```markdown
## Phase 45: Backend Core

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Built server infrastructure with auth and error handling.

### Middleware Chain

| Order | Middleware | Purpose |
|-------|------------|---------|
| 1 | helmet | Security headers |
| 2 | cors | Cross-origin |
| 3 | json | Body parsing |
| 4 | requestLogger | Logging |
| 5 | authenticate | JWT verification |
| 6 | errorHandler | Error formatting |

### Auth Endpoints

| Endpoint | Method | Status |
|----------|--------|--------|
| /api/auth/login | POST | ✅ |
| /api/auth/register | POST | ✅ |
| /api/auth/logout | POST | ✅ |

### Error Codes Implemented

| Code | HTTP Status |
|------|-------------|
| UNAUTHORIZED | 401 |
| FORBIDDEN | 403 |
| VALIDATION_ERROR | 400 |
| NOT_FOUND | 404 |
| INTERNAL_ERROR | 500 |

### Files Created
- src/backend/server.ts
- src/backend/middleware/*.ts
- src/backend/services/auth.ts
- src/backend/utils/response.ts

### Deviations
- None

### Notes
{{Any observations or decisions}}
```

---

## Rules

1. **Match ERROR-CODES.md exactly** — Error format must be consistent
2. **Use shared-types/** — All responses typed
3. **Middleware order matters** — Security first, errors last
4. **No business logic yet** — Only infrastructure

---

## Next Phase

After completing Phase 45, proceed to:

**Phase 46: API Implementation**

---
