# Architecture Patterns

---
Section: 0f. Implementation
Purpose: Coding standards for consistent implementation across all phases
Version: v1.0.0
---

## Overview

This document defines the coding patterns and conventions for implementation. These patterns ensure any implementer (human or AI) produces consistent, maintainable code.

**Key Principle:** Code must be written for AI comprehension, not just human comprehension. The ~1,500 LOC constraint exists for AI context limits.

---

## Directory Patterns

### Feature-Based Organization (Recommended)

```
src/
├── features/                    # Feature modules
│   ├── auth/
│   │   ├── components/          # Feature-specific components
│   │   │   ├── LoginForm.svelte
│   │   │   └── LogoutButton.svelte
│   │   ├── api/                 # Feature API calls
│   │   │   └── authApi.ts
│   │   ├── stores/              # Feature state
│   │   │   └── authStore.ts
│   │   ├── types.ts             # Feature-specific types (re-exports from shared)
│   │   └── index.ts             # Public exports
│   │
│   ├── projects/
│   │   ├── components/
│   │   │   ├── ProjectCard.svelte
│   │   │   ├── ProjectList.svelte
│   │   │   └── ProjectForm.svelte
│   │   ├── api/
│   │   │   └── projectsApi.ts
│   │   ├── stores/
│   │   │   └── projectsStore.ts
│   │   ├── types.ts
│   │   └── index.ts
│   │
│   └── settings/
│       └── ...
│
├── shared/                      # Shared across features
│   ├── components/              # Generic UI components
│   │   ├── Button.svelte
│   │   ├── Input.svelte
│   │   ├── Modal.svelte
│   │   └── ...
│   ├── hooks/                   # Shared hooks/utilities
│   │   ├── useDebounce.ts
│   │   └── useLocalStorage.ts
│   ├── utils/                   # Pure utility functions
│   │   ├── formatDate.ts
│   │   ├── validateEmail.ts
│   │   └── ...
│   └── types/                   # shared-types/ (THE source of truth)
│       ├── index.ts
│       ├── user.ts
│       ├── project.ts
│       └── api.ts
│
├── lib/                         # External integrations
│   ├── api/                     # API client setup
│   │   └── client.ts
│   ├── auth/                    # Auth provider integration
│   │   └── provider.ts
│   └── analytics/               # Analytics integration
│       └── tracker.ts
│
├── routes/                      # Page routes (SvelteKit/Next.js)
│   ├── +layout.svelte
│   ├── +page.svelte
│   ├── auth/
│   │   ├── login/+page.svelte
│   │   └── logout/+page.svelte
│   └── projects/
│       ├── +page.svelte
│       └── [id]/+page.svelte
│
└── app.css                      # Global styles / tokens
```

### Layer-Based Organization (Alternative)

Use only if REPO-STRUCTURE.md specifies this pattern:

```
src/
├── components/                  # All components
├── pages/                       # All pages
├── stores/                      # All state
├── api/                         # All API calls
├── utils/                       # All utilities
└── types/                       # All types
```

---

## Naming Conventions

### Files

| Type | Convention | Example |
|------|------------|---------|
| Components | PascalCase | `ProjectCard.svelte` |
| Pages | PascalCase or +page | `Dashboard.svelte` or `+page.svelte` |
| Utilities | camelCase | `formatDate.ts` |
| Stores | camelCase + Store | `projectsStore.ts` |
| API modules | camelCase + Api | `projectsApi.ts` |
| Types | camelCase | `project.ts` |
| Constants | camelCase or UPPER_SNAKE | `config.ts` or `CONSTANTS.ts` |
| Tests | same as source + .test | `formatDate.test.ts` |

### Folders

| Type | Convention | Example |
|------|------------|---------|
| Feature folders | kebab-case | `user-settings/` |
| Component folders | kebab-case | `form-controls/` |
| Route folders | kebab-case | `project-details/` |

### Code

| Type | Convention | Example |
|------|------------|---------|
| Components | PascalCase | `ProjectCard` |
| Functions | camelCase | `formatDate()` |
| Variables | camelCase | `projectList` |
| Constants | UPPER_SNAKE | `MAX_RETRY_COUNT` |
| Types/Interfaces | PascalCase | `ProjectResponse` |
| Enums | PascalCase | `ProjectStatus` |
| Enum values | UPPER_SNAKE | `ProjectStatus.IN_PROGRESS` |
| Boolean variables | is/has/can prefix | `isLoading`, `hasError`, `canEdit` |
| Event handlers | handle prefix | `handleClick`, `handleSubmit` |
| Async functions | verb prefix | `fetchProjects`, `createUser` |

