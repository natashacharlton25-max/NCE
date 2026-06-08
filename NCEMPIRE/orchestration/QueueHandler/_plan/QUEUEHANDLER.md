# QueueHandler Module

> **Purpose:** Job queue management with priority ordering and load balancing
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

QueueHandler manages the **job queue** - accepting jobs, ordering by priority, and dispatching to Orchestrator. It ensures fair scheduling, prevents overload, and handles queue persistence.

```
Job submitted → QueueHandler.enqueue() → Priority sorted → Dispatched to Orchestrator
```

**QueueHandler ensures:**
- High-priority jobs run first
- Queue doesn't overflow
- Jobs persist across restarts
- Fair scheduling between brands

---

## Roles & Rules

### QueueHandler DOES:
- Accept and queue jobs
- Order by priority
- Dispatch to Orchestrator
- Persist queue across restarts
- Enforce queue size limits
- Provide queue status

### QueueHandler does NOT:
- Execute jobs (Orchestrator does that)
- Check permissions (PolicyEngine does that)
- Track job status (StatusHandler does that)
- Handle failures (FailureHandler does that)

### Boundaries:
- Queue management only
- Cannot modify job content
- Cannot bypass priority rules
- Must respect queue limits

---

## Priority Levels

| Priority | Value | Description | Example |
|----------|-------|-------------|---------|
| `critical` | 0 | Immediate processing | System recovery |
| `high` | 1 | User-initiated, urgent | User clicked "Generate Now" |
| `normal` | 2 | Standard processing | Scheduled content |
| `low` | 3 | Background tasks | Analytics, cleanup |
| `batch` | 4 | Bulk processing | Batch content generation |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `enqueue(job)` | Add job to queue | job | queued |
| `dequeue()` | Get next job to process | - | job |
| `peek()` | View next job without removing | - | job |
| `getQueueStatus()` | Get queue status | - | status |
| `getPosition(jobId)` | Get job's position in queue | jobId | position |
| `prioritize(jobId, priority)` | Change job priority | jobId, priority | updated |
| `remove(jobId)` | Remove job from queue | jobId | removed |
| `clear()` | Clear entire queue | - | cleared |
| `getByBrand(brandId)` | Get queued jobs for brand | brandId | jobs |

---

## Detailed Function Specs

### enqueue(job)

Add a job to the queue.

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc",
  chainName: "generate-blog-post",
  brandId: "acme",
  priority: "normal",
  params: {
    topic: "AI in marketing"
  },
  submittedBy: "user_123",
  submittedAt: "2026-01-17T14:30:52Z"
}

// Output
{
  queued: true,
  jobId: "job_20260117_143052_content_abc",
  position: 5,
  estimatedWait: 120000,  // ms
  queueSize: 8
}
```

### dequeue()

Get next job to process.

```javascript
// Output
{
  job: {
    jobId: "job_20260117_143000_email_xyz",
    chainName: "generate-email",
    brandId: "globex",
    priority: "high",
    params: {...},
    queuedFor: 5000  // ms in queue
  },
  remainingInQueue: 7
}

// If queue empty
{
  job: null,
  remainingInQueue: 0
}
```

### getQueueStatus()

Get current queue status.

```javascript
// Output
{
  total: 8,
  byPriority: {
    critical: 0,
    high: 2,
    normal: 4,
    low: 1,
    batch: 1
  },
  byBrand: {
    acme: 3,
    globex: 2,
    initech: 3
  },
  processing: 2,
  estimatedClearTime: 600000,  // ms
  oldestJob: {
    jobId: "job_20260117_142000_content_old",
    queuedFor: 660000  // 11 minutes
  }
}
```

### getPosition(jobId)

Get job's position in queue.

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc"
}

// Output
{
  jobId: "job_20260117_143052_content_abc",
  position: 5,
  ahead: 4,
  estimatedWait: 120000,
  priority: "normal"
}
```

### prioritize(jobId, priority)

Change job priority (bump up or down).

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc",
  priority: "high"
}

// Output
{
  updated: true,
  jobId: "job_20260117_143052_content_abc",
  previousPriority: "normal",
  newPriority: "high",
  previousPosition: 5,
  newPosition: 2
}
```

---

## Queue Ordering

Jobs are ordered by:

1. **Priority** (primary) - Higher priority first
2. **Submission time** (secondary) - Earlier submitted first
3. **Fair share** (optional) - Prevent brand monopoly

```javascript
// Fair share example
// Without fair share: Brand A with 10 jobs blocks Brand B
// With fair share: Round-robin between brands at same priority

{
  fairShareEnabled: true,
  maxJobsPerBrandBeforeYield: 3
}
```

---

## Queue Limits

```javascript
{
  limits: {
    maxQueueSize: 100,
    maxPerBrand: 20,
    maxPerUser: 10,
    warningThreshold: 80
  }
}

// When limit reached
{
  queued: false,
  reason: "queue_full",
  queueSize: 100,
  maxSize: 100,
  suggestion: "Wait for current jobs to complete"
}
```

---

## Persistence

Queue survives restarts:

```json
// /repos/system/queue.json
{
  "lastUpdated": "2026-01-17T14:30:52Z",
  "jobs": [
    {
      "jobId": "job_20260117_143000_email_xyz",
      "chainName": "generate-email",
      "brandId": "globex",
      "priority": 1,
      "params": {...},
      "submittedAt": "2026-01-17T14:30:00Z"
    }
  ]
}
```

---

## Config

### module.config.json

```json
{
  "module": "QueueHandler",
  "version": "1.0.0",
  "description": "Job queue management",
  "type": "system-service",

  "functions": [
    {
      "name": "enqueue",
      "description": "Add job to queue",
      "input": ["job"],
      "output": "queued"
    },
    {
      "name": "dequeue",
      "description": "Get next job to process",
      "input": [],
      "output": "job"
    },
    {
      "name": "peek",
      "description": "View next job without removing",
      "input": [],
      "output": "job"
    },
    {
      "name": "getQueueStatus",
      "description": "Get queue status",
      "input": [],
      "output": "status"
    },
    {
      "name": "getPosition",
      "description": "Get job position",
      "input": ["jobId"],
      "output": "position"
    },
    {
      "name": "prioritize",
      "description": "Change job priority",
      "input": ["jobId", "priority"],
      "output": "updated"
    },
    {
      "name": "remove",
      "description": "Remove job from queue",
      "input": ["jobId"],
      "output": "removed"
    },
    {
      "name": "clear",
      "description": "Clear entire queue",
      "input": [],
      "output": "cleared"
    },
    {
      "name": "getByBrand",
      "description": "Get queued jobs for brand",
      "input": ["brandId"],
      "output": "jobs"
    }
  ],

  "config": {
    "maxQueueSize": 100,
    "maxPerBrand": 20,
    "fairShareEnabled": true,
    "persistPath": "/repos/system/queue.json",
    "pollInterval": 100
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Queue persistence |
| Logger | Logging queue operations |

---

## Used By

| Component | How |
|-----------|-----|
| Orchestrator | Job dispatch |
| PolicyEngine | Queue limit checks |
| UI | Queue display |

---

*Last updated: 2026-01-17*
