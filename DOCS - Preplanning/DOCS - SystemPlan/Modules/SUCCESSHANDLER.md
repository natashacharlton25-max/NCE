# SuccessHandler Module

> **Purpose:** Handle successful job completion, cleanup, and post-success actions
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

SuccessHandler is the counterpart to FailureHandler. When a job completes successfully, Orchestrator hands off to SuccessHandler for cleanup, archiving, and triggering any post-success actions. Think of it as the "closing shift" - cleaning up after a successful service.

```
Job completes → Orchestrator → SuccessHandler → Archive + Cleanup + Notify
```

**SuccessHandler does:**
- Move temp files to permanent storage
- Archive job context and trace
- Trigger post-success actions (notifications, follow-up jobs)
- Clean up temporary resources
- Log completion metrics

**SuccessHandler does NOT:**
- Execute the job (Orchestrator does that)
- Make content decisions
- Retry anything (job already succeeded)

---

## Roles & Rules

### SuccessHandler DOES:
- Receive completed jobs from Orchestrator
- Move temp files to permanent archive storage
- Archive job context with full trace
- Trigger post-success actions (notifications, follow-up jobs)
- Clean up temporary resources for the job
- Log completion metrics
- Schedule follow-up jobs when configured

### SuccessHandler does NOT:
- Execute the job (Orchestrator does that)
- Make content decisions
- Retry anything (job already succeeded)
- Decide if job succeeded (Orchestrator determines this)
- Handle failures (FailureHandler does that)
- Write directly to repos (uses Archivist)

### Boundaries:
- Only processes jobs marked as completed
- Cannot modify job results
- Cannot trigger chains directly (uses Scheduler for follow-ups)
- Must archive before cleanup (preserve job history)
- If archiving fails, preserve temp file as backup

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `handleSuccess(jobContext)` | Main entry - process successful job | jobContext | successReport |
| `archiveJob(jobContext)` | Move job to completed archive | jobContext | archivePath |
| `cleanupTemp(jobId)` | Remove temporary files | jobId | cleaned |
| `triggerPostActions(jobContext)` | Run post-success actions | jobContext | actionsTriggered |
| `notifyCompletion(jobContext)` | Send completion notification | jobContext | notified |
| `logMetrics(jobContext)` | Log success metrics | jobContext | logged |
| `scheduleFollowUp(jobContext, followUpConfig)` | Schedule follow-up jobs | jobContext, config | scheduled |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `SuccessHandler.processSuccess` | Full success handling | handleSuccess → archiveJob → cleanupTemp → triggerPostActions |
| `SuccessHandler.quickArchive` | Archive only, no actions | archiveJob → cleanupTemp |

---

## Detailed Function Specs

### handleSuccess(jobContext)

Main entry point when a job completes successfully.

```javascript
// Input
{
  jobId: "job_20260117_143052_brandkit_abc123",
  chainName: "brand-kit",
  status: "completed",
  result: {
    brandId: "acme",
    outputs: {
      brandKit: "/repos/brands/acme/brand-kit.json",
      logo: "/repos/brands/acme/logo.svg",
      palette: "/repos/brands/acme/palette.json"
    }
  },
  trace: [...],
  tempFile: "/jobs/temp/job_20260117_143052_brandkit_abc123.json",
  trigger: "user",
  startTime: "2026-01-17T14:30:52Z",
  endTime: "2026-01-17T14:32:15Z"
}

// Process
1. Validate job is actually completed
2. Archive job to permanent storage
3. Clean up temp files
4. Log success metrics
5. Trigger post-success actions
6. Send completion notification (if configured)

// Output
{
  success: true,
  jobId: "job_20260117_143052_brandkit_abc123",
  archivePath: "/repos/jobs/completed/2026-01/job_20260117_143052_brandkit_abc123.json",
  metrics: {
    duration: 83000,  // ms
    stepsCompleted: 5,
    tokensUsed: 2500,
    cost: 0.05
  },
  postActions: {
    triggered: ["notify-ui", "schedule-social-posts"],
    skipped: []
  },
  cleanedUp: [
    "/jobs/temp/job_20260117_143052_brandkit_abc123.json",
    "/jobs/temp/job_20260117_143052_brandkit_abc123_checkpoint.json"
  ]
}
```

### archiveJob(jobContext)

Moves job from temp to permanent archive.

