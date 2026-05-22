# State Management — {{Project Name}}

---
Status: DRAFT | IN REVIEW | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
Owner: {{owner}}
---

## Overview

This document defines how state is managed throughout the frontend application.

**Stack:**
- Server State: {{TanStack Query / SWR / RTK Query}}
- Client State: {{Zustand / Redux / Svelte stores / Context}}
- Form State: {{React Hook Form / Formik / Superforms}}
- Persistence: {{LocalStorage / SessionStorage}}

---

## State Categories

| Category | Description | Tool | When to Use |
|----------|-------------|------|-------------|
| Server State | Data from API | {{library}} | Any API data |
| Client State | UI state | {{library}} | Shared UI state |
| URL State | Route params | Router | Shareable state |
| Form State | User input | {{library}} | Forms |
| Persisted State | Survives refresh | LocalStorage | User preferences |

---

## Server State

### Configuration

```typescript
// Query client configuration
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 30 * 1000,        // 30 seconds
      gcTime: 5 * 60 * 1000,       // 5 minutes (formerly cacheTime)
      retry: 1,
      refetchOnWindowFocus: true,
    },
  },
});
```

### Resources

| Resource | Query Key | Endpoint | Stale Time | Cache Time |
|----------|-----------|----------|------------|------------|
| Current User | `["user", "me"]` | GET /api/users/me | Infinity | Session |
| Projects List | `["projects", filters]` | GET /api/projects | 30s | 5min |
| Project Detail | `["project", id]` | GET /api/projects/:id | 30s | 5min |
| Stats | `["stats"]` | GET /api/stats | 60s | 5min |

### Query Patterns

#### Basic Query

```typescript
// Fetching projects
const { data, isLoading, error } = useQuery({
  queryKey: ["projects", { page, search }],
  queryFn: () => api.getProjects({ page, search }),
});
```

#### Mutation with Cache Update

```typescript
// Creating a project
const mutation = useMutation({
  mutationFn: api.createProject,
  onSuccess: (newProject) => {
    queryClient.invalidateQueries({ queryKey: ["projects"] });
  },
});
```

### Refetch Triggers

| Resource | Refetch On |
|----------|------------|
| Current User | Login, logout, profile update |
| Projects List | Create, delete, filter change |
| Project Detail | Update, navigate to |

---

## Client State

### Global State Shape

```typescript
interface AppState {
  // === UI State ===
  ui: {
    sidebarCollapsed: boolean;
    theme: "light" | "dark" | "system";
    toasts: Toast[];
  };
  
  // === UI Actions ===
  toggleSidebar: () => void;
  setTheme: (theme: Theme) => void;
  addToast: (toast: Toast) => void;
  removeToast: (id: string) => void;
}
```

### Store Implementation

```typescript
// Using Zustand
const useAppStore = create<AppState>()(
  persist(
    (set) => ({
      ui: {
        sidebarCollapsed: false,
        theme: "system",
        toasts: [],
      },
      
      toggleSidebar: () =>
        set((state) => ({
          ui: { ...state.ui, sidebarCollapsed: !state.ui.sidebarCollapsed },
        })),
      
      setTheme: (theme) =>
        set((state) => ({
          ui: { ...state.ui, theme },
        })),
      
      // Toast actions...
    }),
    {
      name: "app-storage",
      partialize: (state) => ({
        ui: {
          sidebarCollapsed: state.ui.sidebarCollapsed,
          theme: state.ui.theme,
        },
      }),
    }
  )
);
```

### Client State Inventory

| State | Scope | Initial | Persisted | Used By |
|-------|-------|---------|-----------|---------|
| `sidebarCollapsed` | Global | `false` | Yes | Sidebar, Layout |
| `theme` | Global | `"system"` | Yes | Theme provider |
| `toasts` | Global | `[]` | No | Toast container |
| `modalOpen` | Component | `false` | No | Various modals |
| `activeTab` | Component | `0` | No | Tab components |

---

## URL State

### Route Parameters

| Route | Param | Type | Description |
|-------|-------|------|-------------|
| `/projects/:id` | `id` | `string` | Project identifier |
| `/users/:userId` | `userId` | `string` | User identifier |

### Query Parameters

| Page | Param | Type | Default | Description |
|------|-------|------|---------|-------------|
| `/projects` | `page` | `number` | `1` | Current page |
| `/projects` | `search` | `string` | `""` | Search query |
| `/projects` | `status` | `string` | `"all"` | Status filter |
| `/login` | `returnUrl` | `string` | `/dashboard` | Redirect after login |

### URL State Sync

```typescript
// Reading URL state
const searchParams = useSearchParams();
const page = Number(searchParams.get("page")) || 1;

// Writing URL state
const setPage = (newPage: number) => {
  const params = new URLSearchParams(searchParams);
  params.set("page", String(newPage));
  router.push(`?${params.toString()}`);
};
```

---

## Form State

### Forms Inventory

| Form | Location | Fields | Validation |
|------|----------|--------|------------|
| LoginForm | `/login` | email, password | Required, format |
| RegisterForm | `/register` | name, email, password | Required, format, strength |
| ProjectForm | `/projects/new` | name, description | Required, length |
| SettingsForm | `/settings` | name, email, avatar | Format, size |

