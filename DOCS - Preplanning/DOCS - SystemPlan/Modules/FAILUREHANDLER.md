# FailureHandler Module

> **Purpose:** Handle failures, escalate appropriately, coordinate recovery
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

FailureHandler is the "restaurant manager" - when a chef (module) has a problem, the head chef (Orchestrator) reports to the manager who decides how to fix it. Should we use the expensive emergency supplier, or wait for the normal delivery?

```
Module fails → Orchestrator catches → FailureHandler decides → Solution or Escalate
```

---

## Roles & Rules

### FailureHandler DOES:
- Classify errors into escalation levels
- Coordinate retry attempts (Level 1)
- Ask Librarian for alternatives (Level 2)
- Trigger self-healing chains (Level 3)
- Create human tickets (Level 4)
- Log all failures and resolutions
- Update job status via StatusHandler

### FailureHandler does NOT:
- Execute retries directly (returns instruction, Orchestrator executes)
- Make creative decisions (self-healing uses defined templates)
- Override budget limits (Level 4 - needs human)
- Create data outside templates (self-healing is rule-based)
- Fix auth/permission issues (always Level 4)

### Boundaries:
- Cannot approve budget increases
- Cannot create new repo types
- Cannot bypass authentication
- Cannot skip logging

---

## 4-Level Escalation System

| Level | Problem | Handler | Solution | Example |
|-------|---------|---------|----------|---------|
| 1 | Transient | Retry logic | Wait and retry | API timeout, rate limit |
| 2 | Missing data | Librarian | Find alternative | Tone not found |
| 3 | No alternative | Self-healing | Create new entry | Generate missing tone |
| 4 | Novel decision | Human | Queue for review | Budget exceeded, auth error |

### Kitchen Analogy

| Kitchen | System | Example |
|---------|--------|---------|
| "Wait 5 min, oven still heating" | Level 1: Retry | API rate limit |
| "Out of salmon, check freezer" | Level 2: Librarian | Find similar tone |
| "Order from emergency supplier" | Level 3: Self-healing | Create the tone |
| "Call the owner - menu decision" | Level 4: Human | Novel category needed |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `handleFailure(error, context)` | Main entry - classify and route | error, context | solution |
| `classifyError(error)` | Determine error level/type | error | classification |
| `attemptRetry(error, context, options)` | Level 1: Retry with backoff | error, context, options | retryResult |
| `findAlternative(error, context)` | Level 2: Ask Librarian | error, context | alternative |
| `triggerSelfHealing(error, context)` | Level 3: Create missing entry | error, context | newEntry |
| `escalateToHuman(error, context)` | Level 4: Queue for human | error, context | ticket |
| `logFailure(error, context, resolution)` | Record failure and outcome | error, context, resolution | logged |
| `checkCircuitBreaker(moduleId)` | Check if module is circuit-broken | moduleId | status |

---

## Detailed Function Specs

### handleFailure(error, context)

Main entry point - classifies error and routes to appropriate handler.

```javascript
// Input
{
  error: {
    code: "NOT_FOUND_TONE",
    message: "Tone 'formal-corporate' not found",
    module: "PromptBuilder",
    recoverable: true,
    dataType: "requiredCanFindAlternative"  // From PromptBuilder's three-tier system
  },
  context: {
    jobId: "job_20260117_143052_brandkit_abc",
    brandId: "acme",
    chainName: "brand-kit",
    currentStep: 3,
    refs: { tone: "formal-corporate" }
  }
}

// Process
1. classifyError(error) → { level: 2, type: "missing_data" }
2. StatusHandler.setStatus(jobId, "recovering", { level: 2 })
3. Route to Level 2 handler: findAlternative()
4. If solution found → return solution
5. If not → escalate to next level
6. logFailure(error, context, resolution)

// Output (solution found)
{
  resolved: true,
  level: 2,
  handler: "findAlternative",
  solution: {
    action: "substitute",
    original: { tone: "formal-corporate" },
    replacement: { tone: "business-formal" },
    reason: "Similar tone found via Librarian.recommend()"
  },
  statusUpdate: { status: "resumed", previousStatus: "recovering" }
}

// Output (escalated)
{
  resolved: false,
  level: 2,
  escalatedTo: 3,
  reason: "No similar tones found in repos",
  statusUpdate: { status: "self-healing", previousStatus: "recovering" }
}
```

### classifyError(error)

Determines which level should handle this error.

```javascript
// Input
{
  code: "API_RATE_LIMIT",
  message: "Rate limit exceeded",
  retryAfter: 30000,
  module: "AICaller"
}

// Output
{
  level: 1,
  type: "transient",
  subtype: "rate_limit",
  retryable: true,
  suggestedAction: "retry_with_backoff",
  waitTime: 30000
}
```

