# Orchestrator Module

> **Purpose:** Coordinates chains, routes jobs, manages execution flow
> **Type:** Core Module
> **Status:** Planning

---

## Overview

The Orchestrator is the central coordinator - the "head chef" of the system. It receives job requests, loads chain definitions, and executes steps in sequence (or parallel where defined). All chain execution flows through Orchestrator.

```
Request → Orchestrator → Load Chain → Execute Steps → Return Result
```

**Orchestrator does NOT:**
- Generate content (delegates to pipeline modules)
- Make AI calls directly (uses AICaller)
- Handle failures (delegates to FailureHandler)
- Store data (uses FileManager/Archivist)

**Orchestrator DOES:**
- Manage job lifecycle (create, run, complete)
- Load and execute chain definitions
- Route to correct modules
- Track execution state and trace
- Manage priority queue
- Coordinate parallel execution

---

## Roles & Rules

### Orchestrator DOES:
- Manage job lifecycle (create, queue, run, complete/fail)
- Load chain definitions from system.config
- Execute steps in sequence or parallel
- Route to correct modules based on chain config
- Track execution state and trace for each job
- Manage priority queue ordering
- Handle checkpoints and recovery points
- Hand off to SuccessHandler on completion
- Hand off to FailureHandler on errors
- Apply timeout levels and progressive wait loops

### Orchestrator does NOT:
- Generate content (delegates to pipeline modules)
- Make AI calls directly (uses AICaller)
- Handle failure recovery logic (FailureHandler decides solutions)
- Store data permanently (uses Archivist via SuccessHandler)
- Select which model to use (ModelManager does this)
- Track tokens or costs (TokenManager/CostBudgetManager do this)
- Create file paths (Builders does this BEFORE steps run)

### Boundaries:
- Cannot modify chain definitions at runtime
- Cannot skip PolicyEngine approval
- Cannot bypass FailureHandler for error handling
- Cannot write directly to repos (goes through Archivist)
- Must use Builders-created paths for all step outputs
- Must log every step via Logger

---

## Job Triggers

Jobs can be triggered from three sources:

| Source | Description | Example |
|--------|-------------|---------|
| **Scheduler** | Automated scheduled execution | Daily content batch at 6am |
| **User Push** | Manual single-run trigger | User clicks "Generate Now" |
| **Svelte UI** | Remote control from web app | Dashboard triggers job |

```javascript
// All triggers call the same entry point
Orchestrator.runChain(chainName, params, { trigger: "scheduler" | "user" | "ui" })
```

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `createJob(chainName, params)` | Create new job with ID and context | chainName, params | jobContext |
| `loadChain(chainName)` | Load chain definition from system.config | chainName | chainDefinition |
| `executeStep(step, context)` | Run single step, update context | step, context | updatedContext |
| `runChain(chainName, params)` | Full chain execution | chainName, params | result |
| `handleStepResult(result, context)` | Process step output, update trace | result, context | updatedContext |
| `checkCheckpoint(step)` | Check if step is a checkpoint | step | boolean |
| `saveCheckpoint(context)` | Save recovery point | context | checkpointId |
| `resumeFromCheckpoint(checkpointId)` | Resume job from checkpoint | checkpointId | context |
| `queueJob(chainName, params, priority)` | Add job to priority queue | chainName, params, priority | queuePosition |
| `getNextJob()` | Get highest priority job from queue | - | jobContext |
| `calculatePriority(params)` | Calculate job priority score | params | priorityScore |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Orchestrator.runJob` | Full job lifecycle | createJob → loadChain → runChain |
| `Orchestrator.resumeJob` | Resume from failure | loadCheckpoint → runChain (from checkpoint) |
| `Orchestrator.processQueue` | Process queued jobs | getNextJob → runJob (loop) |

---

## Detailed Function Specs

### createJob(chainName, params)

Creates a new job with unique ID and initial context.

```javascript
// Input
{
  chainName: "brand-kit",
  params: {
    brandId: "acme",
    refs: { tone: "formal-corporate" }
  },
  trigger: "user",
  priority: {
    content: 1,      // 1=high, 2=medium, 3=low
    deadline: "2026-01-18T09:00:00Z",
    tokenBudget: "normal"  // "urgent" | "normal" | "low"
  }
}