### Validation Schema Example

```typescript
// Using Zod
const loginSchema = z.object({
  email: z.string().email("Invalid email address"),
  password: z.string().min(8, "Password must be at least 8 characters"),
});

type LoginInput = z.infer<typeof loginSchema>;
```

### Form State Management

```typescript
// Using React Hook Form
const {
  register,
  handleSubmit,
  formState: { errors, isSubmitting },
} = useForm<LoginInput>({
  resolver: zodResolver(loginSchema),
});
```

---

## Persisted State

### What's Persisted

| Key | Storage | Type | Default | Expires |
|-----|---------|------|---------|---------|
| `app-storage` | LocalStorage | JSON | `{}` | Never |
| `theme` | LocalStorage | string | `"system"` | Never |
| `sidebar_collapsed` | LocalStorage | boolean | `false` | Never |

### What's NOT Persisted

| State | Reason |
|-------|--------|
| Server data | Managed by query cache |
| Form drafts | Intentionally temporary |
| Modal open | Transient UI state |
| Active tab | Not worth persisting |

### Persistence Implementation

```typescript
// Zustand persist middleware (see Client State section)
// OR manual localStorage
const theme = localStorage.getItem("theme") || "system";
```

### Hydration

```typescript
// Handle SSR hydration mismatch
const [mounted, setMounted] = useState(false);

useEffect(() => {
  setMounted(true);
}, []);

// Don't render theme-dependent UI until mounted
if (!mounted) return <Skeleton />;
```

---

## State Anti-Patterns

### ❌ DO NOT

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Duplicate server state in Redux | Sync bugs, stale data | Use TanStack Query |
| Store derived state | Gets out of sync | Compute in render/selector |
| Global state for local UI | Complexity, re-renders | Component state |
| Couple navigation to data | Breaks back button | Separate concerns |
| No loading states | Poor UX, layout shift | Always handle loading |
| No error states | Silent failures | Always handle errors |
| Store full API response | Memory bloat | Store only needed fields |
| Mutate state directly | Unpredictable updates | Use immutable updates |

### ✅ DO

| Practice | Benefit |
|----------|---------|
| Server state in query cache | Automatic caching, deduplication |
| Derive state in selectors | Single source of truth |
| Colocate state with usage | Easier reasoning |
| URL state for shareable | Bookmarkable, back button works |
| Optimistic updates | Better perceived performance |
| Error boundaries | Graceful failure |

---

## State Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                         URL State                            │
│  /projects?page=2&search=foo                                │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Server State                            │
│  TanStack Query Cache                                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │ user/me     │  │ projects    │  │ project/123 │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Client State                            │
│  Zustand Store                                               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ui: { sidebarCollapsed, theme, toasts }             │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     Component State                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                  │
│  │ modalOpen│  │ activeTab│  │ formData │                  │
│  └──────────┘  └──────────┘  └──────────┘                  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Persisted State                         │
│  LocalStorage                                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ theme, sidebarCollapsed                              │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## Client-Side Logic (If Applicable)

> **Note:** If your frontend has significant client-side business logic that does NOT involve API calls, document it here. Otherwise, mark N/A.

### Heavy Client Logic Inventory

| Logic Module | Purpose | Complexity | Location |
|--------------|---------|------------|----------|
| {{module}} | {{purpose}} | High/Medium | {{file or "TBD"}} |

**Examples of heavy client logic:**
- Complex calculators (mortgage, tax, pricing)
- Client-side data transformations
- Real-time visualizations
- Game loops
- Image/video processing
- Offline-first sync logic

### Logic Module Details (if applicable)

#### {{Module Name}}

**Purpose:** {{what it does}}

**Inputs:**
| Input | Type | Source |
|-------|------|--------|
| {{input}} | {{type}} | {{where it comes from}} |

**Outputs:**
| Output | Type | Destination |
|--------|------|-------------|
| {{output}} | {{type}} | {{where it goes}} |

**Algorithm/Logic:**
{{High-level description of the logic, not code}}

**Performance Considerations:**
- {{consideration}}

**Testing Requirements:**
- {{requirement}}

---

**If N/A:** This application does not have significant client-side business logic. All business logic lives on the backend.

---

## State Ownership Table

| State | Owner | Category | Source | Updates On | Persisted |
|-------|-------|----------|--------|------------|-----------|
| Current user | Query cache | Server | GET /api/users/me | Login, profile update | No |
| Projects list | Query cache | Server | GET /api/projects | CRUD, filter change | No |
| Project detail | Query cache | Server | GET /api/projects/:id | Update, navigate | No |
| Sidebar collapsed | Zustand | Client | User action | Toggle click | Yes |
| Theme | Zustand | Client | User preference | Settings change | Yes |
| Toasts | Zustand | Client | System events | Add/remove | No |
| Page number | URL | URL | Query param | Pagination click | N/A |
| Search query | URL | URL | Query param | Search input | N/A |
| Login form | Component | Form | User input | Typing | No |
| Modal open | Component | Component | User action | Open/close | No |

---

## Notes

{{Any additional notes about state management decisions or patterns}}

---
Generated: {{timestamp}}
Phase: 41 (State Management)
---
