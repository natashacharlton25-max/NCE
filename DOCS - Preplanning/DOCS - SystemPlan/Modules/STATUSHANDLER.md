# StatusHandler Module

> **Purpose:** Process status tracking, timeout management, and pause state handling
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

StatusHandler is the **status tracker** for all running processes. It monitors job statuses, tracks timeouts, handles pause states, and provides status queries. Think of it as the "air traffic control" display showing all active operations.

```
Job starts → StatusHandler.setStatus() → Status tracked → Query anytime
```

**StatusHandler tracks:**
- Running jobs and their current step
- Paused/waiting jobs
- Timeout countdowns
- Failed/completed job history

---

## Roles & Rules

### StatusHandler DOES:
- Track status of all jobs
- Monitor timeout countdowns
- Handle pause/resume states
- Provide status queries
- Track waiting jobs (human approval)
- Maintain status history
- Trigger timeout callbacks

### StatusHandler does NOT:
- Execute jobs (Orchestrator does that)
- Handle failures (FailureHandler does that)
- Queue jobs (QueueHandler does that)
- Log to audit (Logger does that)

### Boundaries:
- Status tracking only
- Cannot modify job execution
- Cannot skip timeout enforcement
- All status changes logged

---

## Job Statuses

| Status | Description | Can Transition To |
|--------|-------------|-------------------|
| `queued` | Waiting in queue | `running`, `cancelled` |
| `running` | Currently executing | `waiting`, `completed`, `failed`, `paused` |
| `waiting` | Waiting for external event | `running`, `failed`, `cancelled` |
| `paused` | Manually paused | `running`, `cancelled` |
| `completed` | Successfully finished | - |
| `failed` | Failed with error | - |
| `cancelled` | Manually cancelled | - |
| `timeout` | Timed out | `failed` |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `setStatus(jobId, status)` | Set job status | jobId, status | updated |
| `getStatus(jobId)` | Get job status | jobId | status |
| `getActiveJobs()` | Get all active jobs | - | jobs |
| `getWaitingJobs()` | Get jobs waiting for input | - | jobs |
| `pause(jobId)` | Pause a job | jobId | paused |
| `resume(jobId)` | Resume a paused job | jobId | resumed |
| `startTimeout(jobId, duration)` | Start timeout countdown | jobId, duration | started |
| `cancelTimeout(jobId)` | Cancel timeout | jobId | cancelled |
| `getTimeoutRemaining(jobId)` | Get time until timeout | jobId | remaining |

---

## Detailed Function Specs

### setStatus(jobId, status)

Set or update job status.

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc",
  status: "running",
  details: {
    currentStep: 3,
    totalSteps: 5,
    stepName: "AICaller.call",
    progress: 60
  }
}

// Output
{
  updated: true,
  jobId: "job_20260117_143052_content_abc",
  previousStatus: "queued",
  newStatus: "running",
  timestamp: "2026-01-17T14:30:52Z"
}
```

### getStatus(jobId)

Get current job status with full details.

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc"
}

// Output
{
  jobId: "job_20260117_143052_content_abc",
  status: "running",
  details: {
    currentStep: 3,
    totalSteps: 5,
    stepName: "AICaller.call",
    progress: 60
  },
  timing: {
    created: "2026-01-17T14:30:50Z",
    started: "2026-01-17T14:30:52Z",
    elapsed: 45000,
    estimatedRemaining: 30000
  },
  timeout: {
    active: true,
    remaining: 175000,
    expiresAt: "2026-01-17T14:34:52Z"
  }
}
```

### getActiveJobs()

Get all currently active jobs.

```javascript
// Output
{
  active: [
    {
      jobId: "job_20260117_143052_content_abc",
      chainName: "generate-blog-post",
      brandId: "acme",
      status: "running",
      progress: 60,
      startedAt: "2026-01-17T14:30:52Z"
    },
    {
      jobId: "job_20260117_143100_email_def",
      chainName: "generate-email",
      brandId: "globex",
      status: "running",
      progress: 25,
      startedAt: "2026-01-17T14:31:00Z"
    }
  ],
  total: 2
}
```