**Classification Rules:**

| Error Code Pattern | Level | Type | Data Tier |
|--------------------|-------|------|-----------|
| `API_TIMEOUT`, `API_RATE_LIMIT`, `API_5XX` | 1 | transient | n/a |
| `API_NETWORK`, `API_TEMPORARY` | 1 | transient | n/a |
| `NOT_FOUND_*` + `requiredCanFindAlternative` | 2 | missing_data | Tier 2 |
| `NOT_FOUND_*` + `optionalCanBeCreated` | 3 | missing_data | Tier 3 |
| `NOT_FOUND_*` + `requiredNoSubstitute` | 4 | critical_missing | Tier 1 |
| `VALIDATION_RECOVERABLE` | 2 | validation | n/a |
| `AUTH_*` | 4 | auth | n/a |
| `BUDGET_EXCEEDED` | 4 | budget | n/a |
| `SYSTEM_*` | 4 | system | n/a |

### Integration with PromptBuilder's Three-Tier Data

FailureHandler knows about PromptBuilder's data classification:

| PromptBuilder Tier | FailureHandler Level | Action |
|--------------------|---------------------|--------|
| `requiredNoSubstitute` | Level 4 (immediate) | Queue for human - brand.name, legal.* |
| `requiredCanFindAlternative` | Level 2 → 3 → 4 | Librarian → Self-heal → Human |
| `optionalCanBeCreated` | Level 3 → skip | Self-heal or skip gracefully |

```javascript
// Example: Missing brand.name (Tier 1 - requiredNoSubstitute)
{
  code: "NOT_FOUND_BRAND_NAME",
  dataType: "requiredNoSubstitute"
}
// → Immediately Level 4, no recovery attempts

// Example: Missing tone (Tier 2 - requiredCanFindAlternative)
{
  code: "NOT_FOUND_TONE",
  dataType: "requiredCanFindAlternative"
}
// → Level 2 (Librarian) → Level 3 (self-heal) → Level 4 (human)

// Example: Missing writingRules (Tier 3 - optionalCanBeCreated)
{
  code: "NOT_FOUND_WRITING_RULES",
  dataType: "optionalCanBeCreated"
}
// → Level 3 (self-heal) → skip with default
```

### attemptRetry(error, context, options)

Level 1: Handles transient errors with retry logic.

```javascript
// Input
{
  error: {
    code: "API_RATE_LIMIT",
    retryAfter: 30000,
    module: "AICaller"
  },
  context: {
    jobId: "job_20260117_143052_brandkit_abc",
    currentAttempt: 1
  },
  options: {
    maxRetries: 3,
    backoffMultiplier: 2,
    baseDelay: 1000
  }
}

// Process
1. Check circuit breaker status for module
2. Check if retries remaining
3. Calculate wait time (exponential backoff or retryAfter)
4. StatusHandler.setStatus(jobId, "waiting", { waitMs, reason: "retry" })
5. Return retry instruction to Orchestrator

// Output (should retry)
{
  action: "retry",
  waitMs: 30000,
  attempt: 2,
  maxAttempts: 3,
  reason: "Rate limit - waiting 30s before retry"
}

// Output (retries exhausted)
{
  action: "escalate",
  reason: "Max retries (3) exhausted",
  escalateTo: 2,
  totalWaitTime: 70000  // Sum of all waits
}
```

### findAlternative(error, context)

Level 2: Asks Librarian for alternatives.

```javascript
// Input
{
  error: {
    code: "NOT_FOUND_TONE",
    context: { toneId: "formal-corporate" },
    dataType: "requiredCanFindAlternative"
  },
  context: {
    brandId: "acme",
    refs: { ... },
    brandDomain: "finance"
  }
}

// Process
1. Determine what's missing (tone)
2. Get brand context for better matching
3. Librarian.recommend('tones', {
     similar_to: 'formal-corporate',
     domain: 'finance',
     style: 'professional',
     exclude: ['formal-corporate']
   })
4. If found → validate it's suitable → return substitute
5. If not → escalate to Level 3

// Output (found)
{
  action: "substitute",
  found: true,
  replacement: {
    ref: "tone",
    originalValue: "formal-corporate",
    newValue: "business-formal",
    similarity: 0.89,
    reason: "Similar professional tone for finance domain"
  },
  logged: {
    substitution: true,
    originalMissing: "formal-corporate",
    usedInstead: "business-formal"
  }
}

// Output (not found)
{
  action: "escalate",
  found: false,
  escalateTo: 3,
  reason: "No suitable alternatives found in tones repo",
  searchedRepos: ["tones"],
  searchCriteria: { similar_to: "formal-corporate", domain: "finance" }
}
```