---

## Component Patterns

### Svelte Component Structure

```svelte
<!-- 
  ProjectCard.svelte
  Displays a project summary card with actions.
-->
<script lang="ts">
  // ============================================
  // 1. IMPORTS
  // ============================================
  import type { Project } from '$shared/types';
  import Button from '$shared/components/Button.svelte';
  import { formatDate } from '$shared/utils/formatDate';
  
  // ============================================
  // 2. PROPS
  // ============================================
  export let project: Project;
  export let onEdit: ((id: string) => void) | undefined = undefined;
  export let onDelete: ((id: string) => void) | undefined = undefined;
  
  // ============================================
  // 3. STATE
  // ============================================
  let isExpanded = false;
  let isDeleting = false;
  
  // ============================================
  // 4. DERIVED
  // ============================================
  $: formattedDate = formatDate(project.createdAt);
  $: canEdit = project.status !== 'archived';
  
  // ============================================
  // 5. HANDLERS
  // ============================================
  function handleToggleExpand() {
    isExpanded = !isExpanded;
  }
  
  async function handleDelete() {
    if (!onDelete) return;
    isDeleting = true;
    try {
      await onDelete(project.id);
    } finally {
      isDeleting = false;
    }
  }
  
  // ============================================
  // 6. LIFECYCLE
  // ============================================
  // (onMount, onDestroy if needed)
</script>

<!-- ============================================ -->
<!-- TEMPLATE -->
<!-- ============================================ -->
<article class="project-card" class:expanded={isExpanded}>
  <header>
    <h3>{project.name}</h3>
    <time datetime={project.createdAt}>{formattedDate}</time>
  </header>
  
  {#if isExpanded}
    <p>{project.description}</p>
  {/if}
  
  <footer>
    <Button on:click={handleToggleExpand}>
      {isExpanded ? 'Show less' : 'Show more'}
    </Button>
    
    {#if canEdit && onEdit}
      <Button variant="secondary" on:click={() => onEdit(project.id)}>
        Edit
      </Button>
    {/if}
    
    {#if onDelete}
      <Button 
        variant="danger" 
        loading={isDeleting}
        on:click={handleDelete}
      >
        Delete
      </Button>
    {/if}
  </footer>
</article>

<!-- ============================================ -->
<!-- STYLES -->
<!-- ============================================ -->
<style>
  .project-card {
    padding: var(--space-md);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-md);
  }
  
  .project-card.expanded {
    background: var(--color-surface-alt);
  }
  
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  footer {
    display: flex;
    gap: var(--space-sm);
    margin-top: var(--space-md);
  }
</style>
```

### React Component Structure

```tsx
/**
 * ProjectCard.tsx
 * Displays a project summary card with actions.
 */

// ============================================
// 1. IMPORTS
// ============================================
import { useState } from 'react';
import type { Project } from '@/shared/types';
import { Button } from '@/shared/components/Button';
import { formatDate } from '@/shared/utils/formatDate';

// ============================================
// 2. TYPES
// ============================================
interface ProjectCardProps {
  project: Project;
  onEdit?: (id: string) => void;
  onDelete?: (id: string) => Promise<void>;
}

// ============================================
// 3. COMPONENT
// ============================================
export function ProjectCard({ project, onEdit, onDelete }: ProjectCardProps) {
  // State
  const [isExpanded, setIsExpanded] = useState(false);
  const [isDeleting, setIsDeleting] = useState(false);
  
  // Derived
  const formattedDate = formatDate(project.createdAt);
  const canEdit = project.status !== 'archived';
  
  // Handlers
  const handleToggleExpand = () => {
    setIsExpanded(!isExpanded);
  };
  
  const handleDelete = async () => {
    if (!onDelete) return;
    setIsDeleting(true);
    try {
      await onDelete(project.id);
    } finally {
      setIsDeleting(false);
    }
  };
  
  // Render
  return (
    <article className={`project-card ${isExpanded ? 'expanded' : ''}`}>
      <header>
        <h3>{project.name}</h3>
        <time dateTime={project.createdAt}>{formattedDate}</time>
      </header>
      
      {isExpanded && <p>{project.description}</p>}
      
      <footer>
        <Button onClick={handleToggleExpand}>
          {isExpanded ? 'Show less' : 'Show more'}
        </Button>
        
        {canEdit && onEdit && (
          <Button variant="secondary" onClick={() => onEdit(project.id)}>
            Edit
          </Button>
        )}
        
        {onDelete && (
          <Button 
            variant="danger" 
            loading={isDeleting}
            onClick={handleDelete}
          >
            Delete
          </Button>
        )}
      </footer>
    </article>
  );
}
```

