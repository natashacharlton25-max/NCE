# Scheduler Module

> **Purpose:** Schedule and manage timed jobs, recurring tasks, and sync operations
> **Type:** System Service
> **Status:** Planning

---

## Overview

Scheduler manages all time-based operations - cron jobs, scheduled syncs, recurring tasks, delayed execution. It's the system's clock, ensuring things happen at the right time.

```
Schedule rule → Scheduler.schedule() → Trigger at time → Orchestrator.runChain()
```

**Capabilities:**
- One-time scheduled jobs
- Recurring jobs (cron-style)
- Delayed execution
- Storage sync scheduling
- Rate-limited batch processing

---

## Roles & Rules

### Scheduler DOES:
- Schedule one-time jobs
- Schedule recurring jobs (cron-style)
- Track job status and history
- Trigger Orchestrator when job time arrives
- Manage pause/resume/cancel for scheduled jobs

### Scheduler does NOT:
- Execute jobs (Orchestrator does that)
- Decide what to schedule (called by other modules)
- Handle job failures (FailureHandler does that)
- Process batches (BatchProcessor does that)

### Boundaries:
- Cannot execute job logic directly
- Cannot modify job parameters after scheduling
- Cannot bypass time-based triggers
- Must respect timezone settings

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `schedule(job)` | Schedule a job | job | scheduleId |
| `scheduleRecurring(job, cron)` | Schedule recurring job | job, cron | scheduleId |
| `cancel(scheduleId)` | Cancel scheduled job | scheduleId | cancelled |
| `pause(scheduleId)` | Pause recurring job | scheduleId | paused |
| `resume(scheduleId)` | Resume paused job | scheduleId | resumed |
| `list(filter)` | List scheduled jobs | filter | jobs |
| `getNextRun(scheduleId)` | Get next run time | scheduleId | nextRun |
| `runNow(scheduleId)` | Trigger immediate run | scheduleId | result |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Scheduler.scheduleSync` | Schedule storage sync | schedule (sync job) |
| `Scheduler.scheduleBatch` | Schedule batch processing | scheduleRecurring |

---

## Detailed Function Specs

### schedule(job)

Schedules a one-time job.

```javascript
// Input
{
  job: {
    name: "brand-kit-delivery",
    chain: "email-brand-kit",
    params: {
      brandId: "acme",
      recipient: "client@example.com"
    },
    runAt: "2026-01-20T09:00:00Z",
    timezone: "Europe/London"
  }
}

// Output
{
  scheduleId: "sched_abc123",
  name: "brand-kit-delivery",
  runAt: "2026-01-20T09:00:00Z",
  status: "scheduled"
}
```

### scheduleRecurring(job, cron)

Schedules a recurring job with cron expression.

```javascript
// Input
{
  job: {
    name: "daily-storage-sync",
    chain: "storage-sync",
    params: {
      sources: ["canva", "google-drive"],
      destination: "local"
    }
  },
  cron: "0 2 * * *",  // Every day at 2 AM
  timezone: "Europe/London"
}

// Output
{
  scheduleId: "sched_recurring_001",
  name: "daily-storage-sync",
  cron: "0 2 * * *",
  nextRun: "2026-01-18T02:00:00Z",
  status: "active"
}
```

### list(filter)

Lists scheduled jobs.

```javascript
// Input
{
  filter: {
    status: "active",
    type: "recurring"
  }
}

// Output
{
  jobs: [
    {
      scheduleId: "sched_recurring_001",
      name: "daily-storage-sync",
      type: "recurring",
      cron: "0 2 * * *",
      nextRun: "2026-01-18T02:00:00Z",
      lastRun: "2026-01-17T02:00:00Z",
      lastStatus: "success"
    },
    {
      scheduleId: "sched_recurring_002",
      name: "weekly-report",
      type: "recurring",
      cron: "0 9 * * 1",  // Monday 9 AM
      nextRun: "2026-01-20T09:00:00Z"
    }
  ],
  total: 2
}
```

---

## Cron Expression Reference

```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6) (Sunday = 0)
│ │ │ │ │
* * * * *
```

**Common patterns:**
| Pattern | Description |
|---------|-------------|
| `0 * * * *` | Every hour |
| `0 2 * * *` | Daily at 2 AM |
| `0 9 * * 1` | Monday at 9 AM |
| `0 0 1 * *` | First of month |
| `*/15 * * * *` | Every 15 minutes |

---

## Storage Sync Scheduling

```javascript
// Schedule storage sync
Scheduler.scheduleRecurring({
  job: {
    name: "canva-sync",
    chain: "storage-sync-canva",
    params: {
      source: "canva",
      folders: ["Brands", "Templates"],
      syncMode: "incremental"  // or "full"
    }
  },
  cron: "0 3 * * *"  // 3 AM daily
});

// The chain calls:
// CanvaIntegration.listFolders → compare with local → download new/changed
```

---

## Job Status

| Status | Description |
|--------|-------------|
| `scheduled` | Waiting for run time |
| `active` | Recurring job, waiting for next run |
| `running` | Currently executing |
| `paused` | Temporarily paused |
| `completed` | One-time job finished |
| `failed` | Last run failed |
| `cancelled` | Cancelled by user |

---

## Config

### module.config.json

```json
{
  "module": "Scheduler",
  "version": "1.0.0",
  "description": "Schedule and manage timed jobs",
  "type": "service",

  "functions": [
    {
      "name": "schedule",
      "description": "Schedule a job",
      "input": ["job"],
      "output": "scheduleId"
    },
    {
      "name": "scheduleRecurring",
      "description": "Schedule recurring job",
      "input": ["job", "cron"],
      "output": "scheduleId"
    },
    {
      "name": "cancel",
      "description": "Cancel scheduled job",
      "input": ["scheduleId"],
      "output": "cancelled"
    },
    {
      "name": "pause",
      "description": "Pause recurring job",
      "input": ["scheduleId"],
      "output": "paused"
    },
    {
      "name": "resume",
      "description": "Resume paused job",
      "input": ["scheduleId"],
      "output": "resumed"
    },
    {
      "name": "list",
      "description": "List scheduled jobs",
      "input": ["filter"],
      "output": "jobs"
    },
    {
      "name": "getNextRun",
      "description": "Get next run time",
      "input": ["scheduleId"],
      "output": "nextRun"
    },
    {
      "name": "runNow",
      "description": "Trigger immediate run",
      "input": ["scheduleId"],
      "output": "result"
    }
  ],

  "steps": [
    {
      "name": "scheduleSync",
      "description": "Schedule storage sync",
      "sequence": ["schedule"]
    },
    {
      "name": "scheduleBatch",
      "description": "Schedule batch processing",
      "sequence": ["scheduleRecurring"]
    }
  ],

  "config": {
    "defaultTimezone": "Europe/London",
    "maxConcurrentJobs": 5,
    "retryFailedJobs": true,
    "retryDelay": 300000
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Orchestrator | Running chains when triggered |
| FileManager | Storing schedule state |
| Logger | Logging job execution |

---

## Used By

| Module | How |
|--------|-----|
| StorageSync | Scheduled syncs |
| BatchProcessor | Scheduled batch jobs |
| Reports | Scheduled report generation |

---

*Last updated: 2026-01-17*
