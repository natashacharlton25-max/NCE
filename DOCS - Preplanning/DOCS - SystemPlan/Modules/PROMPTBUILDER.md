# PromptBuilder Module

> **Purpose:** Compile prompts from templates, refs, and brand context
> **Type:** Service Module
> **Status:** Planning

---

## Overview

PromptBuilder is a compiler. It receives references (IDs/paths), fetches the actual data from repos, resolves variables, applies constraints, and outputs ready-to-send prompt JSON. It never stores data - only transforms it.

```
Refs (IDs) → Fetch from repos → Resolve variables → Apply constraints → Compiled prompt JSON
```

**PromptBuilder does:**
- Fetch template and context data via FileManager
- Resolve template variables (`{{brand.name}}`)
- Handle conditionals (`{{#if hasLogo}}...{{/if}}`)
- Include reusable partials (`{{> writing-rules}}`)
- Validate token count before output
- Cache frequently used data (brand voice, etc.)
- Output universal format (AICaller converts for specific AI)

**PromptBuilder does NOT:**
- Store data (just transforms)
- Know which AI will run the prompt
- Make AI calls (AICaller does that)

---

## Roles & Rules

### PromptBuilder DOES:
- Fetch template and context data via FileManager
- Resolve template variables (`{{brand.name}}`)
- Handle conditionals (`{{#if hasLogo}}...{{/if}}`)
- Include reusable partials (`{{> writing-rules}}`)
- Validate token count before output
- Cache frequently used data (brand voice, etc.)
- Output universal format (AICaller converts for specific AI)
- Classify missing data into three types
- Request alternatives from Librarian for substitutable refs

### PromptBuilder does NOT:
- Store data (just transforms)
- Know which AI will run the prompt
- Make AI calls (AICaller does that)
- Choose which model to use (ModelManager does this)
- Create output paths (Builders does this)
- Retry on failure (FailureHandler decides this)

### Boundaries:
- Cannot write to repos (read-only access via FileManager)
- Cannot modify source data (returns new compiled data)
- Cannot bypass required refs (auto-fail if missing "no substitute" refs)
- Must validate tokens before output
- Cache is batch-scoped only (cleared after batch ends)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `loadTemplate(promptId)` | Fetch prompt template from repos | promptId | template |
| `loadContext(refs, brandId)` | Fetch tone, brand, values from refs | refs, brandId | context |
| `loadPartial(partialId)` | Fetch reusable snippet | partialId | partial |
| `resolveVariables(template, context)` | Replace `{{brand.name}}`, `{{tone.style}}` etc. | template, context | resolvedTemplate |
| `resolveConditionals(template, context)` | Process `{{#if}}...{{/if}}` blocks | template, context | processedTemplate |
| `resolvePartials(template)` | Include `{{> partial}}` snippets | template | expandedTemplate |
| `applyConstraints(prompt, limits)` | Apply token limits, safety rules | prompt, limits | constrainedPrompt |
| `validateTokens(prompt, modelLimits)` | Check prompt fits model context | prompt, modelLimits | validation |
| `addExamples(prompt, examples)` | Add few-shot examples to prompt | prompt, examples | promptWithExamples |
| `compile(refs, brandId)` | Full flow: load → resolve → constrain → output | refs, brandId | compiledPrompt |
| `getCached(key)` | Get from cache if exists | key | cached or null |
| `setCache(key, value, ttl)` | Store in cache | key, value, ttl | success |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `PromptBuilder.compile` | Full compilation flow | loadTemplate → loadContext → resolvePartials → resolveConditionals → resolveVariables → addExamples → applyConstraints → validateTokens |
| `PromptBuilder.compileWithFallback` | Compile with Librarian fallback on missing refs | compile (with error handling → Librarian.recommend) |
| `PromptBuilder.compileWithCache` | Compile using cache for repeated data | getCached → compile → setCache |

---

## Detailed Function Specs

### loadTemplate(promptId)

Fetches prompt template from `/repos/prompts/`. Supports version requests.