---

## API Patterns

### API Client Setup

```typescript
// lib/api/client.ts

import type { ApiError } from '@/shared/types';

const BASE_URL = import.meta.env.VITE_API_URL || '/api';

interface RequestOptions extends RequestInit {
  params?: Record<string, string>;
}

class ApiClient {
  private baseUrl: string;
  private token: string | null = null;
  
  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }
  
  setToken(token: string | null) {
    this.token = token;
  }
  
  private async request<T>(
    endpoint: string,
    options: RequestOptions = {}
  ): Promise<T> {
    const { params, ...fetchOptions } = options;
    
    // Build URL with params
    let url = `${this.baseUrl}${endpoint}`;
    if (params) {
      const searchParams = new URLSearchParams(params);
      url += `?${searchParams}`;
    }
    
    // Add auth header
    const headers = new Headers(fetchOptions.headers);
    if (this.token) {
      headers.set('Authorization', `Bearer ${this.token}`);
    }
    headers.set('Content-Type', 'application/json');
    
    // Make request
    const response = await fetch(url, {
      ...fetchOptions,
      headers,
    });
    
    // Handle errors
    if (!response.ok) {
      const error: ApiError = await response.json();
      throw new ApiRequestError(error);
    }
    
    return response.json();
  }
  
  get<T>(endpoint: string, params?: Record<string, string>) {
    return this.request<T>(endpoint, { method: 'GET', params });
  }
  
  post<T>(endpoint: string, data: unknown) {
    return this.request<T>(endpoint, {
      method: 'POST',
      body: JSON.stringify(data),
    });
  }
  
  put<T>(endpoint: string, data: unknown) {
    return this.request<T>(endpoint, {
      method: 'PUT',
      body: JSON.stringify(data),
    });
  }
  
  delete<T>(endpoint: string) {
    return this.request<T>(endpoint, { method: 'DELETE' });
  }
}

export const api = new ApiClient(BASE_URL);
```

### Feature API Module

```typescript
// features/projects/api/projectsApi.ts

import { api } from '@/lib/api/client';
import type { 
  Project, 
  ProjectCreate, 
  ProjectUpdate,
  PaginatedResponse 
} from '@/shared/types';

export const projectsApi = {
  list: (page = 1, limit = 20) => 
    api.get<PaginatedResponse<Project>>('/projects', { 
      page: String(page), 
      limit: String(limit) 
    }),
  
  get: (id: string) => 
    api.get<Project>(`/projects/${id}`),
  
  create: (data: ProjectCreate) => 
    api.post<Project>('/projects', data),
  
  update: (id: string, data: ProjectUpdate) => 
    api.put<Project>(`/projects/${id}`, data),
  
  delete: (id: string) => 
    api.delete<void>(`/projects/${id}`),
};
```

---

## Error Handling Patterns

### Backend Error Handler

```typescript
// backend/middleware/errorHandler.ts

import type { Request, Response, NextFunction } from 'express';
import type { ApiError } from '@/shared/types';
import { ERROR_CODES } from '@/shared/types/errors';

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

export function errorHandler(
  error: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  // Log error (but not in tests)
  if (process.env.NODE_ENV !== 'test') {
    console.error('Error:', error);
  }
  
  // Handle known errors
  if (error instanceof AppError) {
    const response: ApiError = {
      error_code: error.code,
      message: error.message,
      details: error.details,
    };
    return res.status(error.statusCode).json(response);
  }
  
  // Handle validation errors (e.g., from Zod)
  if (error.name === 'ZodError') {
    const response: ApiError = {
      error_code: ERROR_CODES.VALIDATION_ERROR,
      message: 'Validation failed',
      details: (error as any).errors,
    };
    return res.status(400).json(response);
  }
  
  // Handle unknown errors
  const response: ApiError = {
    error_code: ERROR_CODES.INTERNAL_ERROR,
    message: 'An unexpected error occurred',
  };
  return res.status(500).json(response);
}
```

### Backend Route Handler Pattern

