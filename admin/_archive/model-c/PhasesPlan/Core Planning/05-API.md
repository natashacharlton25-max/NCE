# Interface Layer

> Defines how you interact with the system: CLI, REST API, and Svelte webapp.

---

## Overview

Three ways to interact with the system:

| Interface | Purpose | When to Use |
|-----------|---------|-------------|
| **CLI** | Direct command execution | Local development, scripts, automation |
| **REST API** | Programmatic access | External integrations, webhooks |
| **Svelte Webapp** | Visual interface | Remote access, monitoring, settings |

---

## CLI Interface

Command-line interface for local operation.

### Job Commands

```bash
# Create and run a job
node run job:create <job-type> [options]

# Examples
node run job:create brand-build --brand=mindthebox
node run job:create colour-palette --brand=acme --mood=professional
node run job:create social-campaign --brand=techco --platforms=instagram,linkedin --posts=12
node run job:create blog-batch --brand=startup --topics="AI trends,Remote work,Productivity" --count=3

# Job management
node run job:status <job-id>           # Check job status
node run job:list                      # List all jobs
node run job:list --status=running     # Filter by status
node run job:cancel <job-id>           # Cancel running job
node run job:retry <job-id>            # Retry failed job
node run job:pause <job-id>            # Pause running job
node run job:resume <job-id>           # Resume paused job
```

### Task Commands

```bash
# View tasks for a job
node run task:list <job-id>
node run task:status <task-id>
node run task:output <task-id>         # View task output
```

### System Commands

```bash
# System status
node run system:status                 # Overall system health
node run system:validators             # Validator states (rate, token, budget)
node run system:providers              # Provider health and limits

# Budget
node run budget:status                 # Current budget state
node run budget:set daily 50           # Set daily budget
node run budget:approvals              # Pending approval requests
node run budget:approve <approval-id>  # Approve pending cost

# Queue
node run queue:list                    # View task queue
node run queue:priority                # View priority queue (waiting batches)

# Archive
node run archive:list                  # List archived jobs
node run archive:view <job-id>         # View archived job details
node run archive:cleanup               # Run manual cleanup
```

### Brand Commands

```bash
# Brand management
node run brand:list                    # List all brands
node run brand:create <name>           # Create new brand
node run brand:view <brand-id>         # View brand details
node run brand:update <brand-id>       # Update brand
```

### Output Format

```bash
# Default: human-readable
node run job:status abc123

# JSON output for scripting
node run job:status abc123 --json

# Verbose output
node run job:create brand-build --brand=test --verbose
```

---

## REST API

HTTP API for programmatic access and webapp backend.

### Base URL

```
Local:      http://localhost:3000/api
Production: https://your-domain.com/api
```

### Authentication

Single-user Google OAuth for now.

```
Authorization: Bearer <google-oauth-token>
```

### Endpoints

#### Jobs

```http
# Create job
POST /api/jobs
Content-Type: application/json

{
  "type": "brand-build",
  "config": {
    "brand": "mindthebox",
    "options": {}
  }
}

Response: 201 Created
{
  "id": "job-abc123",
  "type": "brand-build",
  "status": "pending",
  "createdAt": "2026-01-18T10:30:00Z"
}
```

```http
# List jobs
GET /api/jobs
GET /api/jobs?status=running
GET /api/jobs?type=brand-build
GET /api/jobs?limit=10&offset=0

Response: 200 OK
{
  "jobs": [...],
  "total": 45,
  "limit": 10,
  "offset": 0
}
```

```http
# Get job details
GET /api/jobs/:id

Response: 200 OK
{
  "id": "job-abc123",
  "type": "brand-build",
  "status": "running",
  "progress": 45,
  "tasks": [...],
  "createdAt": "...",
  "startedAt": "..."
}
```

```http
# Job actions
POST /api/jobs/:id/cancel
POST /api/jobs/:id/pause
POST /api/jobs/:id/resume
POST /api/jobs/:id/retry
```

#### Tasks

```http
# Get tasks for job
GET /api/jobs/:jobId/tasks

# Get task details
GET /api/tasks/:id

# Get task output
GET /api/tasks/:id/output
```

#### System Status

```http
# Overall health
GET /api/health

Response: 200 OK
{
  "status": "healthy",
  "uptime": 86400,
  "version": "1.0.0",
  "database": "connected",
  "providers": {
    "openai": "healthy",
    "anthropic": "healthy"
  }
}
```