```javascript
// Input - latest version
"brand-intro"

// Input - specific version
"brand-intro@2.0"

// Process
FileManager.get('prompts', 'brand-intro')

// Output
{
  "id": "brand-intro",
  "version": "2.1",
  "status": "active",
  "name": "Brand Introduction",
  "system": "You are a brand strategist. Write in {{tone.style}} voice. {{> writing-rules}}",
  "user": "Create an introduction for {{brand.name}}, a {{brand.industry}} company.",
  "examples": [
    {
      "user": "Create an introduction for TechCorp, a software company.",
      "assistant": "TechCorp stands at the forefront of software innovation..."
    }
  ],
  "constraints": {
    "maxTokens": 500,
    "temperature": 0.7
  },
  "requiredRefs": ["tone", "brand"],
  "optionalRefs": ["values"]
}
```

### loadContext(refs, brandId)

Fetches all referenced data needed for variable resolution. Uses cache for repeated calls.

```javascript
// Input
{
  refs: { tone: "formal-corporate", values: "innovation-focused" },
  brandId: "acme"
}

// Process
1. Check cache for each ref
2. Fetch missing from FileManager
3. Cache fetched data

// Output
{
  tone: { style: "formal, professional", vocabulary: [...] },
  values: { primary: "Innovation", secondary: [...] },
  brand: { name: "Acme Corp", industry: "technology", ... }
}
```

### resolveVariables(template, context)

Replaces template variables with actual values.

```javascript
// Input
{
  template: {
    system: "Write in {{tone.style}} voice.",
    user: "Create intro for {{brand.name}}, a {{brand.industry}} company."
  },
  context: {
    tone: { style: "formal, professional" },
    brand: { name: "Acme Corp", industry: "technology" }
  }
}

// Output
{
  system: "Write in formal, professional voice.",
  user: "Create intro for Acme Corp, a technology company."
}
```

**Variable syntax:**
- `{{brand.name}}` - nested object access
- `{{tone.style}}` - from tone context
- `{{values.primary}}` - from values context

### resolveConditionals(template, context)

Processes if/then logic in templates.

```javascript
// Input
{
  template: "{{#if hasLogo}}Include the logo description: {{brand.logoDescription}}{{/if}}",
  context: {
    hasLogo: true,
    brand: { logoDescription: "A blue mountain peak" }
  }
}

// Output
"Include the logo description: A blue mountain peak"

// If hasLogo was false:
"" (empty string - section removed)
```

**Conditional syntax:**
- `{{#if variable}}...{{/if}}` - include if truthy
- `{{#if variable}}...{{else}}...{{/if}}` - if/else
- `{{#unless variable}}...{{/unless}}` - include if falsy

### resolvePartials(template)

Includes reusable snippets from global store.

```javascript
// Template contains
"{{> writing-rules}}"

// Process
1. loadPartial('writing-rules')
2. Returns: "Always write in active voice. Keep sentences under 20 words..."
3. Replace {{> writing-rules}} with content

// Partials stored in
/repos/partials/writing-rules.json
/repos/partials/brand-voice.json
/repos/partials/formatting-rules.json
```

### addExamples(prompt, examples)

Adds few-shot examples to improve AI output quality.

```javascript
// Input
{
  prompt: { system: "...", user: "..." },
  examples: [
    {
      user: "Create intro for TechCorp...",
      assistant: "TechCorp stands at the forefront..."
    },
    {
      user: "Create intro for BadExample...",
      assistant: "BAD - too long and unfocused...",
      isNegative: true
    }
  ]
}

// Output
{
  messages: [
    { role: "system", content: "..." },
    { role: "user", content: "Create intro for TechCorp..." },
    { role: "assistant", content: "TechCorp stands at the forefront..." },
    { role: "user", content: "Now, Create intro for Acme Corp..." }
  ]
}
```

### validateTokens(prompt, modelLimits)

Checks prompt fits within model context limits.