// Output
{
  jobId: "job_20260117_143052_brandkit_abc123",
  chainName: "brand-kit",
  brandId: "acme",
  created: "2026-01-17T14:30:52Z",
  status: "created",
  trigger: "user",
  priority: {
    score: 85,  // Calculated priority score
    content: 1,
    deadline: "2026-01-18T09:00:00Z",
    tokenBudget: "normal"
  },
  refs: { tone: "formal-corporate" },
  trace: [],
  tempFile: "/jobs/temp/job_20260117_143052_brandkit_abc123.json"
}
```

### loadChain(chainName)

Loads chain definition from system.config.json.

```javascript
// Input
"brand-kit"

// Output
{
  name: "brand-kit",
  description: "Generate full brand kit",
  steps: [
    "Builders.scaffoldBrand",
    "ModelManager.selectWithFallback",
    "TokenManager.validateAndTrack",
    "AICaller.callWithRetry"
  ]
}
```

### executeStep(step, context)

Runs a single step by calling the referenced module.step.

```javascript
// Input
{
  step: "TokenManager.validateAndTrack",
  context: { jobId: "...", brandId: "acme", ... }
}

// Process
1. Parse step → module: "TokenManager", stepName: "validateAndTrack"
2. Load module config
3. Call module.executeStep(stepName, context)
4. Update context with result
5. Add to trace

// Output
{
  ...context,
  tokenEstimate: 1500,
  budgetStatus: "ok",
  trace: [..., { step: "TokenManager.validateAndTrack", status: "completed" }]
}
```

### runChain(chainName, params)

Full chain execution - the main entry point.

```javascript
// Input
{
  chainName: "brand-kit",
  params: { brandId: "acme" }
}

// Process
1. createJob(chainName, params)
2. loadChain(chainName)
3. For each step in chain:
   a. If parallel block → run steps concurrently (see Parallel Execution)
   b. Else → executeStep(step, context)
   c. If checkpoint → saveCheckpoint(context)
   d. If error → throw to FailureHandler
   e. If timeout (>5 min) → treat as error, throw to FailureHandler
4. On success → hand off to SuccessHandler
5. Return final context

// Output
{
  jobId: "...",
  status: "completed",
  result: { ... },
  trace: [...]
}
```

### calculatePriority(params)

Calculates priority score for job queue ordering.

```javascript
// Input
{
  content: 1,           // 1=high, 2=medium, 3=low
  deadline: "2026-01-18T09:00:00Z",
  tokenBudget: "urgent"
}

// Process
1. Base score from content priority: high=100, medium=50, low=25
2. Add deadline urgency:
   - Due in <2h: +50
   - Due in <24h: +30
   - Due in <7d: +10
   - No deadline: +0
3. Add token budget modifier:
   - urgent: +20
   - normal: +0
   - low: -10

// Output
{
  score: 150,  // 100 (high) + 30 (<24h) + 20 (urgent)
  breakdown: {
    content: 100,
    deadline: 30,
    tokenBudget: 20
  }
}
```

---

## Chain Execution Patterns

### Sequential (Default)

Steps run one after another:

```json
{
  "name": "simple-chain",
  "steps": [
    { "module": "AICaller", "step": "callWithRetry" },
    { "module": "Parsers", "step": "convertToMarkdown" },
    { "module": "Archivist", "step": "validateAndSave" }
  ]
}
```

### Parallel (Fan-out/Fan-in)

Steps that don't depend on each other run simultaneously:

```json
{
  "name": "content-with-images",
  "steps": [
    { "module": "AICaller", "step": "callWithRetry" },
    {
      "parallel": [
        { "module": "Parsers", "step": "convertToMarkdown" },
        { "module": "ImageCollections", "step": "fetchFromUnsplash" }
      ],
      "wait": "all"
    },
    { "module": "Content", "step": "assembleContent" }
  ]
}
```

**Execution flow:**
```
Step 1: AICaller.callWithRetry
         │
         ▼ success
         │
    ┌────┴────┐  (parallel - both start simultaneously)
    ▼         ▼
 Parsers   ImageCollections
    │         │
    ▼         ▼
 markdown   images
    │         │
    └────┬────┘  (wait for ALL to complete)
         │
         ▼
