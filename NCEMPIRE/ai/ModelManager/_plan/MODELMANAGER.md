# ModelManager Module

> **Purpose:** Model selection, configuration, and fallback management
> **Type:** Service Module
> **Status:** Planning

---

## Overview

ModelManager handles model selection and fallback logic. Given requirements (task type, budget, quality needs), it picks the best model. When a model fails, it provides fallback options.

```
Requirements → ModelManager → Best model for job
Primary fails → ModelManager → Fallback options
```

---

## Roles & Rules

### ModelManager DOES:
- Select best model based on requirements
- Provide model configurations for AICaller
- Return fallback options when primary fails
- Check model availability and rate limits
- Score models against task requirements
- List available models by type/capability

### ModelManager does NOT:
- Make API calls (AICaller does that)
- Track usage or costs (TokenManager does that)
- Handle failures (FailureHandler does that)
- Modify model cards (human/admin only)

### Boundaries:
- Read-only access to model-cards repo
- Cannot enable/disable models (human only)
- Cannot change model pricing (human only)
- Cannot bypass rate limits (just reports status)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `selectModel(requirements)` | Choose best model for task | requirements | modelId |
| `getConfig(modelId)` | Get full model configuration | modelId | modelConfig |
| `getFallback(modelId)` | Get fallback model for primary | modelId | fallbackId |
| `listModels(filter)` | List available models | filter | modelList |
| `checkAvailability(modelId)` | Check if model is available | modelId | availability |
| `scoreModel(modelId, requirements)` | Score model against requirements | modelId, requirements | score |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `ModelManager.selectWithFallback` | Select model with fallback chain | selectModel → getFallback |
| `ModelManager.findAlternative` | Find alternative when primary unavailable | checkAvailability → Librarian.recommend |

---

## Detailed Function Specs

### selectModel(requirements)

Chooses best model based on requirements.

```javascript
// Input
{
  type: "text",                    // text, image, audio
  task: "brand-intro",             // Specific task type
  maxCost: 0.05,                   // Budget per call
  preferSpeed: false,              // Prioritize speed?
  preferQuality: true,             // Prioritize quality?
  requiredCapabilities: ["json-mode"]  // Must-have features
}

// Process
1. Librarian.search('model-cards', { type, status: 'active' })
2. Filter by requiredCapabilities
3. Filter by maxCost
4. Score remaining models against preferences
5. Return highest scoring

// Output
{
  modelId: "gpt-4-turbo",
  score: 0.94,
  reason: "Best quality for text, supports json-mode, within budget",
  alternatives: ["gpt-4o", "claude-3-opus"]
}
```

### getConfig(modelId)

Gets full configuration for a model.

```javascript
// Input
"gpt-4-turbo"

// Process
FileManager.get('model-cards', modelId)

// Output
{
  "id": "gpt-4-turbo",
  "name": "GPT-4 Turbo",
  "provider": "openai",
  "type": "text",
  "status": "active",

  "endpoint": "https://api.openai.com/v1/chat/completions",
  "apiKeyRef": "OPENAI_API_KEY",

  "limits": {
    "contextWindow": 128000,
    "maxOutput": 4096,
    "requestsPerMinute": 500,
    "tokensPerMinute": 30000
  },

  "pricing": {
    "inputPer1k": 0.01,
    "outputPer1k": 0.03,
    "currency": "USD"
  },

  "capabilities": ["text", "json-mode", "function-calling"],
  "fallback": "gpt-4o-mini",

  "tags": ["premium", "high-quality", "openai"]
}
```

### getFallback(modelId)

Gets the configured fallback for a model.

```javascript
// Input
"gpt-4-turbo"

// Process
1. getConfig(modelId)
2. Return config.fallback

// Output
{
  fallbackId: "gpt-4o-mini",
  reason: "Configured fallback for gpt-4-turbo"
}

// If no fallback configured
{
  fallbackId: null,
  reason: "No fallback configured",
  recommendation: "Use Librarian.recommend for alternatives"
}
```

### checkAvailability(modelId)

Checks if model is currently available.