```javascript
// Input
{
  prompt: { messages: [...], max_tokens: 500 },
  modelLimits: { contextWindow: 8000, maxOutput: 4000 }
}

// Process
1. Estimate prompt tokens (via TokenManager)
2. Check: promptTokens + max_tokens < contextWindow

// Output - Success
{
  valid: true,
  promptTokens: 1500,
  responseTokens: 500,
  totalTokens: 2000,
  headroom: 6000
}

// Output - Failure
{
  valid: false,
  promptTokens: 7800,
  responseTokens: 500,
  totalTokens: 8300,
  overBy: 300,
  error: "Prompt exceeds model context by 300 tokens"
}
```

**On validation failure:** Send to FailureHandler with error details. FailureHandler may:
- Suggest shorter template version
- Reduce max_tokens
- Suggest different model with larger context

### compile(refs, brandId)

Full compilation flow - the main entry point.

```javascript
// Input
{
  refs: {
    prompt: "brand-intro",  // or "brand-intro@2.0" for specific version
    tone: "formal-corporate",
    model: "gpt-4-turbo"
  },
  brandId: "acme"
}

// Process
1. loadTemplate(refs.prompt)
2. loadContext(refs, brandId) - uses cache
3. resolvePartials(template)
4. resolveConditionals(template, context)
5. resolveVariables(template, context)
6. addExamples(resolved, template.examples)
7. applyConstraints(resolved, template.constraints)
8. validateTokens(result, modelLimits)

// Output
{
  model: "gpt-4-turbo",
  messages: [
    { role: "system", content: "You are a brand strategist. Write in formal, professional voice. Always write in active voice..." },
    { role: "user", content: "Create intro for TechCorp, a software company." },
    { role: "assistant", content: "TechCorp stands at the forefront..." },
    { role: "user", content: "Create an introduction for Acme Corp, a technology company." }
  ],
  max_tokens: 500,
  temperature: 0.7,
  metadata: {
    templateId: "brand-intro",
    templateVersion: "2.1",
    brandId: "acme",
    compiledAt: "2026-01-17T14:30:52Z"
  }
}
```

---

## Data Types

### Missing Data Handling

When data is missing, PromptBuilder classifies it into THREE types:

| Type | Description | If Missing | Example |
|------|-------------|------------|---------|
| **Required - No Substitute** | Core identifiers, legal data | Auto-fail immediately | brand.name, client.id |
| **Required - Can Find Alternative** | Style/voice data that can be similar | Ask Librarian for similar | tone, voice, style |
| **Optional - Can Be Created** | Generic context, rules | FailureHandler self-heals | writingRules, formatGuidelines |

### Missing Data Flow

```
Data missing during compile
    ↓
Check data type classification
    ↓
├─ "Required - No Substitute"
│       ↓
│   Auto-fail to FailureHandler
│   Error: "brand.name is required and cannot be substituted"
│   → Escalate to human
│
├─ "Required - Can Find Alternative"
│       ↓
│   Ask Librarian: "find tone similar to 'formal-corporate'"
│   → Librarian returns alternative
│   → Retry with substitute
│   → If no alternative found, escalate to human
│
└─ "Optional - Can Be Created"
        ↓
    FailureHandler.selfHeal()
    → Generate missing data via AI
    → Save to repo for future use
    → Retry compile
```

### Data Classification

```json
{
  "dataTypes": {
    "requiredNoSubstitute": [
      "brand.name",
      "brand.id",
      "client.name",
      "client.id",
      "legal.*"
    ],
    "requiredCanFindAlternative": [
      "tone.*",
      "voice.*",
      "style.*",
      "values.*"
    ],
    "optionalCanBeCreated": [
      "writingRules.*",
      "formatGuidelines.*",
      "genericContext.*"
    ]
  }
}
```

### Example Scenarios

**Scenario 1: Brand name missing (No Substitute)**
```
Compile needs brand.name but ref "acme" not found
    ↓
Classification: requiredNoSubstitute
    ↓
Auto-fail: "Cannot proceed without brand.name - no substitutes allowed"
    ↓
Escalate to human: "Brand 'acme' not found in repos"
```

**Scenario 2: Tone missing (Can Find Alternative)**
```
Compile needs tone but "formal-corporate" not found
    ↓
Classification: requiredCanFindAlternative
    ↓
Ask Librarian: recommend('tones', { similar_to: 'formal-corporate' })
    ↓
Librarian returns: { id: 'business-professional', similarity: 0.85 }
    ↓
Retry compile with tone = 'business-professional'
```