Step 3: Content.assembleContent
        (has both markdown AND images in context)
```

### Wait Options

| Option | Meaning | Use Case |
|--------|---------|----------|
| `"all"` | Wait for ALL parallel steps to complete (default) | Need all outputs |
| `"any"` | Continue when FIRST one completes | Race condition, use fastest |
| `"allSettled"` | Wait for all, even if some fail | Non-critical parallel tasks |

### Context Merging

After parallel steps complete, their outputs merge into context:

```javascript
// Before parallel
context.refs = {
  aiResponse: "job_xxx/ai-response.json"
}

// After parallel (both outputs added)
context.refs = {
  aiResponse: "job_xxx/ai-response.json",
  markdown: "job_xxx/content.md",        // from Parsers
  images: "job_xxx/images.json"          // from ImageCollections
}
```

---

## Priority System

Jobs are queued by priority score. Higher score = processed first.

### Priority Components

| Component | Values | Score Impact |
|-----------|--------|--------------|
| **Content Priority** | 1=high, 2=medium, 3=low | 100 / 50 / 25 |
| **Deadline** | Time until due | +50 (<2h), +30 (<24h), +10 (<7d) |
| **Token Budget** | urgent, normal, low | +20 / 0 / -10 |

### Example Priority Calculations

```
Job A: Blog post needed for tomorrow morning
- Content: 1 (high) = 100
- Deadline: <24h = +30
- Token: normal = 0
- TOTAL: 130

Job B: Social media post about Job A's blog
- Content: 2 (medium, depends on A) = 50
- Deadline: <24h = +30
- Token: normal = 0
- TOTAL: 80

Job C: Next month's content batch
- Content: 3 (low) = 25
- Deadline: >7d = 0
- Token: low = -10
- TOTAL: 15

Queue order: A (130) → B (80) → C (15)
```

### Parent/Child Priority

When content depends on other content (e.g., social post about blog), parent content automatically gets higher priority:

```javascript
{
  chainName: "blog-with-social",
  steps: [
    { "module": "Content", "step": "generateBlog", "priority": "parent" },
    { "module": "SocialMediaPlanner", "step": "createPosts", "priority": "child" }
  ]
}
```

---

## Job State Management

### State Flow

```
created → queued → running → completed/failed
                      │
                      ├─→ completed → SuccessHandler
                      └─→ failed → FailureHandler
```

### Storage

| State | Storage | Handler |
|-------|---------|---------|
| Running | Temp file (`/jobs/temp/`) | Orchestrator |
| Completed | Jobs repo (`/repos/jobs/`) | SuccessHandler |
| Failed | Jobs repo (`/repos/jobs/failed/`) | FailureHandler |

```javascript
// During execution - temp file
/jobs/temp/job_20260117_143052_brandkit_abc123.json

// On success - SuccessHandler moves to repo
/repos/jobs/completed/2026-01/job_20260117_143052_brandkit_abc123.json

// On failure - FailureHandler moves to repo
/repos/jobs/failed/2026-01/job_20260117_143052_brandkit_abc123.json
```

### Handoff Protocol

```javascript
// On success
Orchestrator.runChain() completes
    ↓
SuccessHandler.handleSuccess(jobContext)
    ↓
- Move temp file to completed repo
- Log completion
- Trigger any post-success actions
- Clean up temp resources

// On failure (after FailureHandler exhausted options)
Orchestrator.runChain() fails
    ↓
FailureHandler.handleFailure(error, jobContext)
    ↓
