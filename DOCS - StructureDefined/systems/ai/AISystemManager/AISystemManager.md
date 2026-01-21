# AISystemManager Module

> **Purpose:** Coordinate AI services and manage AI system state
> **Type:** AI Services Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

AISystemManager coordinates AI-related modules and manages overall AI system configuration.

---

## Notes

### Role
AISystemManager is a **Manager** (domain coordinator), not a Module. It coordinates AI services but doesn't make API calls itself.

### Coordinates (from ROLE-MATRIX)
- AICaller - makes API calls
- ModelManager - model selection and fallbacks
- TokenManager - token estimation and tracking
- CostBudgetManager - budget checking and cost tracking

### Key Responsibilities
- **Orchestrate AI requests**: Route requests through the right sequence of modules
- **Model selection**: Use ModelManager to pick optimal model for request
- **Pre-flight checks**: Validate budget, estimate tokens before calling
- **Post-call processing**: Extract tokens, track costs, log usage
- **Fallback coordination**: If primary model fails, coordinate fallback sequence
- **System health**: Monitor AI system status, provider availability

### Does NOT Do
- Make API calls directly (AICaller does that)
- Select models (ModelManager does that)
- Track tokens (TokenManager does that)
- Manage budgets (CostBudgetManager does that)
- Build prompts (PromptBuilder does that)
- Parse responses (Parsers does that)

### Data Flow
```
Caller (ThemeGenerator, etc.)
     ↓
AISystemManager.call(request)
     ↓
1. TokenManager.estimateTokens(prompt)
2. CostBudgetManager.checkBudget(brandId, estimate)
3. ModelManager.selectModel(requirements)
     ↓
4. AICaller.call(prompt, model)
     ↓
5. Parsers.parseAIResponse(response)
6. TokenManager.recordUsage(response)
7. CostBudgetManager.recordCost(jobId, cost)
     ↓
Return parsed response to caller
```

### Depends On
- AICaller - execute API calls
- ModelManager - model selection
- TokenManager - token estimation/tracking
- CostBudgetManager - budget management
- Parsers - response parsing
- FailureHandler - error recovery coordination

### Feeds Into (Called By)
- ThemeGenerator - theme generation requests
- OutlineGenerator - outline generation requests
- TemplateEngine - section content generation
- Content - various content generation
- Any module needing AI calls

### Functions (Draft)

| Function | Input | Output |
|----------|-------|--------|
| `call(request)` | AIRequest | AIResponse |
| `callWithFallback(request)` | AIRequest | AIResponse |
| `getSystemStatus()` | - | SystemStatus |
| `getProviderStatus(provider)` | provider | ProviderStatus |
| `estimateCost(request)` | AIRequest | CostEstimate |

### AIRequest Type (Draft)

```javascript
{
  prompt: string,              // compiled prompt from PromptBuilder
  brandId: string,             // for budget tracking
  jobId?: string,              // for cost attribution
  requirements?: {
    maxTokens?: number,        // output limit
    temperature?: number,      // creativity
    speed?: "fast" | "normal", // model selection hint
    quality?: "high" | "standard",
    capabilities?: string[]    // e.g., ["vision", "long-context"]
  },
  fallbackStrategy?: "auto" | "none" | string[],  // model fallback list
  timeout?: number             // ms
}
```

### AIResponse Type (Draft)

```javascript
{
  success: boolean,
  content: any,                // parsed response content
  raw: object,                 // raw provider response
  model: string,               // model actually used
  provider: string,            // provider used
  tokens: {
    input: number,
    output: number,
    total: number
  },
  cost: number,                // calculated cost
  latency: number,             // ms
  fallbackUsed?: boolean       // true if primary failed
}
```

### Error Codes (to add to ERROR-CODES.md)
- `AI_BUDGET_EXCEEDED` - request would exceed brand budget
- `AI_ALL_PROVIDERS_FAILED` - all providers/fallbacks failed
- `AI_NO_SUITABLE_MODEL` - no model meets requirements
- `AI_SYSTEM_UNAVAILABLE` - AI system not initialized

### Architecture Considerations
- **Single entry point**: All AI requests go through AISystemManager for consistent tracking
- **Request queuing**: May need queue for high-volume periods
- **Circuit breaker**: Track provider health, skip unhealthy providers
- **Cost alerts**: Notify when approaching budget limits
- **Audit trail**: Log all AI requests for debugging/billing

### Pre-Build Concerns
1. **Boundary clarity**: Manager coordinates, doesn't implement
2. **Sync vs async**: Some callers need sync, others async
3. **Streaming**: Support streaming responses for long generation
4. **Cancellation**: Allow cancelling in-progress requests

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Updated: 2026-01-20*
*Status: Placeholder*
