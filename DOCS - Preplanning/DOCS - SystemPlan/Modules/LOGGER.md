# Logger Module

> **Purpose:** System-wide logging with centralized job log consolidation
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

Logger is the single point for all system logging. Orchestrator passes outputPaths after each chain step, and Logger reads from them to consolidate into job logs. This keeps modules simple (they just save to outputPath) while Logger handles all logging.

```
Module saves output → Orchestrator gets outputPath → Logger reads and logs
```

---

## Roles & Rules

### Logger DOES:
- Receive step completions from Orchestrator (with outputPaths)
- Read from outputPaths to extract relevant log data
- Consolidate all steps into a single job log
- Provide log levels (debug, info, warn, error, fatal)
- Route logs to different outputs (console, file, alerts)
- Track job execution traces
- Make logs searchable/filterable

### Logger does NOT:
- Execute chain steps (Orchestrator does that)
- Track budgets or costs (TokenManager/CostBudgetManager do that)
- Make decisions based on log content
- Store raw AI response content (only metadata)

### Boundaries:
- Never log API keys, passwords, PII
- Never log full prompt content (only metadata)
- Never log raw large responses (only summary)
- Cannot modify output files (read-only)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `addToJobLog(jobId, stepInfo)` | Add step to job log | jobId, stepInfo | logged |
| `finalizeJobLog(jobId, status)` | Complete job log | jobId, status | finalized |
| `log(level, message, data)` | General logging | level, message, data | logged |
| `debug(message, data)` | Debug level log | message, data | logged |
| `info(message, data)` | Info level log | message, data | logged |
| `warn(message, data)` | Warning level log | message, data | logged |
| `error(message, data)` | Error level log | message, data | logged |
| `fatal(message, data)` | Fatal level log | message, data | logged |
| `setContext(context)` | Set context for subsequent logs | context | set |
| `getJobLog(jobId)` | Get complete job log | jobId | jobLog |
| `getRecent(filter)` | Get recent log entries | filter | entries |

---

## Detailed Function Specs

### addToJobLog(jobId, stepInfo)

**KEY FUNCTION** - Called by Orchestrator after each chain step. Reads from outputPath to extract log-relevant data.

```javascript
// Input (from Orchestrator)
{
  jobId: "job_20260117_143052_brandkit_abc",
  stepInfo: {
    step: 3,
    module: "AICaller",
    function: "call",
    status: "success",
    outputPath: "/jobs/temp/job_20260117_143052_airesponse.json",
    startTime: "2026-01-17T14:31:00Z",
    endTime: "2026-01-17T14:31:05Z"
  }
}

// Process
1. Read outputPath to extract log data
2. For AICaller response, extract:
   - _meta.model
   - _meta.provider
   - _meta.durationMs
   - raw.usage.prompt_tokens
   - raw.usage.completion_tokens
   - (Never log full content)
3. Add to job log

// What Logger extracts from AICaller output
{
  model: "gpt-4-turbo",
  provider: "openai",
  durationMs: 4523,
  tokens: { prompt: 165, completion: 387, total: 552 }
}

// Output
{
  logged: true,
  jobId: "job_20260117_143052_brandkit_abc",
  stepIndex: 3,
  extractedData: { model, provider, durationMs, tokens }
}
```

### What Logger Extracts Per Module

| Module | What Logger Extracts from outputPath |
|--------|--------------------------------------|
| PromptBuilder | templateId, version, tokenEstimate |
| ModelManager | selectedModel, score, alternatives |
| AICaller | model, provider, durationMs, tokens |
| TokenManager | tokensRecorded, costRecorded, budgetRemaining |
| Parsers | contentType, contentLength, outputFormat |
| Any module | step, status, duration, errors |

### finalizeJobLog(jobId, status)

Called by SuccessHandler or FailureHandler when job completes.

```javascript
// Input
{
  jobId: "job_20260117_143052_brandkit_abc",
  status: "completed"  // or "failed", "cancelled"
}

// Process
1. Add completion timestamp
2. Calculate total duration
3. Calculate total tokens/cost
4. Write final job log to archive
5. Clean up temp job log

// Output
{
  finalized: true,
  jobId: "job_20260117_143052_brandkit_abc",
  archivePath: "/repos/logs/jobs/2026-01/job_20260117_143052_brandkit_abc.json",
  summary: {
    status: "completed",
    totalDuration: 83000,
    totalTokens: 552,
    totalCost: 0.01326,
    stepsCompleted: 6,
    errors: 0
  }
}
```

### log(level, message, data)

General purpose logging (not job-specific).

```javascript
// Input
{
  level: "warn",
  message: "Budget approaching limit",
  data: {
    brandId: "acme",
    percentUsed: 89.5,
    remaining: 5.23
  }
}

// Output (formatted entry)
{
  level: "warn",
  timestamp: "2026-01-17T14:30:52.123Z",
  message: "Budget approaching limit",
  data: {
    brandId: "acme",
    percentUsed: 89.5,
    remaining: 5.23
  }
}
```

### getJobLog(jobId)

Retrieves complete job log for review.