- Move temp file to failed repo
- Log failure with full trace
- Notify human if needed
- Preserve state for manual recovery
```

---

## Error Handling

### Timeout Level System

Orchestrator defines **global timeout levels**. Each module picks a level - no hardcoded times in module configs.

**Why levels instead of hardcoded times:**
- Adjust all timeouts in one place (slow internet day? bump levels up)
- Modules just declare "I'm a level 2 task"
- Easier to analyse patterns across modules

#### Timeout Levels (defined in Orchestrator config)

| Level | Default Time | Typical Use |
|-------|--------------|-------------|
| 1 | 2 minutes | Quick tasks (validation, parsing) |
| 2 | 3 minutes | Standard tasks (AI calls, API requests) |
| 3 | 5 minutes | Heavy processing (PDF, image generation) |
| 4 | 15 minutes | Batch/complex operations |

```json
// orchestrator.config.json
{
  "timeoutLevels": {
    "1": 120000,
    "2": 180000,
    "3": 300000,
    "4": 900000
  }
}
```

#### Module declares its level

```json
// aicaller.config.json
{
  "timeoutLevel": 2
}

// pdfmaker.config.json
{
  "timeoutLevel": 3
}
```

### Timeout Handling Flow

When a timeout triggers, Orchestrator **grabs module status** before deciding what to do. If the module is waiting for an external response (API, network), it enters a **progressive wait loop** instead of immediately failing.

#### Progressive Wait Loop

```
Step reaches initial timeout (e.g., 3 min for Level 2)
    ↓
Orchestrator polls module: "What's your status?"
    ↓
Module responds: "paused - waiting for OpenAI"
    ↓
Status is "waiting/paused" → Enter extended wait loop:

    Loop Check 1: Wait +5 min → poll again
        ↓
        Still waiting? → Log, continue to check 2
        Success/Error? → Exit loop, proceed normally

    Loop Check 2: Wait +10 min → poll again
        ↓
        Still waiting? → Log, continue to check 3
        Success/Error? → Exit loop, proceed normally

    Loop Check 3: Wait +15 min → poll again
        ↓
        Still waiting? → NOW fail to FailureHandler
        Success/Error? → Exit loop, proceed normally
```

#### Wait Loop Timing

| Check | Additional Wait | Total from Start | If Still Waiting |
|-------|-----------------|------------------|------------------|
| Initial timeout | At level time | 3 min (Level 2) | Start loop |
| Loop 1 | +5 min | 8 min | Log, continue |
| Loop 2 | +10 min | 18 min | Log, continue |
| Loop 3 | +15 min | 33 min | **Fail** |

#### When Loop Triggers vs Immediate Fail

| Module Status | Action |
|---------------|--------|
| `paused`, `waiting` | Enter wait loop (external dependency) |
| `running` | Immediate fail (should have finished) |
| `no response` | Immediate fail (module crashed) |
| `error` | Immediate fail (module reported error) |
| `success` | No timeout, proceed |

#### Detailed Timeout Report

Each check is logged. On final fail, full report sent to FailureHandler:

```javascript
{
  type: "TIMEOUT",
  module: "AICaller",
  step: "callWithRetry",
  timeoutLevel: 2,
  initialTimeout: 180000,
  totalElapsed: 1980000,  // 33 min
  loopChecks: [
    { check: 1, elapsed: 480000, status: "paused - waiting for OpenAI" },
    { check: 2, elapsed: 1080000, status: "paused - waiting for OpenAI" },
    { check: 3, elapsed: 1980000, status: "paused - waiting for OpenAI" }
  ],
  finalStatus: "paused - waiting for OpenAI",
  jobId: "job_20260117_143052"
}
```

This rich logging helps FailureHandler and you understand:
- Was it a slow API? (paused throughout)
- Did something hang? (running too long)
- Did the module crash? (no response)

### Module Status Values

Modules can report these statuses when polled:

| Status | Meaning | Timeout Behaviour |
|--------|---------|-------------------|
| `running` | Actively processing | Continue countdown |
| `paused` | Waiting for external (API, network) | Log but continue countdown |
| `progress:50%` | Working, reports progress | Continue countdown, log progress |
| `success` | Completed | No timeout, proceed |
| `error` | Failed with error | Send to FailureHandler |
| `no response` | Module unresponsive | Treat as system error |

### Error Classification

The timeout report helps FailureHandler classify the error:

| Scenario | Likely Cause | FailureHandler Action |
|----------|--------------|----------------------|
| Status: "paused - waiting for API", retry: 2 | External API slow/down | Try different model/provider |
| Status: "running", elapsed way over | Code stuck in loop | System error, log for review |
| Status: "no response" | Module crashed | System error, restart module |
| Status: "error - rate limited" | Hit API limits | Wait and retry, or use fallback |

This rich logging helps distinguish **data errors** (bad input, missing refs) from **system errors** (API down, network issues, code bugs).

### On Step Failure

```
Step fails
    ↓
