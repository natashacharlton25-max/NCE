# Content Module

> **Purpose:** Generate text content (intros, taglines, descriptions)
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

Content is a Pipeline Module for text generation tasks. It defines steps for generating different content types - brand intros, taglines, product descriptions, etc. The actual AI calls go through AICaller, Content just orchestrates.

```
Chain calls Content.step → Content prepares → AICaller generates → Content validates
```

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `prepareIntro(context)` | Prepare context for intro generation | context | preparedPrompt |
| `prepareTagline(context)` | Prepare context for tagline generation | context | preparedPrompt |
| `prepareDescription(context, type)` | Prepare for description generation | context, type | preparedPrompt |
| `validateContent(content, type)` | Validate generated content | content, type | validationResult |
| `refineContent(content, feedback)` | Prepare refinement request | content, feedback | preparedPrompt |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Content.generateIntro` | Generate brand introduction | prepareIntro → (AICaller via chain) → validateContent |
| `Content.generateTagline` | Generate tagline options | prepareTagline → (AICaller via chain) → validateContent |
| `Content.generateDescription` | Generate product/service description | prepareDescription → (AICaller via chain) → validateContent |
| `Content.refine` | Refine existing content | refineContent → (AICaller via chain) → validateContent |

---

## Detailed Function Specs

### prepareIntro(context)

Prepares prompt for brand introduction generation.

```javascript
// Input
{
  brand: {
    name: "Acme Corp",
    industry: "technology",
    values: ["innovation", "reliability"]
  },
  refs: {
    tone: "professional-tech",
    prompt: "brand-intro-v2"
  },
  options: {
    length: "medium",  // short, medium, long
    includeTagline: true
  }
}

// Process
1. Load prompt template via PromptBuilder
2. Set context variables
3. Return prepared prompt refs

// Output
{
  promptRef: "brand-intro-v2",
  context: {
    brand: { ... },
    tone: "professional-tech"
  },
  constraints: {
    length: "medium",
    includeTagline: true
  }
}
```

### validateContent(content, type)

Validates generated content meets requirements.

```javascript
// Input
{
  content: {
    headline: "Acme Corp",
    tagline: "Innovation starts here",
    intro: "Acme Corp is a leading..."
  },
  type: "brand-intro"
}

// Process
1. Check required fields present
2. Check length constraints
3. Check for prohibited content
4. Score quality

// Output (valid)
{
  valid: true,
  content: { ... },
  quality: {
    score: 0.89,
    notes: ["Good length", "Strong opening"]
  }
}

// Output (invalid)
{
  valid: false,
  issues: [
    { field: "tagline", issue: "Too long (12 words, max 8)" }
  ],
  suggestion: "Regenerate with stricter constraints"
}
```

### refineContent(content, feedback)

Prepares a refinement request for existing content.

```javascript
// Input
{
  content: {
    headline: "Acme Corp",
    intro: "Acme Corp is a leading technology company..."
  },
  feedback: {
    type: "user",
    notes: "Make it more energetic, less corporate"
  }
}

// Process
1. Load refinement prompt template
2. Include original content
3. Include feedback
4. Return prepared prompt

// Output
{
  promptRef: "content-refine",
  context: {
    original: { ... },
    feedback: "Make it more energetic, less corporate",
    constraints: "Keep same structure, adjust tone"
  }
}
```

---

## Content Types

| Type | Prompt Template | Typical Length |
|------|-----------------|----------------|
| `brand-intro` | brand-intro-v2 | 100-200 words |
| `tagline` | tagline-gen | 3-8 words |
| `product-desc` | product-description | 50-150 words |
| `about-us` | about-company | 200-400 words |
| `mission` | mission-statement | 1-3 sentences |

---

## Config

### module.config.json

```json
{
  "module": "Content",
  "version": "1.0.0",
  "description": "Generate text content",
  "type": "pipeline",

  "functions": [
    {
      "name": "prepareIntro",
      "description": "Prepare context for intro generation",
      "input": ["context"],
      "output": "preparedPrompt"
    },
    {
      "name": "prepareTagline",
      "description": "Prepare context for tagline generation",
      "input": ["context"],
      "output": "preparedPrompt"
    },
    {
      "name": "prepareDescription",
      "description": "Prepare for description generation",
      "input": ["context", "type"],
      "output": "preparedPrompt"
    },
    {
      "name": "validateContent",
      "description": "Validate generated content",
      "input": ["content", "type"],
      "output": "validationResult"
    },
    {
      "name": "refineContent",
      "description": "Prepare refinement request",
      "input": ["content", "feedback"],
      "output": "preparedPrompt"
    }
  ],

  "steps": [
    {
      "name": "generateIntro",
      "description": "Generate brand introduction",
      "sequence": ["prepareIntro", "validateContent"]
    },
    {
      "name": "generateTagline",
      "description": "Generate tagline options",
      "sequence": ["prepareTagline", "validateContent"]
    },
    {
      "name": "generateDescription",
      "description": "Generate product/service description",
      "sequence": ["prepareDescription", "validateContent"]
    },
    {
      "name": "refine",
      "description": "Refine existing content",
      "sequence": ["refineContent", "validateContent"]
    }
  ],

  "config": {
    "defaultLength": "medium",
    "maxRetries": 2
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| PromptBuilder | Loading/compiling prompts |
| FileManager | Loading brand context |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit chain | Content generation steps |
| Orchestrator | Direct content chains |

---

*Last updated: 2026-01-17*