### triggerSelfHealing(error, context)

Level 3: Creates missing entry using AI and templates.

```javascript
// Input
{
  error: {
    code: "NOT_FOUND_TONE",
    context: { toneId: "formal-corporate" },
    dataType: "requiredCanFindAlternative"  // or "optionalCanBeCreated"
  },
  context: { brandId: "acme", brandDomain: "finance" }
}

// Process
1. Check if self-healing is enabled
2. Determine entry type needed (tone)
3. Check if we have a template for creating tones
4. Gather context from brand
5. Build self-healing chain request
6. Orchestrator.runChain("self-heal-tone", {
     toneName: "formal-corporate",
     basedOn: { domain: "finance", style: "formal", industry: "corporate" },
     brandContext: { ... }
   })
7. If success → return new entry reference
8. If fail → escalate to Level 4

// Output (created)
{
  action: "created",
  success: true,
  newEntry: {
    id: "formal-corporate",
    repo: "tones",
    path: "/repos/tones/formal-corporate.json",
    createdBy: "self-healing",
    createdFor: "job_20260117_143052_brandkit_abc"
  },
  message: "New tone entry created via self-healing chain"
}

// Output (failed - no template)
{
  action: "escalate",
  success: false,
  escalateTo: 4,
  reason: "No self-healing template for 'tone' entry type",
  available_templates: ["writing-rules", "format-guidelines"]
}

// Output (failed - chain error)
{
  action: "escalate",
  success: false,
  escalateTo: 4,
  reason: "Self-healing chain failed: AI generation error",
  chainError: { ... }
}
```

### Self-Healing Templates

Available self-healing chains:

| Entry Type | Chain Name | Can Self-Heal |
|------------|------------|---------------|
| `tone` | `self-heal-tone` | Yes |
| `writing-rules` | `self-heal-writing-rules` | Yes |
| `format-guidelines` | `self-heal-format-guidelines` | Yes |
| `generic-context` | `self-heal-generic-context` | Yes |
| `voice` | `self-heal-voice` | Yes |
| `style` | `self-heal-style` | Yes |
| `brand-name` | n/a | No (requiredNoSubstitute) |
| `client-info` | n/a | No (requiredNoSubstitute) |
| `legal` | n/a | No (requiredNoSubstitute) |

### escalateToHuman(error, context)

Level 4: Queues for human decision.

```javascript
// Input
{
  error: {
    code: "BUDGET_EXCEEDED",
    message: "Monthly budget exceeded",
    currentSpend: 150.00,
    budgetLimit: 100.00
  },
  context: {
    jobId: "job_20260117_143052_brandkit_abc",
    brandId: "acme",
    chainName: "brand-kit"
  }
}

// Process
1. Create ticket with full context
2. Determine ticket priority
3. Generate suggested actions
4. Save to review queue via Archivist
5. StatusHandler.setStatus(jobId, "paused", { reason: "awaiting-human" })
6. Notification.send(ticket) if configured
7. Return ticket info

// Output
{
  action: "queued",
  ticket: {
    id: "ticket_20260117_143052_abc",
    type: "budget_exceeded",
    priority: "high",
    createdAt: "2026-01-17T14:30:52Z",

    context: {
      jobId: "job_20260117_143052_brandkit_abc",
      brandId: "acme",
      chainName: "brand-kit",
      error: { ... }
    },

    suggestedActions: [
      { action: "increase_budget", label: "Increase monthly budget to £200" },
      { action: "wait", label: "Wait until next billing period (15 days)" },
      { action: "use_cheaper", label: "Use cheaper models for this job" },
      { action: "cancel_job", label: "Cancel this job" }
    ],

    escalationPath: [
      { level: 1, attempts: 3, result: "exhausted" },
      { level: 2, result: "not_applicable" },
      { level: 3, result: "not_applicable" },
      { level: 4, result: "queued" }
    ]
  },

  jobStatus: {
    status: "paused",
    reason: "awaiting-human-decision",
    canResume: true,
    ticketId: "ticket_20260117_143052_abc"
  },

  message: "Job paused awaiting human decision"
}
```

### checkCircuitBreaker(moduleId)

Prevents cascade failures by tracking module health.