Orchestrator catches error
    ↓
Log to trace with error details
    ↓
Escalate to FailureHandler
    ↓
FailureHandler returns solution (or escalates)
    ↓
Orchestrator retries with solution
    ↓
If still failing after 4-level escalation → mark job failed
```

### Checkpoint Recovery

```
Job fails after checkpoint
    ↓
On retry: Orchestrator.resumeFromCheckpoint(checkpointId)
    ↓
Load saved context
    ↓
Continue from checkpoint step (not beginning)
```

---

## Chain Definition Format

```json
{
  "name": "brand-kit",
  "description": "Generate full brand kit",
  "steps": [
    "Builders.scaffoldBrand",
    "ModelManager.selectWithFallback",
    {
      "parallel": [
        "ContentAlignment.checkAlignment",
        "VisualBrand.generatePalette"
      ],
      "wait": "all"
    },
    {
      "step": "AICaller.callWithRetry",
      "checkpoint": true
    },
    "Archivist.validateAndSave"
  ],
  "priority": {
    "default": 2,
    "escalateOnRetry": true
  },
  "timeout": {
    "total": 600000,
    "perStep": 120000
  }
}
```

### Step Types

| Type | Format | Behavior |
|------|--------|----------|
| Simple | `"Module.step"` | Run sequentially |
| Parallel | `{ "parallel": [...], "wait": "all" }` | Run concurrently |
| Checkpoint | `{ "step": "...", "checkpoint": true }` | Save recovery point |
| Conditional | `{ "step": "...", "condition": "..." }` | Run if condition met |

---

## Adding New Functions

To add a new function to Orchestrator:

### 1. Define in module.config.json

```json
{
  "functions": [
    {
      "name": "newFunctionName",
      "description": "What it does",
      "input": ["param1", "param2"],
      "output": "outputType"
    }
  ]
}
```

### 2. Implement the function

```python
def new_function_name(param1, param2):
    """
    Docstring explaining purpose.

    Args:
        param1: Description
        param2: Description

    Returns:
        outputType: Description
    """
    # Implementation
    return result
