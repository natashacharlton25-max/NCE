# AICaller Module

> **Purpose:** Make AI API calls to Anthropic/OpenAI
> **Type:** AI Services Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

AICaller makes API calls to AI providers. 
---

## Notes

### Provider Support

| Provider | Status | Use Case | Config |
|----------|--------|----------|--------|
| Anthropic (Claude) | Primary | Complex reasoning, long context | @anthropic-ai/sdk |
| OpenAI (GPT) | Primary | Speed, cost optimization | openai SDK |
| Google AI | Supported | Alternative fast model | REST API |
| Groq | Supported | Ultra-fast inference | REST API |
| OpenRouter | Supported | Multi-model routing | REST API |
| Ollama | Future | Local/GPU models | HTTP |

### Key Responsibilities
- **Make API calls**: Execute requests to AI providers
- **Provider routing**: Route calls based on config, load, fallback strategy
- **Rate limiting**: Respect per-provider limits, queue if needed
- **Retry logic**: Exponential backoff, distinguish transient vs permanent failures
- **Return raw response**: Pass full provider response to caller
- **Error classification**: Transient (retry) vs permanent (send to FailureHandler)

### Data Flow
- **Input**: Receives prompt from PromptBuilder
- **API Keys**: Gets credentials from APIKeyManager
- **Output**: Returns raw provider response
- **Token extraction**: Done by Parser or AISystemManager → TokenManager
- **Cost tracking**: Done by CostTracker using parsed token data

### Depends On
- APIKeyManager - provider credentials
- RateLimiter - enforce rate limits
- FailureHandler - error recovery
- Parser - token extraction
- TokenManager - token tracking

### Feeds Into (Called By)
- AISystemManager - orchestrates AI operations
- ThemeGenerator - theme generation
- OutlineGenerator - outline generation
- All downstream content generation modules

### Architecture Considerations
- **Worker pools per provider**: Separate workers/threads per model to prevent slow calls blocking fast ones
- **Queue management**: Per-provider queues for better load balancing
- **Concurrency limits**: Respect provider-specific concurrent request limits

### Pre-Build Concerns (from MODULE-SPEC-CHECKLIST.md)

**1. Dependencies to Document**
- **Depends on** (modules AICaller calls): APIKeyManager, RateLimiter, FailureHandler
- **Feeds into** (modules that call AICaller): PromptBuilder → AICaller → Parser → TokenManager
- **Downstream consumers**: AISystemManager, ThemeGenerator, OutlineGenerator, and all content generation modules
- This should be explicit in the spec

**2. Error Codes**
- AICaller needs to emit error codes (add to ERROR-CODES.md)
- Examples: `API_RATE_LIMIT_EXCEEDED`, `API_PROVIDER_TIMEOUT`, `API_INVALID_CREDENTIALS`, `API_QUOTA_EXCEEDED`, `API_MALFORMED_RESPONSE`
- These should be added to ERROR-CODES.md *before* FailureHandler can be written

**3. Schemas & Validation**
- AICaller will need Zod schemas for:
  - Provider configuration (API keys, models, rate limits)
  - Request payloads (prompt, model, parameters per provider)
  - Response validation (detect malformed responses, token counts)
  - Error responses (distinguish transient vs permanent)
- These go in `/src/schemas/AICaller.ts` *after* spec approval

**4. Prompt Ownership**
- AICaller doesn't *create* prompts—it *calls* them (PromptBuilder → AICaller)
- This boundary should be clear in the spec

**5. Concern Ownership Cross-Check**
- **Token extraction**: Parser owns this (AICaller returns raw response + raw token counts → Parser extracts structured data → TokenManager tracks)
- **Logging format**: Logger owns (AICaller references Logger spec, not duplicates)
- **Retry logic**: FailureHandler owns (AICaller emits error codes, FailureHandler decides recovery)
- **Rate limiting**: RateLimiter owns (AICaller respects it, doesn't implement it)

**6. What NOT to Include**
- Retry backoff timing (belongs in FailureHandler)
- Logging statements (belongs in Logger spec)
- Validation implementation details (belongs in Validator)
- Cost tracking formulas (belongs in CostTracker)
- Specific provider API keys/secrets (belongs in config files referenced by APIKeyManager)

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Updated: 2026-01-20*
*Status: Placeholder*