```javascript
// Input
{
  moduleId: "AICaller"
}

// Process
1. Get recent failure history for module
2. Check against thresholds
3. Return status

// Output (healthy)
{
  moduleId: "AICaller",
  status: "closed",  // closed = healthy, open = broken
  recentFailures: 2,
  threshold: 5,
  windowMs: 300000,
  message: "Module healthy"
}

// Output (circuit open)
{
  moduleId: "AICaller",
  status: "open",  // circuit broken
  recentFailures: 7,
  threshold: 5,
  windowMs: 300000,
  cooldownEnds: "2026-01-17T14:45:00Z",
  message: "Circuit breaker open - too many failures in window"
}
```

---

## Error Code Reference

### Level 1 - Transient (Retryable)

| Code | Description | Default Retries |
|------|-------------|-----------------|
| `API_TIMEOUT` | Request timed out | 3 |
| `API_RATE_LIMIT` | Provider rate limited | 3 + wait |
| `API_5XX` | Provider server error | 3 |
| `API_NETWORK` | Network connection failed | 3 |
| `API_TEMPORARY` | Temporary provider issue | 3 |

### Level 2 - Missing Data (Librarian)

| Code | Description | Recovery |
|------|-------------|----------|
| `NOT_FOUND_TONE` | Tone entry not found | Find similar |
| `NOT_FOUND_VOICE` | Voice entry not found | Find similar |
| `NOT_FOUND_STYLE` | Style entry not found | Find similar |
| `NOT_FOUND_VALUES` | Values entry not found | Find similar |
| `VALIDATION_RECOVERABLE` | Minor validation issue | Find alternative |

### Level 3 - Self-Healing

| Code | Description | Recovery |
|------|-------------|----------|
| `NO_ALTERNATIVE_FOUND` | Librarian found nothing | Create new |
| `NOT_FOUND_WRITING_RULES` | Writing rules missing | Generate |
| `NOT_FOUND_FORMAT_GUIDELINES` | Format guidelines missing | Generate |
| `NOT_FOUND_GENERIC_CONTEXT` | Generic context missing | Generate |

### Level 4 - Human Required

| Code | Description | Reason |
|------|-------------|--------|
| `NOT_FOUND_BRAND_NAME` | Brand name missing | Critical data |
| `NOT_FOUND_CLIENT_INFO` | Client info missing | Critical data |
| `NOT_FOUND_LEGAL` | Legal info missing | Critical data |
| `AUTH_INVALID` | Invalid API key | Needs credential |
| `AUTH_EXPIRED` | API key expired | Needs renewal |
| `BUDGET_EXCEEDED` | Budget limit hit | Needs approval |
| `SYSTEM_CRITICAL` | System-level error | Needs admin |
| `SELF_HEAL_FAILED` | Self-healing failed | Novel situation |

---

## Integration with StatusHandler

FailureHandler updates job status throughout recovery:

```javascript
// Status flow during recovery
job_running
  → recovering (Level 1 retry started)
  → waiting (waiting before retry)
  → recovering (Level 2 Librarian search)
  → self-healing (Level 3 chain running)
  → paused (Level 4 awaiting human)
  → resumed (recovery successful)
  → failed (all recovery failed)
```

StatusHandler calls from FailureHandler:
```javascript
// Starting recovery
StatusHandler.setStatus(jobId, "recovering", {
  level: 1,
  error: error.code
});

// Waiting for retry
StatusHandler.setStatus(jobId, "waiting", {
  waitMs: 30000,
  reason: "retry-backoff"
});

// Self-healing
StatusHandler.setStatus(jobId, "self-healing", {
  creating: "tone",
  for: "formal-corporate"
});

// Paused for human
StatusHandler.setStatus(jobId, "paused", {
  reason: "awaiting-human",
  ticketId: "ticket_xxx"
});
```

---

## Config

### module.config.json