```typescript
// backend/routes/projects.ts

import { Router } from 'express';
import { z } from 'zod';
import { AppError } from '../middleware/errorHandler';
import { authenticate, authorize } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { projectsService } from '../services/projects';
import type { Project } from '@/shared/types';

const router = Router();

// Validation schemas
const createProjectSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().max(1000).optional(),
});

const updateProjectSchema = createProjectSchema.partial();

// Routes
router.get('/', 
  authenticate,
  async (req, res, next) => {
    try {
      const { page = '1', limit = '20' } = req.query;
      const projects = await projectsService.list(
        req.user.id,
        parseInt(page as string),
        parseInt(limit as string)
      );
      res.json(projects);
    } catch (error) {
      next(error);
    }
  }
);

router.get('/:id',
  authenticate,
  async (req, res, next) => {
    try {
      const project = await projectsService.get(req.params.id);
      
      if (!project) {
        throw new AppError('PROJECT_NOT_FOUND', 'Project not found', 404);
      }
      
      if (project.userId !== req.user.id) {
        throw new AppError('FORBIDDEN', 'Access denied', 403);
      }
      
      res.json(project);
    } catch (error) {
      next(error);
    }
  }
);

router.post('/',
  authenticate,
  validate(createProjectSchema),
  async (req, res, next) => {
    try {
      const project = await projectsService.create({
        ...req.body,
        userId: req.user.id,
      });
      res.status(201).json(project);
    } catch (error) {
      next(error);
    }
  }
);

// ... more routes

export { router as projectsRouter };
```

### Frontend Error Handling

```typescript
// shared/utils/handleApiError.ts

import type { ApiError } from '@/shared/types';

export class ApiRequestError extends Error {
  constructor(public apiError: ApiError) {
    super(apiError.message);
    this.name = 'ApiRequestError';
  }
}

export function getErrorMessage(error: unknown): string {
  if (error instanceof ApiRequestError) {
    return error.apiError.message;
  }
  if (error instanceof Error) {
    return error.message;
  }
  return 'An unexpected error occurred';
}

export function isNotFoundError(error: unknown): boolean {
  return error instanceof ApiRequestError && 
    error.apiError.error_code === 'NOT_FOUND';
}

export function isAuthError(error: unknown): boolean {
  return error instanceof ApiRequestError && 
    ['UNAUTHORIZED', 'FORBIDDEN', 'TOKEN_EXPIRED'].includes(
      error.apiError.error_code
    );
}
```

---

## State Management Patterns

### Svelte Store Pattern

```typescript
// features/projects/stores/projectsStore.ts

import { writable, derived } from 'svelte/store';
import type { Project } from '@/shared/types';
import { projectsApi } from '../api/projectsApi';

interface ProjectsState {
  items: Project[];
  loading: boolean;
  error: string | null;
  selectedId: string | null;
}

const initialState: ProjectsState = {
  items: [],
  loading: false,
  error: null,
  selectedId: null,
};

function createProjectsStore() {
  const { subscribe, set, update } = writable<ProjectsState>(initialState);
  
  return {
    subscribe,
    
    async load() {
      update(s => ({ ...s, loading: true, error: null }));
      try {
        const response = await projectsApi.list();
        update(s => ({ ...s, items: response.data, loading: false }));
      } catch (error) {
        update(s => ({ 
          ...s, 
          loading: false, 
          error: getErrorMessage(error) 
        }));
      }
    },
    
    async create(data: ProjectCreate) {
      update(s => ({ ...s, loading: true, error: null }));
      try {
        const project = await projectsApi.create(data);
        update(s => ({ 
          ...s, 
          items: [...s.items, project], 
          loading: false 
        }));
        return project;
      } catch (error) {
        update(s => ({ 
          ...s, 
          loading: false, 
          error: getErrorMessage(error) 
        }));
        throw error;
      }
    },
    
    select(id: string | null) {
      update(s => ({ ...s, selectedId: id }));
    },
    
    reset() {
      set(initialState);
    },
  };
}

export const projectsStore = createProjectsStore();

// Derived stores
export const selectedProject = derived(
  projectsStore,
  $store => $store.items.find(p => p.id === $store.selectedId) ?? null
);

export const projectCount = derived(
  projectsStore,
  $store => $store.items.length
);
```

---

## Anti-Patterns (Explicitly Forbidden)

These patterns are not allowed regardless of framework:

### 1. Cross-Feature Imports Without Abstraction

