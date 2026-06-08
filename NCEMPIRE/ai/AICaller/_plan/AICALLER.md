# AICaller Module

> **Purpose:** Call AI APIs and save raw response - nothing else
> **Type:** Service Module
> **Status:** Planning

---

## Overview

AICaller is ULTRA SIMPLE. It has one job: call an AI API and save the raw response. That's it.

```
Compiled prompt → AICaller.call() → AI API → Raw response saved to outputPath
```

**AICaller DOES:**
- Make the API call
- Save raw response to specified outputPath
- Report success/failure status

**AICaller does NOT:**
- Track tokens (TokenManager does this in next chain step)
- Track costs (CostBudgetManager does this in next chain step)
- Parse responses (Parsers module does this)
- Validate responses (separate validation step)
- Handle rate limits (RateLimiter module)
- Select models (ModelManager handles this before AICaller is called)
- Retry with fallbacks (chain config handles retries, ModelManager handles fallbacks)

**Why ultra-simple:**
- Single responsibility = easy to debug
- Chain config tells other modules to pick up data in subsequent steps
- Each module can be tested independently
- Clear boundaries = no confusion about who does what

---

## Before → During → After

### BEFORE (Prerequisites)
- [ ] PolicyEngine.canRun() approved job
- [ ] Builders.createOutputPath() created output path
- [ ] PromptBuilder compiled prompt → promptPath exists
- [ ] ModelManager selected model → modelPath exists with endpoint, apiKeyRef
- [ ] Orchestrator passed: callId, jobId, promptPath, modelPath, outputPath, timeoutLevel

### DURING (Execution)
1. Read prompt from promptPath
2. Read model config from modelPath
3. Get API key from APIKeyManager using apiKeyRef
4. Make HTTP POST request to endpoint
5. Save ENTIRE raw response to outputPath
6. Return { success, callId, outputPath, httpStatus }

### AFTER (Completion)

**On SUCCESS:**
- Orchestrator receives { success: true, outputPath }
- Orchestrator tells Logger.addToJobLog() with outputPath
- Logger reads outputPath, extracts: model, provider, tokens, duration
- TokenManager runs next (reads same outputPath for budget tracking)
- CostBudgetManager runs next (reads same outputPath for cost tracking)

**On FAILURE:**
- Orchestrator receives { success: false, error }
- Orchestrator sends to FailureHandler
- FailureHandler classifies error (Level 1-4)
- FailureHandler may tell Builders.deletePath() for cleanup
- FailureHandler may request retry with different model

---

## Roles & Rules

### AICaller DOES:
- Make the API call
- Save raw response to outputPath
- Report success/failure status
- Add _meta section for traceability

### AICaller does NOT:
- Track tokens (TokenManager does this)
- Track costs (CostBudgetManager does this)
- Parse responses (Parsers does this)
- Handle rate limits (RateLimiter does this)
- Select models (ModelManager does this)
- Retry with fallbacks (FailureHandler coordinates this)
- Create paths (Builders does this)
- Log to job log (Logger does this via Orchestrator)

### Boundaries:
- Cannot modify prompt content
- Cannot choose which model to use
- Cannot decide retry strategy
- Cannot write to paths other than outputPath
- Cannot skip saving response

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `call(request)` | Make API call, save response | request | callResult |
| `getStatus(callId)` | Check call status | callId | status |

That's it. Two functions.

---

## Detailed Function Specs

### call(request)

Makes a single API call and saves raw response.

```javascript
// Input
{
  callId: "call_20260117_143052_abc",
  jobId: "job_20260117_143052_brandkit_xyz",

  // API details (from ModelManager)
  provider: "openai",
  endpoint: "https://api.openai.com/v1/chat/completions",
  apiKeyRef: "OPENAI_API_KEY",

  // Request body (from PromptBuilder)
  body: {
    model: "gpt-4-turbo",
    messages: [...],
    max_tokens: 500,
    temperature: 0.7
  },

  // Where to save response
  outputPath: "/jobs/temp/call_20260117_143052_abc_response.json",

  // Timeout level (from Orchestrator)
  timeoutLevel: 2
}

// Process
1. Get API key from APIKeyManager
2. Make HTTP POST request
3. Save ENTIRE raw response to outputPath
4. Return status

// Output - minimal, just status
{
  success: true,
  callId: "call_20260117_143052_abc",
  outputPath: "/jobs/temp/call_20260117_143052_abc_response.json",
  httpStatus: 200,
  timestamp: "2026-01-17T14:31:05Z"
}

// On failure
{
  success: false,
  callId: "call_20260117_143052_abc",
  error: {
    type: "timeout",
    httpStatus: null,
    message: "Request timed out after 180000ms"
  },
  timestamp: "2026-01-17T14:34:05Z"
}
```

### getStatus(callId)

Check if a call is still running, completed, or failed.

```javascript
// Input
{
  callId: "call_20260117_143052_abc"
}

// Output
{
  callId: "call_20260117_143052_abc",
  status: "completed",  // "running" | "completed" | "failed" | "timeout"
  startTime: "2026-01-17T14:31:00Z",
  endTime: "2026-01-17T14:31:05Z",
  outputPath: "/jobs/temp/call_20260117_143052_abc_response.json"
}
```