```javascript
// Input
"gpt-4-turbo"

// Process
1. getConfig(modelId)
2. Check status === 'active'
3. Check rate limits not exceeded
4. Optionally ping endpoint

// Output (available)
{
  available: true,
  modelId: "gpt-4-turbo",
  rateLimitStatus: {
    requestsRemaining: 450,
    tokensRemaining: 28000
  }
}

// Output (unavailable)
{
  available: false,
  modelId: "gpt-4-turbo",
  reason: "rate_limit_exceeded",
  retryAfter: 45000  // ms
}
```

### scoreModel(modelId, requirements)

Scores a model against specific requirements.

```javascript
// Input
{
  modelId: "gpt-4-turbo",
  requirements: {
    type: "text",
    preferQuality: true,
    preferSpeed: false,
    maxCost: 0.05
  }
}

// Process
1. getConfig(modelId)
2. Score each requirement:
   - Type match: 1.0 or 0
   - Quality: based on tags/tier
   - Speed: based on typical latency
   - Cost: within budget = 1.0, over = 0
3. Weighted average

// Output
{
  modelId: "gpt-4-turbo",
  score: 0.94,
  breakdown: {
    typeMatch: 1.0,
    qualityScore: 0.95,
    speedScore: 0.7,
    costScore: 1.0
  }
}
```

---

## Model Card Format

Stored in `/repos/model-cards/`:

```json
{
  "id": "gpt-4-turbo",
  "name": "GPT-4 Turbo",
  "provider": "openai",
  "type": "text",
  "status": "active",
  "version": "1.0",

  "endpoint": "https://api.openai.com/v1/chat/completions",
  "apiKeyRef": "OPENAI_API_KEY",

  "limits": {
    "contextWindow": 128000,
    "maxOutput": 4096,
    "requestsPerMinute": 500,
    "tokensPerMinute": 30000
  },

  "pricing": {
    "inputPer1k": 0.01,
    "outputPer1k": 0.03,
    "currency": "USD"
  },

  "capabilities": ["text", "json-mode", "function-calling", "vision"],
  "fallback": "gpt-4o-mini",

  "quality": {
    "tier": "premium",
    "bestFor": ["complex-reasoning", "brand-content", "creative"],
    "avoid": ["simple-tasks", "high-volume"]
  },

  "performance": {
    "avgLatency": 2500,
    "reliability": 0.995
  },

  "tags": ["premium", "high-quality", "openai", "text"],
  "created": "2026-01-15",
  "updated": "2026-01-17"
}
```

---

## Config

### module.config.json

```json
{
  "module": "ModelManager",
  "version": "1.0.0",
  "description": "Model selection, configuration, and fallback management",

  "functions": [
    {
      "name": "selectModel",
      "description": "Choose best model for task",
      "input": ["requirements"],
      "output": "modelId"
    },
    {
      "name": "getConfig",
      "description": "Get full model configuration",
      "input": ["modelId"],
      "output": "modelConfig"
    },
    {
      "name": "getFallback",
      "description": "Get fallback model for primary",
      "input": ["modelId"],
      "output": "fallbackId"
    },
    {
      "name": "listModels",
      "description": "List available models",
      "input": ["filter"],
      "output": "modelList"
    },
    {
      "name": "checkAvailability",
      "description": "Check if model is available",
      "input": ["modelId"],
      "output": "availability"
    },
    {
      "name": "scoreModel",
      "description": "Score model against requirements",
      "input": ["modelId", "requirements"],
      "output": "score"
    }
  ],

  "steps": [
    {
      "name": "selectWithFallback",
      "description": "Select model with fallback chain",
      "sequence": ["selectModel", "getFallback"]
    },
    {
      "name": "findAlternative",
      "description": "Find alternative when primary unavailable",
      "sequence": ["checkAvailability", "selectModel"]
    }
  ],

  "config": {
    "defaultType": "text",
    "scoreWeights": {
      "quality": 0.4,
      "speed": 0.2,
      "cost": 0.4
    }
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Getting model cards |
| Librarian | Searching/recommending models |

---

## Used By

| Module | How |
|--------|-----|
| AICaller | Getting model config for API calls |
| PromptBuilder | Applying model-specific constraints |
| FailureHandler | Finding fallback models |

---

*Last updated: 2026-01-17*
