# Phase 5: AI Core

## 5.1 AI Provider System
**Priority:** 1
**Summary:** All interactions with AI APIs. Selects provider, handles failures, fallbacks.

### Workers:
- **OpenAIWorker** - GPT-4, GPT-4o, DALL-E
- **ClaudeWorker** - Anthropic Claude
- **GeminiWorker** - Google Gemini
- **GroqWorker** - Groq (fast inference)
- **OpenRouterWorker** - OpenRouter multi-provider

### Functions:
- ResponseParser, TokenCounter

---

## 5.2 Prompt System
**Priority:** 2
**Summary:** Building and managing prompts from templates.

### Workers:
- **TemplateWorker** - Loads prompt templates from files
- **BuilderWorker** - Compiles prompts with variables

### Functions:
- VariableResolver, ConstraintApplier