---

## Raw Response Format

AICaller saves the COMPLETE raw response from the AI provider. No processing.

```json
// /jobs/temp/call_20260117_143052_abc_response.json

{
  "_meta": {
    "callId": "call_20260117_143052_abc",
    "jobId": "job_20260117_143052_brandkit_xyz",
    "provider": "openai",
    "model": "gpt-4-turbo",
    "timestamp": "2026-01-17T14:31:05Z",
    "durationMs": 4523
  },

  "raw": {
    "id": "chatcmpl-abc123xyz",
    "object": "chat.completion",
    "created": 1737124265,
    "model": "gpt-4-turbo-2024-04-09",
    "choices": [
      {
        "index": 0,
        "message": {
          "role": "assistant",
          "content": "Here is the brand introduction..."
        },
        "finish_reason": "stop"
      }
    ],
    "usage": {
      "prompt_tokens": 150,
      "completion_tokens": 320,
      "total_tokens": 470
    }
  }
}
```

The `_meta` section is added by AICaller for traceability. The `raw` section is exactly what the AI provider returned.

---

## How Chain Config Works

AICaller is one step in a chain. After each step, Orchestrator handles logging via Logger.

```json
// Example chain step configuration
{
  "steps": [
    {
      "step": 1,
      "module": "PromptBuilder",
      "function": "compile",
      "input": { "templateId": "brand-intro" },
      "outputPath": "/jobs/temp/{{jobId}}_prompt.json"
    },
    {
      "step": 2,
      "module": "ModelManager",
      "function": "selectModel",
      "input": { "requirements": { "type": "text", "quality": "high" } },
      "outputPath": "/jobs/temp/{{jobId}}_model.json"
    },
    {
      "step": 3,
      "module": "AICaller",
      "function": "call",
      "input": {
        "promptPath": "/jobs/temp/{{jobId}}_prompt.json",
        "modelPath": "/jobs/temp/{{jobId}}_model.json"
      },
      "outputPath": "/jobs/temp/{{jobId}}_airesponse.json"
    },
    {
      "step": 4,
      "module": "TokenManager",
      "function": "recordUsage",
      "input": {
        "responsePath": "/jobs/temp/{{jobId}}_airesponse.json"
      }
    },
    {
      "step": 5,
      "module": "CostBudgetManager",
      "function": "recordCost",
      "input": {
        "responsePath": "/jobs/temp/{{jobId}}_airesponse.json"
      }
    },
    {
      "step": 6,
      "module": "Parsers",
      "function": "extractContent",
      "input": {
        "responsePath": "/jobs/temp/{{jobId}}_airesponse.json"
      },
      "outputPath": "/repos/content/{{brandId}}/brand-intro.md"
    }
  ]
}
```

**Logging flow (handled by Orchestrator):**
```
AICaller completes → returns { success, outputPath }
    ↓
Orchestrator receives result
    ↓
Orchestrator calls Logger.addToJobLog(jobId, {
  step: 3,
  module: "AICaller",
  outputPath: "/jobs/temp/xxx_airesponse.json",
  status: "success"
})
    ↓
Logger reads from outputPath, extracts relevant data (tokens, model, duration)
    ↓
Logger adds to consolidated job log
```

Notice: AICaller just calls and saves. Orchestrator handles logging. TokenManager/CostBudgetManager read for their own tracking.

---

## Supported Providers

AICaller supports multiple providers. Each has slightly different request/response formats, but AICaller handles the HTTP call the same way.

| Provider | Endpoint Pattern | Notes |
|----------|-----------------|-------|
| OpenAI | `api.openai.com/v1/chat/completions` | GPT models |
| Anthropic | `api.anthropic.com/v1/messages` | Claude models |
| Google | `generativelanguage.googleapis.com` | Gemini models |
| OpenRouter | `openrouter.ai/api/v1/chat/completions` | Multi-provider gateway |
| Local | `localhost:xxxx/v1/chat/completions` | Ollama, LMStudio, etc |