```

### 3. Register in function map

```python
FUNCTION_MAP = {
    "createJob": create_job,
    "loadChain": load_chain,
    "newFunctionName": new_function_name,  # Add here
}
```

---

## Adding New Config Options

### 1. Add to config section

```json
{
  "config": {
    "existingOption": "value",
    "newOption": "defaultValue"
  }
}
```

### 2. Document the option

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `newOption` | string | "defaultValue" | What it controls |

### 3. Use in code

```python
config = load_module_config("Orchestrator")
new_option = config.get("newOption", "defaultValue")
```

---

## Config

### module.config.json

```json
{
  "module": "Orchestrator",
  "version": "1.0.0",
  "description": "Chain coordinator and job manager",
  "type": "core",

  "functions": [
    {
      "name": "createJob",
      "description": "Create new job with ID and context",
      "input": ["chainName", "params"],
      "output": "jobContext"
    },
    {
      "name": "loadChain",
      "description": "Load chain definition from system.config",
      "input": ["chainName"],
      "output": "chainDefinition"
    },
    {
      "name": "executeStep",
      "description": "Run single step, update context",
      "input": ["step", "context"],
      "output": "updatedContext"
    },
    {
      "name": "runChain",
      "description": "Full chain execution",
      "input": ["chainName", "params"],
      "output": "result"
    },
    {
      "name": "handleStepResult",
      "description": "Process step output, update trace",
      "input": ["result", "context"],
      "output": "updatedContext"
    },
    {
      "name": "checkCheckpoint",
      "description": "Check if step is a checkpoint",
      "input": ["step"],
      "output": "boolean"
    },
    {
      "name": "saveCheckpoint",
      "description": "Save recovery point",
      "input": ["context"],
      "output": "checkpointId"
    },
    {
      "name": "resumeFromCheckpoint",
      "description": "Resume job from checkpoint",
      "input": ["checkpointId"],
      "output": "context"
    },
    {
      "name": "queueJob",
      "description": "Add job to priority queue",
      "input": ["chainName", "params", "priority"],
      "output": "queuePosition"
    },
    {
      "name": "getNextJob",
      "description": "Get highest priority job from queue",
      "input": [],
      "output": "jobContext"
    },
    {
      "name": "calculatePriority",
      "description": "Calculate job priority score",
      "input": ["params"],
      "output": "priorityScore"
    }
  ],

  "steps": [
    {
      "name": "runJob",
      "description": "Full job lifecycle",
      "sequence": ["createJob", "loadChain", "runChain"]
    },
    {
      "name": "resumeJob",
      "description": "Resume from failure",
      "sequence": ["resumeFromCheckpoint", "runChain"]
    },
    {
      "name": "processQueue",
      "description": "Process queued jobs by priority",
      "sequence": ["getNextJob", "runJob"]
    }
  ],

  "config": {
    "maxParallelSteps": 5,
    "checkpointStorage": "/repos/checkpoints/",
    "tempJobStorage": "/jobs/temp/",
    "queueProcessingInterval": 1000,
    "statusPollInterval": 30000,

    "timeoutLevels": {
      "1": 120000,
      "2": 180000,
      "3": 300000,
      "4": 900000
    },

    "timeoutLoop": {
      "enabled": true,
      "intervals": [300000, 600000, 900000],
      "maxChecks": 3,
      "triggerOnStatus": ["paused", "waiting"]
    },

    "priority": {
      "contentWeights": { "1": 100, "2": 50, "3": 25 },
      "deadlineThresholds": {
        "2h": 50,
        "24h": 30,
        "7d": 10
      },
      "tokenBudgetModifiers": {
        "urgent": 20,
        "normal": 0,
        "low": -10
      }
    }
  }
}
```

---

## Data Requirements

### From Repos

| Repo | Data Needed | Purpose |
|------|-------------|---------|
| `/repos/config/system.config.json` | Chain definitions | Load chains to execute |
| `/repos/checkpoints/` | Saved job states | Resume from failure |
| `/repos/jobs/` | Completed/failed jobs | History and recovery |

### From Other Modules

| Module | Data | When |
|--------|------|------|
| Scheduler | Job triggers | Scheduled execution |
| FailureHandler | Recovery solutions | On step failure |
| SuccessHandler | Completion confirmation | On job success |

---

## Validation

### Input Validation

```javascript
// createJob validation
{
  chainName: { required: true, type: "string", exists: "system.config.chains" },
  params: { required: true, type: "object" },
  params.brandId: { required: false, type: "string" },
  priority: { required: false, type: "object" }
}

// runChain validation
{
  chainName: { required: true, type: "string" },
  params: { required: true, type: "object" }
}
```

### Output Validation

```javascript
// Job context must have
{
  jobId: { required: true, format: "job_YYYYMMDD_HHMMSS_chain_hash" },
  status: { required: true, enum: ["created", "queued", "running", "completed", "failed"] },
  trace: { required: true, type: "array" }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| system.config.json | Chain definitions |
| All modules | Step execution |
| FailureHandler | Error escalation |
| SuccessHandler | Completion handling |
| Logger | Execution logging |
| FileManager | Checkpoint storage |

---

## Used By

| Module | How |
|--------|-----|
| Scheduler | Scheduled job triggers |
| Svelte UI | Remote job triggers |
| FailureHandler | Retry with solutions |

---

## Future Considerations

### Sub-jobs (Not Implemented)

Could allow modules to spawn sub-jobs for parallel work. Trade-offs:
- **Pro:** Would save time for complex workflows
- **Con:** Takes responsibility from Orchestrator, harder to track
- **Decision:** Defer until needed, keep Orchestrator as single coordinator for now

### Multi-user Support (Not Implemented)

Currently single-user system. Future considerations:
- User permissions per chain
- Resource allocation per user
- Queue priority per user
- Concurrent job limits per user

---

*Last updated: 2026-01-17*