```http
# Validator states
GET /api/validators

Response: 200 OK
{
  "apiRate": {
    "openai": { "used": 45, "limit": 60, "resetsIn": 35 },
    "anthropic": { "used": 12, "limit": 60, "resetsIn": 35 }
  },
  "tokens": {
    "gpt-4o": { "used": 45000, "limit": 90000, "resetsIn": 35 },
    "claude-sonnet": { "used": 12000, "limit": 40000, "resetsIn": 35 }
  },
  "budget": {
    "daily": { "used": 8.50, "limit": 50.00 },
    "monthly": { "used": 142.00, "limit": 500.00 }
  }
}
```

```http
# Provider status
GET /api/providers

Response: 200 OK
{
  "providers": [
    {
      "id": "openai",
      "status": "healthy",
      "latency": 245,
      "errorRate": 0.01,
      "models": ["gpt-4o", "gpt-4o-mini"]
    }
  ]
}
```

#### Budget

```http
# Get budget status
GET /api/budget

# Update budget
PUT /api/budget
{
  "daily": 50.00,
  "monthly": 500.00
}

# List pending approvals
GET /api/budget/approvals

# Approve/reject
POST /api/budget/approvals/:id/approve
POST /api/budget/approvals/:id/reject
```

#### Brands

```http
GET /api/brands
GET /api/brands/:id
POST /api/brands
PUT /api/brands/:id
DELETE /api/brands/:id
```

#### Archive

```http
GET /api/archive/jobs
GET /api/archive/jobs/:id
```

#### Settings

```http
GET /api/settings
PUT /api/settings

{
  "notifications": {
    "email": "user@example.com",
    "onJobComplete": true,
    "onJobFailed": true,
    "onBudgetThreshold": true,
    "onApprovalNeeded": true
  },
  "defaults": {
    "model": "gpt-4o",
    "maxConcurrentJobs": 3
  }
}
```

### WebSocket (Real-time Updates)

```javascript
// Connect
const ws = new WebSocket('ws://localhost:3000/ws');

// Authentication
ws.send(JSON.stringify({
  type: 'auth',
  token: 'google-oauth-token'
}));

// Subscribe to events
ws.send(JSON.stringify({
  type: 'subscribe',
  events: ['job.*', 'budget.threshold']
}));

// Receive events
ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  // { type: 'job.completed', data: { jobId: '...' } }
};
```

### Event Types for WebSocket

| Event | When | Data |
|-------|------|------|
| `job.created` | New job started | `{ jobId, type }` |
| `job.progress` | Progress updated | `{ jobId, progress }` |
| `job.completed` | Job finished | `{ jobId, result }` |
| `job.failed` | Job failed | `{ jobId, error }` |
| `job.paused` | Job waiting | `{ jobId, reason }` |
| `task.completed` | Task done | `{ jobId, taskId }` |
| `budget.threshold` | Budget at 80% | `{ used, limit }` |
| `budget.exhausted` | Budget depleted | `{ resetAt }` |
| `approval.required` | Needs human | `{ approvalId, amount }` |

---

## Svelte Webapp

Visual interface for remote access, monitoring, and settings management.

### Technology Stack

```
Frontend: Svelte/SvelteKit
Styling:  TailwindCSS
Auth:     Google OAuth
Backend:  REST API + WebSocket
Hosting:  Self-hosted / Vercel / Railway
```

### Pages / Routes

```
/                       # Dashboard (overview)
/jobs                   # Job list
/jobs/:id               # Job detail
/jobs/new               # Create new job
/tasks/:id              # Task detail
/brands                 # Brand management
/brands/:id             # Brand detail
/brands/new             # Create brand
/budget                 # Budget overview & approvals
/settings               # User settings
/archive                # Archived jobs
/login                  # Google OAuth login
```