**Scenario 3: Writing rules missing (Can Be Created)**
```
Compile needs writingRules but none specified
    ↓
Classification: optionalCanBeCreated
    ↓
FailureHandler.selfHeal():
  - Generate generic writing rules via AI
  - Save to /repos/partials/writing-rules-generated.json
    ↓
Retry compile with new partial
```

---

## Template Format

```json
{
  "id": "brand-intro",
  "version": "2.1",
  "status": "active",
  "name": "Brand Introduction",
  "description": "Generate brand introduction paragraph",
  "created": "2026-01-10",
  "updated": "2026-01-17",

  "system": "You are a brand strategist. Write in {{tone.style}} voice. {{> writing-rules}} {{#if values}}Emphasize these values: {{values.list}}.{{/if}}",

  "user": "Create an introduction for {{brand.name}}, a {{brand.industry}} company based in {{brand.location}}.",

  "examples": [
    {
      "user": "Create an introduction for TechCorp, a software company based in London.",
      "assistant": "TechCorp stands at the forefront of software innovation, delivering cutting-edge solutions that transform how businesses operate."
    }
  ],

  "constraints": {
    "maxTokens": 500,
    "temperature": 0.7,
    "responseFormat": "text"
  },

  "requiredRefs": ["tone", "brand"],
  "optionalRefs": ["values"],

  "partials": ["writing-rules"],

  "tags": ["brand", "intro", "text"]
}
```

---

## Partial Format

Reusable snippets stored in `/repos/partials/`:

```json
{
  "id": "writing-rules",
  "version": "1.0",
  "content": "Always write in active voice. Keep sentences under 20 words. Avoid jargon unless industry-specific.",
  "tags": ["rules", "style"]
}
```

---

## Version Logging

Every compiled prompt logs which versions were used:

```json
{
  "contentId": "blog_20260117_acme_intro",
  "compiledPrompt": {
    "templateId": "brand-intro",
    "templateVersion": "2.1",
    "date": "2026-01-17T14:30:52Z",
    "refs": {
      "tone": "formal-corporate@1.0",
      "brand": "acme",
      "partials": ["writing-rules@1.0"]
    }
  }
}
```

This enables:
- Tracing which template created which content
- Rolling back to previous versions
- Understanding what changed if output quality changed

---

## Caching

### What Gets Cached

| Data | Cache Duration | Why |
|------|----------------|-----|
| Brand data | Per batch | Same brand used 100x in batch |
| Tone/Voice | Per batch | Rarely changes |
| Partials | Per batch | Shared across templates |
| Templates | Per batch | Loaded once, used many times |

### Cache Behaviour

```javascript
// During batch processing
batch.start()
  ↓
First call: loadContext('acme') → fetch from FileManager → cache
  ↓
Next 99 calls: loadContext('acme') → return from cache
  ↓
batch.end() → clear cache

// Next batch starts fresh (picks up any changes)
```

### Cache Config

```json
{
  "cache": {
    "enabled": true,
    "scope": "batch",
    "maxItems": 1000,
    "ttl": 3600000
  }
}
```

---

## Error Handling

### Missing Ref

```
PromptBuilder.compile() fails
  "Tone 'formal-corporate' not found"
         ↓
Classify data type:
  - "tone" is in canBeCreated? → FailureHandler.selfHeal()
  - "tone" is in mustBeSpecific? → Librarian.recommend()
         ↓
Solution found → Retry compile with new ref
         ↓
Still failing → Escalate to human
```

### Token Limit Exceeded

```
validateTokens() fails
  "Prompt exceeds model context by 300 tokens"
         ↓
FailureHandler receives error
         ↓
Possible solutions:
  1. Try template with "-short" suffix if exists
  2. Reduce max_tokens in constraints
  3. Suggest model with larger context
  4. Escalate to human to shorten content
```

### Invalid Template