```javascript
// Input
{
  jobId: "job_20260117_143052_brandkit_abc123",
  tempFile: "/jobs/temp/job_20260117_143052_brandkit_abc123.json",
  ...jobContext
}

// Process
1. Determine archive path based on date
2. Enrich job context with completion metadata
3. Write to archive location via Archivist
4. Verify write success

// Output
{
  archived: true,
  from: "/jobs/temp/job_20260117_143052_brandkit_abc123.json",
  to: "/repos/jobs/completed/2026-01/job_20260117_143052_brandkit_abc123.json",
  metadata: {
    completedAt: "2026-01-17T14:32:15Z",
    duration: 83000,
    chainName: "brand-kit"
  }
}
```

### cleanupTemp(jobId)

Removes all temporary files for a job.

```javascript
// Input
{
  jobId: "job_20260117_143052_brandkit_abc123"
}

// Process
1. Find all temp files matching job ID pattern
2. Delete each file
3. Verify cleanup

// Output
{
  cleaned: true,
  files: [
    "/jobs/temp/job_20260117_143052_brandkit_abc123.json",
    "/jobs/temp/job_20260117_143052_brandkit_abc123_checkpoint.json",
    "/jobs/temp/job_20260117_143052_brandkit_abc123_draft.json"
  ],
  errors: []
}
```

### triggerPostActions(jobContext)

Runs any configured post-success actions.

```javascript
// Input
{
  jobContext: { ... },
  chainConfig: {
    postSuccess: [
      { action: "notify-ui", params: {} },
      { action: "schedule-follow-up", params: { chain: "social-posts", delay: "1h" } }
    ]
  }
}

// Process
1. Load post-success actions from chain config
2. For each action:
   - Validate action is configured
   - Execute action with job context
   - Log result

// Output
{
  triggered: [
    { action: "notify-ui", status: "success" },
    { action: "schedule-follow-up", status: "success", scheduledJobId: "job_xxx" }
  ],
  skipped: [],
  failed: []
}
```

### notifyCompletion(jobContext)

Sends completion notification via Notification module.

```javascript
// Input
{
  jobContext: { ... },
  notifyConfig: {
    channels: ["ui", "email"],
    summary: true
  }
}

// Process
1. Build notification message
2. Send via Notification module
3. Include job summary and outputs

// Output
{
  notified: true,
  channels: ["ui", "email"],
  message: {
    title: "Job Completed: brand-kit",
    summary: "Brand kit generated for Acme Corp",
    outputs: 3,
    duration: "1m 23s"
  }
}
```

### logMetrics(jobContext)

Logs success metrics for analytics.

```javascript
// Input
{
  jobContext: { ... }
}

// Process
1. Calculate metrics from job trace
2. Log to Logger module
3. Update running totals if configured

// Output
{
  logged: true,
  metrics: {
    jobId: "job_xxx",
    chainName: "brand-kit",
    duration: 83000,
    stepsCompleted: 5,
    stepsSkipped: 0,
    tokensUsed: 2500,
    estimatedCost: 0.05,
    trigger: "user",
    completedAt: "2026-01-17T14:32:15Z"
  }
}
```

### scheduleFollowUp(jobContext, followUpConfig)

Schedules follow-up jobs based on completed job.

```javascript
// Input
{
  jobContext: {
    jobId: "job_xxx",
    chainName: "brand-kit",
    result: { brandId: "acme" }
  },
  followUpConfig: {
    chain: "social-media-batch",
    delay: "1h",
    params: {
      inherit: ["brandId"],
      additional: { postCount: 5 }
    }
  }
}

// Process
1. Build follow-up job params (inherit from completed job)
2. Calculate scheduled time
3. Submit to Scheduler

// Output
{
  scheduled: true,
  followUpJob: {
    chainName: "social-media-batch",
    params: { brandId: "acme", postCount: 5 },
    scheduledFor: "2026-01-17T15:32:15Z",
    parentJobId: "job_xxx"
  }
}
```

---

## Post-Success Actions

### Built-in Actions

| Action | Description | Params |
|--------|-------------|--------|
| `notify-ui` | Update UI with completion | - |
| `notify-email` | Send email notification | `to`, `template` |
| `schedule-follow-up` | Schedule dependent job | `chain`, `delay`, `params` |
| `trigger-chain` | Immediately run another chain | `chain`, `params` |
| `update-status` | Update external status | `endpoint`, `payload` |