```
❌ WRONG:
features/projects/utils.ts imports from features/users/internal.ts

✅ RIGHT:
features/projects/utils.ts imports from shared/utils/
```

Features should not reach into each other. Use shared abstractions.

### 2. Business Logic in UI Components

```
❌ WRONG:
// Inside PriceDisplay component
function calculateDiscount(price, tier) { ... }

✅ RIGHT:
// Business logic in service/util
import { calculateDiscount } from '$lib/services/pricing';
```

Components render. Services calculate.

### 3. API Calls in Pure Components

```
❌ WRONG:
// Inside PriceDisplay component
const price = await fetch('/api/price');

✅ RIGHT:
// API calls in pages or dedicated data components
// Pure components receive data as props
```

Keep data fetching at page/container level.

### 4. Duplicated Types

```
❌ WRONG:
// In features/projects/types.ts
interface Project { id: string; name: string; }

// In features/tasks/types.ts  
interface Project { id: string; name: string; } // Duplicate!

✅ RIGHT:
// In shared/types/project.ts (single source)
export interface Project { id: string; name: string; }
```

Types live in shared-types/ only.

### 5. Manual Edits to Generated Files

```
❌ WRONG:
// Editing node_modules/ or generated client files

✅ RIGHT:
// Edit source (schema, config), then regenerate
```

Generated files are read-only.

### 6. Hardcoded Environment Values

```
❌ WRONG:
const API_URL = 'https://api.production.com';

✅ RIGHT:
const API_URL = import.meta.env.VITE_API_URL;
// Or equivalent per framework
```

All environment-specific values from environment variables.

---

## File Size Guidelines

### Targets

| Type | Target | Hard Limit |
|------|--------|------------|
| Component file | ~150 LOC | 300 LOC |
| Utility file | ~100 LOC | 200 LOC |
| Store file | ~150 LOC | 300 LOC |
| API module | ~100 LOC | 200 LOC |
| Route handler | ~200 LOC | 400 LOC |
| Any single file | ~300 LOC | 500 LOC |
| Feature module (total) | ~800 LOC | 1,500 LOC |

### When to Split

Split a file when:
- Exceeds 300 LOC
- Has more than 3 distinct responsibilities
- Contains unrelated functions
- Has complex sections that could be tested independently

### How to Split

**Components:**
```
Before: BigForm.svelte (400 LOC)
After:
├── BigForm.svelte (150 LOC) - orchestration
├── FormHeader.svelte (80 LOC)
├── FormFields.svelte (100 LOC)
└── FormActions.svelte (70 LOC)
```

**Utilities:**
```
Before: utils.ts (500 LOC)
After:
├── dateUtils.ts (100 LOC)
├── stringUtils.ts (80 LOC)
├── validationUtils.ts (120 LOC)
└── formatUtils.ts (100 LOC)
```

---

## Comments Guidelines

### When to Comment

**DO comment:**
- Why a non-obvious approach was chosen
- Business logic that isn't self-evident
- Workarounds for bugs or limitations
- Complex algorithms
- Public API documentation

**DON'T comment:**
- What the code does (code should be self-documenting)
- Obvious operations
- Every function (only public APIs need JSDoc)

### Comment Format

```typescript
// Single-line: brief explanation
const result = complexCalculation(); // Why this formula is used

/**
 * Multi-line: for complex explanations
 * 
 * This approach is used because the standard method
 * fails when the input contains unicode characters.
 * See: https://github.com/issue/123
 */
function handleUnicodeInput(input: string) {
  // ...
}

/**
 * JSDoc: for public APIs
 * @param userId - The user's unique identifier
 * @returns The user object or null if not found
 * @throws {AppError} When database connection fails
 */
export async function getUser(userId: string): Promise<User | null> {
  // ...
}
```

---

## Testing Patterns (For Reference)

Tests are written in 0g, but code should be testable:

```typescript
// ✅ Testable: Pure function, no side effects
export function calculateTotal(items: CartItem[]): number {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}

// ✅ Testable: Dependencies injected
export function createOrderService(db: Database, mailer: Mailer) {
  return {
    async createOrder(data: OrderCreate) {
      const order = await db.orders.create(data);
      await mailer.send(order.email, 'Order confirmation', ...);
      return order;
    }
  };
}

// ❌ Hard to test: Hidden dependencies
export async function createOrder(data: OrderCreate) {
  const order = await globalDb.orders.create(data); // Hidden dep
  await sendEmail(order.email, ...); // Hidden side effect
  return order;
}
```

---