```json
{
  "module": "FailureHandler",
  "version": "2.0.0",
  "description": "Handle failures and coordinate recovery",
  "type": "system-service",

  "functions": [
    {
      "name": "handleFailure",
      "description": "Main entry - classify and route",
      "input": ["error", "context"],
      "output": "solution"
    },
    {
      "name": "classifyError",
      "description": "Determine error level/type",
      "input": ["error"],
      "output": "classification"
    },
    {
      "name": "attemptRetry",
      "description": "Level 1: Retry with backoff",
      "input": ["error", "context", "options"],
      "output": "retryResult"
    },
    {
      "name": "findAlternative",
      "description": "Level 2: Ask Librarian",
      "input": ["error", "context"],
      "output": "alternative"
    },
    {
      "name": "triggerSelfHealing",
      "description": "Level 3: Create missing entry",
      "input": ["error", "context"],
      "output": "newEntry"
    },
    {
      "name": "escalateToHuman",
      "description": "Level 4: Queue for human",
      "input": ["error", "context"],
      "output": "ticket"
    },
    {
      "name": "logFailure",
      "description": "Record failure and outcome",
      "input": ["error", "context", "resolution"],
      "output": "logged"
    },
    {
      "name": "checkCircuitBreaker",
      "description": "Check module health status",
      "input": ["moduleId"],
      "output": "status"
    }
  ],

  "steps": [
    {
      "name": "handleWithEscalation",
      "description": "Full escalation flow",
      "sequence": ["classifyError", "attemptRetry", "findAlternative", "triggerSelfHealing", "escalateToHuman", "logFailure"]
    },
    {
      "name": "autoRecover",
      "description": "Automatic recovery (no human)",
      "sequence": ["classifyError", "attemptRetry", "findAlternative", "triggerSelfHealing"]
    }
  ],

  "config": {
    "maxRetries": 3,
    "backoffBase": 1000,
    "backoffMultiplier": 2,
    "maxBackoff": 60000,
    "selfHealingEnabled": true,
    "circuitBreakerEnabled": true
  }
}
```

### failure.rules.json

```json
{
  "classification": {
    "level1_transient": {
      "codes": ["API_TIMEOUT", "API_RATE_LIMIT", "API_5XX", "API_NETWORK", "API_TEMPORARY"],
      "action": "retry",
      "maxRetries": 3
    },
    "level2_missing_alternative": {
      "codes": ["NOT_FOUND_TONE", "NOT_FOUND_VOICE", "NOT_FOUND_STYLE", "NOT_FOUND_VALUES"],
      "dataTypes": ["requiredCanFindAlternative"],
      "action": "find_alternative"
    },
    "level3_self_heal": {
      "codes": ["NO_ALTERNATIVE_FOUND", "NOT_FOUND_WRITING_RULES", "NOT_FOUND_FORMAT_GUIDELINES"],
      "dataTypes": ["optionalCanBeCreated", "requiredCanFindAlternative"],
      "action": "self_heal",
      "condition": "selfHealingEnabled === true"
    },
    "level4_human": {
      "codes": ["NOT_FOUND_BRAND_NAME", "NOT_FOUND_CLIENT_INFO", "NOT_FOUND_LEGAL", "AUTH_*", "BUDGET_*", "SYSTEM_*", "SELF_HEAL_FAILED"],
      "dataTypes": ["requiredNoSubstitute"],
      "action": "escalate_human"
    }
  },

  "retry": {
    "maxAttempts": 3,
    "backoff": "exponential",
    "baseDelay": 1000,
    "maxDelay": 60000,
    "jitter": true
  },

  "circuitBreaker": {
    "failureThreshold": 5,
    "windowMs": 300000,
    "cooldownMs": 600000,
    "halfOpenAfter": 300000
  },

  "selfHealingTemplates": {
    "tone": "self-heal-tone",
    "voice": "self-heal-voice",
    "style": "self-heal-style",
    "writing-rules": "self-heal-writing-rules",
    "format-guidelines": "self-heal-format-guidelines",
    "generic-context": "self-heal-generic-context"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Librarian | Finding alternatives (Level 2) |
| Orchestrator | Running self-healing chains (Level 3) |
| StatusHandler | Updating job status |
| Logger | Recording failures |
| Archivist | Saving tickets |
| Notification | Alerting humans (Level 4) |

---

## Used By

| Module | How |
|--------|-----|
| Orchestrator | Receives error, calls handleFailure() |
| All modules | Report errors in standard format |

---

## Example: Complete Recovery Flow

```
1. PromptBuilder fails: "Tone 'formal-corporate' not found"
   Error: { code: "NOT_FOUND_TONE", dataType: "requiredCanFindAlternative" }

2. Orchestrator catches → calls FailureHandler.handleFailure()

3. FailureHandler.classifyError()
   → Level 2 (missing data, can find alternative)

4. StatusHandler.setStatus(jobId, "recovering", { level: 2 })

5. FailureHandler.findAlternative()
   → Librarian.recommend('tones', { similar_to: 'formal-corporate' })
   → Found: "business-formal" (similarity: 0.89)

6. Return to Orchestrator:
   {
     resolved: true,
     solution: { action: "substitute", replacement: "business-formal" }
   }

7. StatusHandler.setStatus(jobId, "running")

8. Orchestrator retries PromptBuilder with substituted tone

9. Job continues with business-formal instead of formal-corporate

10. Logger records: "Substituted formal-corporate → business-formal"
```

---

*Last updated: 2026-01-17*