### Dashboard

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  DASHBOARD                                                     [Settings] [?]│
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐              │
│  │ JOBS TODAY      │  │ BUDGET          │  │ PROVIDERS       │              │
│  │ ───────────     │  │ ───────────     │  │ ───────────     │              │
│  │ Running: 2      │  │ Daily: $8/$50   │  │ OpenAI: OK      │              │
│  │ Completed: 8    │  │ Monthly: $142   │  │ Anthropic: OK   │              │
│  │ Failed: 0       │  │ [████░░░░] 16%  │  │ Gemini: OK      │              │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘              │
│                                                                              │
│  ACTIVE JOBS                                                    [+ New Job] │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │ Brand Build - mindthebox          [████████░░] 75%        Running    │  │
│  │ Social Campaign - acme            [██████████] 100%       Complete   │  │
│  │ Blog Batch - techco               [██░░░░░░░░] 20%        Paused ⚠   │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  RECENT ALERTS                                                               │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │ ⚠ Budget approval needed: $25 for image batch     [Approve] [Reject] │  │
│  │ ✓ Job completed: Brand Build for startup                              │  │
│  │ ⚠ OpenAI rate limit reached - using fallback                         │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Job Detail View

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  JOB: Brand Build - mindthebox                              [Pause] [Cancel]│
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Status: Running                    Progress: [████████░░] 75%              │
│  Started: 10:30 AM                  Est. Complete: 10:45 AM                 │
│  Cost: $1.20 (would cost: $1.20)                                            │
│                                                                              │
│  TASKS                                                                       │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │ ✓ Get brand values              Complete         0.2s                  │  │
│  │ ✓ Generate colour palette       Complete         2.1s       $0.15     │  │
│  │ ✓ Check accessibility           Complete         0.5s                  │  │
│  │ ● Generate typography           Running          ...        ~$0.10    │  │
│  │ ○ Generate taglines             Pending                     ~$0.20    │  │
│  │ ○ Write brand story             Pending                     ~$0.45    │  │
│  │ ○ Generate logo concepts        Pending                     ~$0.30    │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  LOGS                                                          [View Full]  │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │ 10:32:15  Using Claude for typography generation                      │  │
│  │ 10:32:10  Colour palette passed accessibility check                   │  │
│  │ 10:31:45  Generated 5-colour palette                                  │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Settings Page

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  SETTINGS                                                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  NOTIFICATIONS                                                               │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │ Email: user@example.com                              [Change]         │  │
│  │                                                                       │  │
│  │ [✓] Email on job completion                                          │  │
│  │ [✓] Email on job failure                                             │  │
│  │ [✓] Email when budget at 80%                                         │  │
│  │ [✓] Email when approval needed                                       │  │
│  │ [ ] Email daily summary                                               │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  BUDGET LIMITS                                                               │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │ Daily budget:    $[50.00]                                             │  │
│  │ Monthly budget:  $[500.00]                                            │  │
│  │ Alert threshold: [80]%                                                 │  │
│  │ Auto-approve up to: $[5.00]                                           │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  DEFAULTS                                                                    │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │ Default AI model:     [GPT-4o           ▼]                           │  │
│  │ Max concurrent jobs:  [3]                                             │  │
│  │ Default brand:        [mindthebox       ▼]                           │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│                                                        [Save Changes]       │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Mobile Responsive

The webapp should work on mobile for quick status checks:
- Simplified dashboard
- Job list and status
- Approve/reject budget requests
- View alerts

---

## Authentication

Single-user Google OAuth implementation.

### Flow

```
1. User visits /login
2. Redirect to Google OAuth
3. Google returns with auth code
4. Server exchanges code for tokens
5. Verify user email matches allowed user
6. Issue session token
7. Redirect to dashboard
```

### Configuration

```json
// config/auth.json
{
  "provider": "google",
  "allowedUsers": ["your.email@gmail.com"],
  "sessionDuration": "7d",
  "oauth": {
    "clientId": "FROM_ENV",
    "clientSecret": "FROM_ENV",
    "callbackUrl": "/api/auth/callback"
  }
}
```

### Environment Variables

```
GOOGLE_CLIENT_ID=your-client-id
GOOGLE_CLIENT_SECRET=your-client-secret
SESSION_SECRET=random-secret-for-signing-sessions
```

### Middleware

```typescript
// All /api/* routes (except /api/health) require auth
// All WebSocket connections require auth token
// CLI uses local execution (no auth needed)
```

---

## Notifications

Alert system for important events.

### Channels

| Channel | When Used |
|---------|-----------|
| **WebSocket** | Real-time updates in webapp |
| **Email** | Job complete/failed, budget alerts, approvals |
| **In-App** | All alerts shown in webapp |

### Notification Types

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  NOTIFICATION TYPES                                                          │
│                                                                              │
│  JOB NOTIFICATIONS                                                          │
│  ├── Job completed successfully                                             │
│  ├── Job failed (with error details)                                        │
│  └── Job paused (waiting for resources)                                     │
│                                                                              │
│  BUDGET NOTIFICATIONS                                                        │
│  ├── Daily budget at 80% threshold                                          │
│  ├── Daily budget exhausted                                                 │
│  ├── Approval needed for large cost                                         │
│  └── Monthly summary                                                        │
│                                                                              │
│  SYSTEM NOTIFICATIONS                                                        │
│  ├── Provider unhealthy (circuit breaker tripped)                           │
│  ├── Provider recovered                                                     │
│  └── System startup/shutdown                                                │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Email Templates