The request body format is prepared by PromptBuilder (which knows each provider's format).

---

## Error Types

AICaller reports errors but doesn't handle them. FailureHandler does that.

| Error Type | HTTP Status | Retryable | Notes |
|------------|-------------|-----------|-------|
| `timeout` | null | Yes | Request took too long |
| `rate_limit` | 429 | Yes | Provider rate limited |
| `server_error` | 5xx | Yes | Provider server issue |
| `auth_error` | 401/403 | No | Invalid API key |
| `bad_request` | 400 | No | Invalid request format |
| `content_filter` | 400 | No | Content blocked by provider |
| `network_error` | null | Yes | Connection failed |

Error output example:
```json
{
  "success": false,
  "callId": "call_20260117_143052_abc",
  "error": {
    "type": "rate_limit",
    "httpStatus": 429,
    "message": "Rate limit exceeded",
    "retryAfter": 60,
    "raw": { ... }  // Raw error response from provider
  }
}
```

---

## Timeout Levels

AICaller receives timeout level from Orchestrator's global config:

| Level | Duration | Typical Use |
|-------|----------|-------------|
| 1 | 2 min | Quick calls, simple prompts |
| 2 | 3 min | Standard content generation |
| 3 | 5 min | Complex generation, long content |
| 4 | 15 min | Batch processing, multiple generations |

AICaller doesn't decide the timeout - it's told what timeout to use.

---

## Config

### module.config.json

```json
{
  "module": "AICaller",
  "version": "2.0.0",
  "description": "Call AI APIs and save raw response - nothing else",
  "type": "service",

  "functions": [
    {
      "name": "call",
      "description": "Make API call and save raw response",
      "input": ["request"],
      "output": "callResult"
    },
    {
      "name": "getStatus",
      "description": "Check call status",
      "input": ["callId"],
      "output": "status"
    }
  ],

  "config": {
    "defaultTimeoutLevel": 2,
    "userAgent": "NCE-System/1.0",
    "retryOnNetworkError": false,
    "saveRawResponses": true
  },

  "providers": {
    "openai": {
      "baseUrl": "https://api.openai.com/v1",
      "apiKeyRef": "OPENAI_API_KEY"
    },
    "anthropic": {
      "baseUrl": "https://api.anthropic.com/v1",
      "apiKeyRef": "ANTHROPIC_API_KEY"
    },
    "google": {
      "baseUrl": "https://generativelanguage.googleapis.com/v1beta",
      "apiKeyRef": "GOOGLE_API_KEY"
    },
    "openrouter": {
      "baseUrl": "https://openrouter.ai/api/v1",
      "apiKeyRef": "OPENROUTER_API_KEY"
    }
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | Getting API keys |
| Writer | Saving response files |

That's it. Just two dependencies. Ultra-simple.

---

## Used By

| Module | How |
|--------|-----|
| Orchestrator | Executes as chain step |

---

## Relationship with Other AI Modules

```
                    AISystemManager (coordinator)
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ModelManager       AICaller          TokenManager
   (selects model)    (just calls)      (tracks usage)
        │                  │                  │
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
                    CostBudgetManager
                    (tracks costs)
```

- **ModelManager** runs BEFORE AICaller - selects which model to use
- **AICaller** makes the call, saves raw response
- **TokenManager** runs AFTER AICaller - reads response, tracks tokens
- **CostBudgetManager** runs AFTER AICaller - reads response, tracks costs
- **AISystemManager** coordinates these modules (future module)

---

## Example: Complete Flow

```
1. Orchestrator starts chain

2. PolicyEngine.canRun() - "Can this run?"
   → Checks permissions (brand access, module access)
   → Checks cost limits (will this exceed budget?)
   → Checks safety rules (content policies)
   → Returns: { approved: true } or { approved: false, reason: "..." }
   → If denied, return to user immediately

3. BEFORE EACH STEP: Orchestrator asks Builders.createOutputPath()
   → Builders creates path, returns path info
   → Path tracked for this job

4. PromptBuilder compiles prompt
   → Uses path from Builders
   → Saves to /jobs/temp/xxx_step1_promptbuilder.json
   → Orchestrator tells Logger: add step to job log

5. ModelManager selects model
   → Uses path from Builders
   → Saves to /jobs/temp/xxx_step2_modelmanager.json
   → Orchestrator tells Logger: add step to job log

6. AICaller.call() receives:
   - promptPath, modelPath, outputPath (ALL from Builders)
   - Makes HTTP call
   - Saves raw response → /jobs/temp/xxx_step3_aicaller.json
   - Returns { success: true, outputPath: "..." }
   → Orchestrator tells Logger: add step to job log

7. TokenManager.recordUsage() receives:
   - responsePath → reads raw response
   - Extracts token counts, calculates cost
   - Records to /repos/token-usage/...
   → Orchestrator tells Logger: add step to job log

8. CostBudgetManager.recordCost() receives:
   - responsePath → reads raw response
   - Updates brand cost tracking
   → Orchestrator tells Logger: add step to job log

9. Parsers.extractContent() receives:
   - responsePath → reads raw response
   - Extracts choices[0].message.content
   - Saves clean content → /repos/content/...
   → Orchestrator tells Logger: add step to job log

10. SuccessHandler archives job
    → Logger finalizes job log
    → Builders.reportPathIssues() checks for empty/broken paths

ON FAILURE at any step:
   → FailureHandler receives error
   → FailureHandler tells Builders.deletePath() for cleanup
   → If path error, FailureHandler asks Builders for new path
   → Builders reports issues to Logger
```

**Key points:**
- **PolicyEngine verifies "Can this run?" FIRST** (permissions, costs, safety)
- **Builders creates ALL paths BEFORE modules run**
- Each module does ONE thing
- AICaller just calls and saves (to path from Builders)
- Orchestrator coordinates logging after each step
- Logger consolidates everything into job log
- On failure, Builders cleans up paths

---

*Last updated: 2026-01-17*