```javascript
// Input
"job_20260117_143052_brandkit_abc"

// Output
{
  jobId: "job_20260117_143052_brandkit_abc",
  chainName: "brand-kit",
  brandId: "acme",
  status: "completed",

  timing: {
    created: "2026-01-17T14:30:52Z",
    started: "2026-01-17T14:30:53Z",
    completed: "2026-01-17T14:32:15Z",
    duration: 83000
  },

  steps: [
    {
      step: 1,
      module: "PromptBuilder",
      function: "compile",
      status: "success",
      duration: 150,
      data: { templateId: "brand-intro", version: "2.1" }
    },
    {
      step: 2,
      module: "ModelManager",
      function: "selectModel",
      status: "success",
      duration: 50,
      data: { selectedModel: "gpt-4-turbo", score: 0.94 }
    },
    {
      step: 3,
      module: "AICaller",
      function: "call",
      status: "success",
      duration: 4523,
      data: {
        model: "gpt-4-turbo",
        provider: "openai",
        tokens: { prompt: 165, completion: 387, total: 552 }
      }
    }
    // ... more steps
  ],

  totals: {
    tokens: 552,
    cost: 0.01326,
    aiCalls: 1
  },

  errors: []
}
```

---

## Job Log Flow

```
1. Orchestrator starts job
   → Logger.setContext({ jobId, chainName, brandId })
   → Logger creates temp job log

2. Each step completes
   → Orchestrator calls Logger.addToJobLog(jobId, { step, outputPath, ... })
   → Logger reads outputPath, extracts relevant data
   → Logger adds to job log

3. Job completes
   → SuccessHandler calls Logger.finalizeJobLog(jobId, "completed")
   → Logger writes final log to archive
   → Temp log cleaned up

4. Job fails
   → FailureHandler calls Logger.finalizeJobLog(jobId, "failed")
   → Logger includes error details
   → Writes to archive with error summary
```

---

## Log Levels

| Level | When to Use | Outputs To |
|-------|-------------|------------|
| `debug` | Development detail, variable values | Console (dev only) |
| `info` | Normal operations, milestones | Console, File |
| `warn` | Recoverable issues, approaching limits | Console, File, Alert queue |
| `error` | Failures, exceptions | Console, File, Alert queue |
| `fatal` | System halt conditions | Console, File, Alert queue, Notify |

---

## Log Storage

```
/repos/logs/
  /jobs/
    /2026-01/
      job_20260117_143052_brandkit_abc.json  (completed job logs)
      job_20260117_150000_content_def456.json
  /system/
    /2026-01-17/
      system.log  (general system logs)
      errors.log  (errors only)
```

---

## What to Log vs Never Log

| Always Log | Never Log |
|------------|-----------|
| Job start/end | API keys |
| Step transitions | Full prompt content |
| Errors (all details) | Passwords |
| Retries and outcomes | Raw large responses |
| Budget warnings | PII |
| Model selections | Full AI response content |
| Token counts | |
| Duration/timing | |

---

## Config

### module.config.json

```json
{
  "module": "Logger",
  "version": "2.0.0",
  "description": "System-wide logging with job log consolidation",
  "type": "system-service",

  "functions": [
    {
      "name": "addToJobLog",
      "description": "Add step to job log (reads from outputPath)",
      "input": ["jobId", "stepInfo"],
      "output": "logged"
    },
    {
      "name": "finalizeJobLog",
      "description": "Complete job log",
      "input": ["jobId", "status"],
      "output": "finalized"
    },
    {
      "name": "log",
      "description": "General logging",
      "input": ["level", "message", "data"],
      "output": "logged"
    },
    {
      "name": "debug",
      "description": "Debug level log",
      "input": ["message", "data"],
      "output": "logged"
    },
    {
      "name": "info",
      "description": "Info level log",
      "input": ["message", "data"],
      "output": "logged"
    },
    {
      "name": "warn",
      "description": "Warning level log",
      "input": ["message", "data"],
      "output": "logged"
    },
    {
      "name": "error",
      "description": "Error level log",
      "input": ["message", "data"],
      "output": "logged"
    },
    {
      "name": "fatal",
      "description": "Fatal level log",
      "input": ["message", "data"],
      "output": "logged"
    },
    {
      "name": "setContext",
      "description": "Set context for subsequent logs",
      "input": ["context"],
      "output": "set"
    },
    {
      "name": "getJobLog",
      "description": "Get complete job log",
      "input": ["jobId"],
      "output": "jobLog"
    },
    {
      "name": "getRecent",
      "description": "Get recent log entries",
      "input": ["filter"],
      "output": "entries"
    }
  ],

  "config": {
    "minLevel": "info",
    "outputs": ["console", "file"],
    "logPath": "/repos/logs/",
    "jobLogPath": "/repos/logs/jobs/",
    "maxFileSize": "10MB",
    "maxFiles": 10,
    "retentionDays": 90,
    "includeTimestamp": true,
    "includeModule": true
  },

  "extractors": {
    "AICaller": ["_meta.model", "_meta.provider", "_meta.durationMs", "raw.usage"],
    "PromptBuilder": ["templateId", "version", "tokenEstimate"],
    "ModelManager": ["modelId", "score", "alternatives"],
    "TokenManager": ["tokens", "cost", "budgetRemaining"]
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading outputPaths |
| Writer | Writing log files |
| Archivist | Archiving completed job logs |

---

## Used By

| Module | How |
|--------|-----|
| Orchestrator | Passes outputPaths after each step |
| SuccessHandler | Finalizes job log on success |
| FailureHandler | Finalizes job log on failure |
| All modules | General logging via log() |

---

*Last updated: 2026-01-17*