```
Subject: [Brand System] Job Completed: Brand Build for mindthebox

Your job has completed successfully.

Job: Brand Build
Brand: mindthebox
Duration: 15 minutes
Cost: $1.20 (would cost: $1.20)

Tasks completed: 8/8

View details: https://your-domain.com/jobs/abc123

---

Subject: [Brand System] Approval Required: $25.00 for image batch

A job requires budget approval to continue.

Job: Social Campaign
Task: Generate 50 images
Requested: $25.00
Current daily remaining: $22.00

Options:
- Approve: https://your-domain.com/budget/approvals/xyz789?action=approve
- Reject: https://your-domain.com/budget/approvals/xyz789?action=reject
- View alternatives: https://your-domain.com/budget/approvals/xyz789
```

---

## Error Responses

Consistent error format across API.

```json
{
  "error": {
    "code": "BUDGET_EXCEEDED",
    "message": "Daily budget of $50.00 has been exceeded",
    "details": {
      "used": 50.15,
      "limit": 50.00,
      "resetAt": "2026-01-19T00:00:00Z"
    }
  }
}
```

### Error Codes

| Code | HTTP Status | Meaning |
|------|-------------|---------|
| `UNAUTHORIZED` | 401 | Missing or invalid auth token |
| `FORBIDDEN` | 403 | User not allowed |
| `NOT_FOUND` | 404 | Resource doesn't exist |
| `VALIDATION_ERROR` | 400 | Invalid request data |
| `BUDGET_EXCEEDED` | 402 | Budget depleted |
| `RATE_LIMITED` | 429 | Too many requests |
| `PROVIDER_ERROR` | 502 | External API failed |
| `INTERNAL_ERROR` | 500 | Unexpected server error |

---

## File Structure for Interface

```
/src/
├── api/                        # REST API
│   ├── routes/
│   │   ├── jobs.ts
│   │   ├── tasks.ts
│   │   ├── brands.ts
│   │   ├── budget.ts
│   │   ├── settings.ts
│   │   ├── archive.ts
│   │   └── health.ts
│   ├── middleware/
│   │   ├── auth.ts
│   │   ├── validation.ts
│   │   └── error-handler.ts
│   ├── websocket/
│   │   └── handler.ts
│   └── server.ts
│
├── cli/                        # CLI commands
│   ├── commands/
│   │   ├── job.ts
│   │   ├── task.ts
│   │   ├── brand.ts
│   │   ├── budget.ts
│   │   ├── system.ts
│   │   └── archive.ts
│   └── index.ts
│
└── webapp/                     # Svelte app (separate repo or monorepo)
    ├── src/
    │   ├── routes/
    │   │   ├── +page.svelte        # Dashboard
    │   │   ├── jobs/
    │   │   ├── brands/
    │   │   ├── budget/
    │   │   ├── settings/
    │   │   └── login/
    │   ├── lib/
    │   │   ├── api.ts              # API client
    │   │   ├── websocket.ts        # WS connection
    │   │   └── stores/             # Svelte stores
    │   └── app.html
    ├── static/
    └── svelte.config.js
```

---

## Summary

| Interface | Local Dev | Production |
|-----------|-----------|------------|
| **CLI** | `node run job:create ...` | Same |
| **REST API** | `localhost:3000/api` | `your-domain.com/api` |
| **Webapp** | `localhost:5173` | `your-domain.com` |
| **WebSocket** | `ws://localhost:3000/ws` | `wss://your-domain.com/ws` |

### Auth Summary

| Interface | Auth Method |
|-----------|-------------|
| **CLI** | None (local execution) |
| **REST API** | Google OAuth Bearer token |
| **Webapp** | Google OAuth session |
| **WebSocket** | Token in first message |

---

## Next Documents

- [01-ARCHITECTURE.md](01-ARCHITECTURE.md) - Core architecture
- [02-SYSTEMS.md](02-SYSTEMS.md) - Complete list of all systems
- [03-STORAGE.md](03-STORAGE.md) - Database schema and config structure
- [04-FILE-STRUCTURE.md](04-FILE-STRUCTURE.md) - Project folder layout
- [06-BUILD-PLAN.md](06-MIGRATION-PLAN.md) - What to build, existing projects as reference
