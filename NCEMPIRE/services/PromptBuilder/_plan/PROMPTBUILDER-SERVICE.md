# PromptBuilder Service

> Central service for ALL prompt construction and template management.
> **Status:** Planning | **Priority:** MEDIUM
> **Location:** `C:\Users\Business\PromptBuilder\`

---

## Overview

The PromptBuilder service handles all prompt assembly. Apps request prompts by template name and variables - they never construct prompts directly. PromptBuilder manages templates, validates variables, chains prompts, and estimates tokens.

### Current State (BrandKit Workflow)

Prompt functionality is currently embedded in BrandKit Workflow:

```
BrandKit Workflow/
├── lib/server/
│   ├── prompt-builder.ts       # Main prompt building
│   ├── template-generator.ts   # Generate user templates
│   └── types/prompt.ts         # Type definitions
│
└── BrandKit AIGen/sections/    # Prompt templates (JSON configs)
    ├── core--identity.json
    ├── core--purpose.json
    ├── voice--tone.json
    └── ... (50 section configs)
```

### Target State (PromptBuilder Service)

```
PromptBuilder/
├── src/
│   ├── index.ts                    # Public API
│   ├── scripts/
│   │   ├── load-template.ts        # Get template from library
│   │   ├── inject-variables.ts     # Fill in context
│   │   ├── chain-prompts.ts        # Multi-step assembly
│   │   ├── validate-prompt.ts      # Check all vars filled
│   │   ├── estimate-tokens.ts      # Token count estimation
│   │   └── optimize-prompt.ts      # Trim if too long
│   └── types/
│       └── index.ts                # Type definitions
├── templates/
│   ├── brand/
│   │   ├── identity-extraction.md
│   │   ├── voice-generation.md
│   │   └── domain-detection.md
│   ├── content/
│   │   ├── blog-post.md
│   │   └── social-caption.md
│   ├── library/
│   │   └── entry-generation.md
│   └── system/
│       ├── json-output.md
│       └── extraction.md
├── _config/
│   ├── variables.json              # Required vars per template
│   ├── chains.json                 # Multi-prompt sequences
│   └── limits.json                 # Token limits per type
├── package.json
└── tsconfig.json
```

---

## Extraction Mapping

### Files to Extract

| Current Location | Target Location | Changes Needed |
|------------------|-----------------|----------------|
| `lib/server/prompt-builder.ts` | `src/scripts/*.ts` | Split into focused scripts |
| `lib/server/template-generator.ts` | `src/scripts/generate-user-template.ts` | Minimal changes |
| `lib/server/types/prompt.ts` | `src/types/index.ts` | Extend with chain types |
| `BrandKit AIGen/sections/*.json` | `templates/brand/*.md` | Convert to markdown templates |

### Functions to Extract

| Current Function | Target Script | Purpose |
|------------------|---------------|---------|
| `buildPrompt()` | `index.ts` (main) | Main entry point |
| `loadSectionConfig()` | `load-template.ts` | Load template file |
| `buildSystemPrompt()` | `inject-variables.ts` | Build system message |
| `buildUserPrompt()` | `inject-variables.ts` | Build user message |
| `gatherContext()` | Keep in app | App-specific context gathering |
| `estimateTokens()` | `estimate-tokens.ts` | Token estimation |
| `generateTemplate()` | `generate-user-template.ts` | User-facing templates |

---

## New Functionality

### 1. Markdown Templates

Convert JSON configs to markdown templates with frontmatter:

```markdown
---
id: brand/voice-generation
name: Voice Generation
category: brand
required_vars:
  - brand_name
  - domain
  - context
optional_vars:
  - examples
  - tone_preferences
token_limit: 4000
---

# System Prompt

You are a brand voice specialist. Your task is to generate authentic brand voice content.

## Rules
- Use the brand's established tone
- Be consistent with domain conventions
- Reference provided examples when available

# User Prompt

Generate brand voice content for **{{brand_name}}**, a {{domain}} business.

## Context
{{context}}

{{#if examples}}
## Examples
{{examples}}
{{/if}}

## Output Format
Return JSON with the following structure:
```json
{
  "primary_tone": "string",
  "supporting_tones": ["string"],
  "vocabulary": ["string"],
  "phrases": ["string"]
}
```
```

### 2. Variable Validation

```typescript
// src/scripts/validate-prompt.ts
export interface ValidationResult {
  valid: boolean;
  missingRequired: string[];
  missingOptional: string[];
  unusedProvided: string[];
}

export function validateVariables(
  template: PromptTemplate,
  variables: Record<string, any>
): ValidationResult {
  const provided = Object.keys(variables);
  const required = template.required_vars || [];
  const optional = template.optional_vars || [];

  const missingRequired = required.filter(v => !provided.includes(v));
  const missingOptional = optional.filter(v => !provided.includes(v));
  const unusedProvided = provided.filter(v =>
    !required.includes(v) && !optional.includes(v)
  );

  return {
    valid: missingRequired.length === 0,
    missingRequired,
    missingOptional,
    unusedProvided
  };
}
```

### 3. Prompt Chaining

```typescript
// _config/chains.json
{
  "chains": {
    "full-brand-generation": {
      "steps": [
        { "template": "brand/identity-extraction", "outputVar": "identity" },
        { "template": "brand/voice-generation", "inputVars": ["identity"] },
        { "template": "brand/visual-guidelines", "inputVars": ["identity"] }
      ]
    },
    "content-with-research": {
      "steps": [
        { "template": "content/research-topic", "outputVar": "research" },
        { "template": "content/blog-post", "inputVars": ["research"] }
      ]
    }
  }
}

// src/scripts/chain-prompts.ts
export async function buildChain(
  chainId: string,
  initialVars: Record<string, any>
): Promise<ChainedPrompt[]> {
  const chain = loadChain(chainId);
  const prompts: ChainedPrompt[] = [];
  let currentVars = { ...initialVars };

  for (const step of chain.steps) {
    const prompt = await buildPrompt(step.template, currentVars);
    prompts.push({
      step: step.template,
      prompt,
      outputVar: step.outputVar
    });

    // Note: Actual execution happens via AICaller
    // This just builds the sequence
  }

  return prompts;
}
```

### 4. Token Optimization

```typescript
// src/scripts/optimize-prompt.ts
export interface OptimizeOptions {
  maxTokens: number;
  preserveSections: string[];  // Never trim these
  trimOrder: string[];         // Order to trim
}

export function optimizePrompt(
  prompt: string,
  options: OptimizeOptions
): OptimizedPrompt {
  const currentTokens = estimateTokens(prompt);

  if (currentTokens <= options.maxTokens) {
    return { prompt, optimized: false, tokens: currentTokens };
  }

  let optimized = prompt;

  for (const section of options.trimOrder) {
    if (options.preserveSections.includes(section)) continue;

    // Remove or truncate section
    optimized = truncateSection(optimized, section);

    if (estimateTokens(optimized) <= options.maxTokens) break;
  }

  return {
    prompt: optimized,
    optimized: true,
    tokens: estimateTokens(optimized),
    removed: findRemovedSections(prompt, optimized)
  };
}
```

---

## Public API

```typescript
// src/index.ts - PromptBuilder Public API

// Build single prompt
export async function build(
  templateId: string,
  variables: Record<string, any>,
  options?: BuildOptions
): Promise<BuiltPrompt>;

// Build prompt chain
export async function buildChain(
  chainId: string,
  variables: Record<string, any>
): Promise<ChainedPrompt[]>;

// Validate variables before building
export async function validateVariables(
  templateId: string,
  variables: Record<string, any>
): Promise<ValidationResult>;

// Get template info
export async function getTemplateInfo(templateId: string): Promise<TemplateInfo>;

// List available templates
export async function listTemplates(category?: string): Promise<TemplateInfo[]>;

// Estimate tokens for prompt
export function estimateTokens(text: string): number;

// Generate user-facing template (for manual fill)
export async function generateUserTemplate(
  sectionId: string,
  format: 'markdown' | 'json' | 'text'
): Promise<string>;
```

---

## Migration Steps

### Step 1: Create PromptBuilder Shell

```bash
mkdir C:\Users\Business\PromptBuilder
cd C:\Users\Business\PromptBuilder
npm init -y
npm install typescript ts-node gray-matter
mkdir src src/scripts src/types templates templates/brand templates/content templates/system _config
```

### Step 2: Convert Templates

```typescript
// Script to convert AIGen JSON configs to markdown templates
// For each BrandKit AIGen/sections/*.json:
// 1. Extract prompt.role, prompt.rules, prompt.instructions
// 2. Create markdown with frontmatter
// 3. Add variable placeholders
```

### Step 3: Extract Core Functions

```bash
# From prompt-builder.ts:
# - loadSectionConfig → load-template.ts
# - buildSystemPrompt, buildUserPrompt → inject-variables.ts
# - estimateTokens → estimate-tokens.ts
```

### Step 4: Add New Features

```bash
# Add:
# - validate-prompt.ts (variable validation)
# - chain-prompts.ts (prompt chaining)
# - optimize-prompt.ts (token optimization)
```

### Step 5: Create Public API

```typescript
// src/index.ts
// Unified interface that routes to scripts
```

### Step 6: Update BrandKit Workflow

```typescript
// Before
import { buildPrompt } from '$lib/server/prompt-builder';
const { systemPrompt, userPrompt } = await buildPrompt({ brandPath, section });

// After
import { PromptBuilder } from '@services/prompt-builder';
const prompt = await PromptBuilder.build('brand/voice-generation', {
  brand_name: brandConfig.name,
  domain: brandConfig.domain,
  context: await gatherContext(brandPath)
});
```

---

## Integration with Current Build

### During Migration

```typescript
// lib/server/prompt-builder.ts - Add forwarding
export async function buildPrompt(options: BuildPromptOptions): Promise<PromptResult> {
  if (process.env.USE_PROMPTBUILDER_SERVICE === 'true') {
    // Map old format to new format
    const templateId = `brand/${options.section.replace('/', '-')}`;
    const variables = {
      brand_name: options.brandConfig?.name,
      domain: options.brandConfig?.domain,
      context: await gatherContext(options.brandPath)
    };

    const result = await PromptBuilder.build(templateId, variables);
    return {
      systemPrompt: result.systemPrompt,
      userPrompt: result.userPrompt,
      estimatedTokens: result.tokens
    };
  }

  // Local implementation
  return localBuildPrompt(options);
}
```

### Template Mapping

| Current (AIGen) | Target (PromptBuilder) |
|-----------------|------------------------|
| `core--identity.json` | `templates/brand/identity.md` |
| `core--purpose.json` | `templates/brand/purpose.md` |
| `voice--tone.json` | `templates/brand/voice-tone.md` |
| `legal--core.json` | `templates/legal/core.md` |

---

## Dependencies

| Service | Dependency Type | Purpose |
|---------|-----------------|---------|
| **Librarian** | Optional | Get example entries for prompts |
| **None required** | - | PromptBuilder is self-contained |

---

## Notes

- Templates use markdown with frontmatter for metadata
- Variables use `{{variable}}` syntax (Handlebars-like)
- Conditionals use `{{#if var}}...{{/if}}` syntax
- Token estimation is approximate (~4 chars per token)
- Chains define multi-step prompt sequences
- Optimization trims non-essential sections to fit limits

---

*Last updated: 2026-01-16*