### getWaitingJobs()

Get jobs waiting for human input or external event.

```javascript
// Output
{
  waiting: [
    {
      jobId: "job_20260117_100000_brand_xyz",
      chainName: "build-brand",
      brandId: "initech",
      status: "waiting",
      waitingFor: "human_approval",
      waitingStep: "mission_statement",
      waitingSince: "2026-01-17T10:15:00Z",
      message: "Mission statement draft awaiting approval"
    }
  ],
  total: 1
}
```

### pause(jobId) / resume(jobId)

Pause and resume jobs.

```javascript
// Pause
{
  paused: true,
  jobId: "job_20260117_143052_content_abc",
  pausedAt: "2026-01-17T14:32:00Z",
  currentStep: 3,
  canResume: true
}

// Resume
{
  resumed: true,
  jobId: "job_20260117_143052_content_abc",
  resumedAt: "2026-01-17T14:35:00Z",
  pauseDuration: 180000,
  resumingStep: 3
}
```

### startTimeout(jobId, duration) / cancelTimeout(jobId)

Manage timeout countdowns.

```javascript
// Start timeout
{
  started: true,
  jobId: "job_20260117_143052_content_abc",
  duration: 180000,  // 3 minutes
  expiresAt: "2026-01-17T14:33:52Z"
}

// Timeout expires callback
{
  type: "timeout_expired",
  jobId: "job_20260117_143052_content_abc",
  duration: 180000,
  action: "notify_failure_handler"
}
```

---

## Status Dashboard Data

StatusHandler provides data for real-time dashboards:

```javascript
// Dashboard overview
{
  summary: {
    running: 2,
    waiting: 1,
    queued: 5,
    completedToday: 45,
    failedToday: 2
  },
  activeJobs: [...],
  waitingJobs: [...],
  recentCompleted: [...],
  recentFailed: [...]
}
```

---

## Timeout Levels

Integration with Orchestrator timeout levels:

| Level | Duration | Use Case |
|-------|----------|----------|
| 1 | 2 min | Quick operations |
| 2 | 3 min | Standard AI calls |
| 3 | 5 min | Complex generation |
| 4 | 15 min | Batch processing |

---

## Config

### module.config.json

```json
{
  "module": "StatusHandler",
  "version": "1.0.0",
  "description": "Process status and timeout tracking",
  "type": "system-service",

  "functions": [
    {
      "name": "setStatus",
      "description": "Set job status",
      "input": ["jobId", "status"],
      "output": "updated"
    },
    {
      "name": "getStatus",
      "description": "Get job status",
      "input": ["jobId"],
      "output": "status"
    },
    {
      "name": "getActiveJobs",
      "description": "Get all active jobs",
      "input": [],
      "output": "jobs"
    },
    {
      "name": "getWaitingJobs",
      "description": "Get jobs waiting for input",
      "input": [],
      "output": "jobs"
    },
    {
      "name": "pause",
      "description": "Pause a job",
      "input": ["jobId"],
      "output": "paused"
    },
    {
      "name": "resume",
      "description": "Resume a paused job",
      "input": ["jobId"],
      "output": "resumed"
    },
    {
      "name": "startTimeout",
      "description": "Start timeout countdown",
      "input": ["jobId", "duration"],
      "output": "started"
    },
    {
      "name": "cancelTimeout",
      "description": "Cancel timeout",
      "input": ["jobId"],
      "output": "cancelled"
    },
    {
      "name": "getTimeoutRemaining",
      "description": "Get time until timeout",
      "input": ["jobId"],
      "output": "remaining"
    }
  ],

  "config": {
    "historyRetention": 86400000,
    "statusCheckInterval": 1000,
    "dashboardUpdateInterval": 5000
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Logger | Logging status changes |

---

## Used By

| Component | How |
|-----------|-----|
| Orchestrator | Status updates during execution |
| FailureHandler | Timeout notifications |
| UI/Dashboard | Status display |

---

*Last updated: 2026-01-17*