```
Template has syntax error or missing required fields
         ↓
validatePrompt() fails
         ↓
Return error with specific field/line info
         ↓
Log for debugging, escalate to human (needs manual fix)
```

---

## Output Format

PromptBuilder outputs a **universal format**. AICaller converts to provider-specific format.

```javascript
// PromptBuilder output (universal)
{
  messages: [
    { role: "system", content: "..." },
    { role: "user", content: "..." },
    { role: "assistant", content: "..." },
    { role: "user", content: "..." }
  ],
  max_tokens: 500,
  temperature: 0.7,
  metadata: { ... }
}

// AICaller converts for Claude
{
  system: "...",
  messages: [
    { role: "user", content: "..." },
    { role: "assistant", content: "..." },
    { role: "user", content: "..." }
  ],
  max_tokens: 500
}

// AICaller converts for OpenAI
{
  messages: [
    { role: "system", content: "..." },
    { role: "user", content: "..." },
    { role: "assistant", content: "..." },
    { role: "user", content: "..." }
  ],
  max_tokens: 500
}
```

---

## Config

### module.config.json

```json
{
  "module": "PromptBuilder",
  "version": "1.0.0",
  "description": "Compile prompts from templates and context",
  "type": "service",
  "timeoutLevel": 1,

  "functions": [
    { "name": "loadTemplate", "input": ["promptId"], "output": "template" },
    { "name": "loadContext", "input": ["refs", "brandId"], "output": "context" },
    { "name": "loadPartial", "input": ["partialId"], "output": "partial" },
    { "name": "resolveVariables", "input": ["template", "context"], "output": "resolvedTemplate" },
    { "name": "resolveConditionals", "input": ["template", "context"], "output": "processedTemplate" },
    { "name": "resolvePartials", "input": ["template"], "output": "expandedTemplate" },
    { "name": "applyConstraints", "input": ["prompt", "limits"], "output": "constrainedPrompt" },
    { "name": "validateTokens", "input": ["prompt", "modelLimits"], "output": "validation" },
    { "name": "addExamples", "input": ["prompt", "examples"], "output": "promptWithExamples" },
    { "name": "compile", "input": ["refs", "brandId"], "output": "compiledPrompt" },
    { "name": "getCached", "input": ["key"], "output": "cached" },
    { "name": "setCache", "input": ["key", "value", "ttl"], "output": "success" }
  ],

  "steps": [
    {
      "name": "compile",
      "description": "Full compilation flow",
      "sequence": ["loadTemplate", "loadContext", "resolvePartials", "resolveConditionals", "resolveVariables", "addExamples", "applyConstraints", "validateTokens"]
    },
    {
      "name": "compileWithFallback",
      "description": "Compile with Librarian fallback",
      "sequence": ["compile"],
      "onError": "librarian_recommend"
    },
    {
      "name": "compileWithCache",
      "description": "Compile using cache",
      "sequence": ["getCached", "compile", "setCache"]
    }
  ],

  "config": {
    "variableSyntax": "{{variable}}",
    "conditionalSyntax": "{{#if}}...{{/if}}",
    "partialSyntax": "{{> partial}}",
    "maxPromptTokens": 8000,
    "defaultMaxTokens": 1000,
    "cache": {
      "enabled": true,
      "scope": "batch",
      "maxItems": 1000,
      "ttl": 3600000
    },
    "dataTypes": {
      "requiredNoSubstitute": ["brand.name", "brand.id", "client.name", "client.id", "legal.*"],
      "requiredCanFindAlternative": ["tone.*", "voice.*", "style.*", "values.*"],
      "optionalCanBeCreated": ["writingRules.*", "formatGuidelines.*", "genericContext.*"]
    }
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Fetching templates, tones, brands, partials from repos |
| Librarian | Fallback recommendations on missing specific refs |
| TokenManager | Token estimation for validation |
| FailureHandler | Self-healing for missing creatable refs |

---

## Used By

| Module | How |
|--------|-----|
| AICaller | Gets compiled prompt for API call |
| Orchestrator | Chain step execution |
| Self-healing | Generates prompts for creating new repo entries |

---

*Last updated: 2026-01-17*