### Chain Config Example

```json
{
  "name": "brand-kit",
  "steps": [...],
  "postSuccess": [
    {
      "action": "notify-ui"
    },
    {
      "action": "schedule-follow-up",
      "params": {
        "chain": "social-media-batch",
        "delay": "1h",
        "inheritParams": ["brandId"]
      }
    },
    {
      "action": "notify-email",
      "params": {
        "template": "job-complete",
        "includeOutputs": true
      }
    }
  ]
}
```

---

## Archive Structure

```
/repos/jobs/
  /completed/
    /2026-01/
      job_20260117_143052_brandkit_abc123.json
      job_20260117_150000_content_def456.json
    /2026-02/
      ...
```

### Archived Job Format

```json
{
  "jobId": "job_20260117_143052_brandkit_abc123",
  "chainName": "brand-kit",
  "status": "completed",
  "trigger": "user",

  "timing": {
    "created": "2026-01-17T14:30:52Z",
    "started": "2026-01-17T14:30:53Z",
    "completed": "2026-01-17T14:32:15Z",
    "duration": 83000
  },

  "params": {
    "brandId": "acme"
  },

  "result": {
    "outputs": {
      "brandKit": "/repos/brands/acme/brand-kit.json"
    }
  },

  "metrics": {
    "stepsCompleted": 5,
    "tokensUsed": 2500,
    "estimatedCost": 0.05
  },

  "trace": [
    { "step": "Builders.scaffoldBrand", "status": "completed", "duration": 150 },
    { "step": "AICaller.callWithRetry", "status": "completed", "duration": 45000 }
  ]
}
```

---

## Error Handling

SuccessHandler itself can fail (e.g., can't write to archive). Handle gracefully:

```javascript
// If archiving fails
try {
  await archiveJob(jobContext);
} catch (error) {
  // Log error but don't fail the whole success handling
  Logger.error("Archive failed", { jobId, error });

  // Keep temp file as backup
  // Mark for manual intervention
  await markForManualArchive(jobContext);
}

// If cleanup fails
try {
  await cleanupTemp(jobId);
} catch (error) {
  // Log but continue - cleanup can be retried later
  Logger.warn("Cleanup failed", { jobId, error });
}
```

---

## Config

### module.config.json

```json
{
  "module": "SuccessHandler",
  "version": "1.0.0",
  "description": "Handle successful job completion",
  "type": "system-service",

  "functions": [
    {
      "name": "handleSuccess",
      "description": "Main entry - process successful job",
      "input": ["jobContext"],
      "output": "successReport"
    },
    {
      "name": "archiveJob",
      "description": "Move job to completed archive",
      "input": ["jobContext"],
      "output": "archivePath"
    },
    {
      "name": "cleanupTemp",
      "description": "Remove temporary files",
      "input": ["jobId"],
      "output": "cleaned"
    },
    {
      "name": "triggerPostActions",
      "description": "Run post-success actions",
      "input": ["jobContext"],
      "output": "actionsTriggered"
    },
    {
      "name": "notifyCompletion",
      "description": "Send completion notification",
      "input": ["jobContext"],
      "output": "notified"
    },
    {
      "name": "logMetrics",
      "description": "Log success metrics",
      "input": ["jobContext"],
      "output": "logged"
    },
    {
      "name": "scheduleFollowUp",
      "description": "Schedule follow-up jobs",
      "input": ["jobContext", "followUpConfig"],
      "output": "scheduled"
    }
  ],

  "steps": [
    {
      "name": "processSuccess",
      "description": "Full success handling",
      "sequence": ["handleSuccess", "archiveJob", "cleanupTemp", "triggerPostActions"]
    },
    {
      "name": "quickArchive",
      "description": "Archive only, no actions",
      "sequence": ["archiveJob", "cleanupTemp"]
    }
  ],

  "config": {
    "archivePath": "/repos/jobs/completed/",
    "tempPath": "/jobs/temp/",
    "retentionDays": 90,
    "defaultNotify": ["ui"],
    "metricsEnabled": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Archivist | Writing to job archive |
| FileManager | Reading/deleting temp files |
| Logger | Logging metrics |
| Notification | Completion notifications |
| Scheduler | Follow-up job scheduling |

---

## Used By

| Module | How |
|--------|-----|
| Orchestrator | Hands off completed jobs |

---

*Last updated: 2026-01-17*
