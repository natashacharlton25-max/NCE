# PromptCondenser Module

> **Purpose:** Validate prompt token limits and intelligently condense when necessary
> **Type:** AI Services Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

PromptCondenser validates prompts against model token limits and applies intelligent condensing strategies when prompts exceed available context. It preserves essential content while removing or summarizing lower-priority content.

```
┌─────────────────────────────────────────────────────────────────┐
│                    CONDENSING PIPELINE                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. MEASURE                                                     │
│     └─ Count tokens by section and priority                     │
│                          ↓                                       │
│  2. EVALUATE                                                    │
│     ├─ Compare total vs available context                       │
│     └─ If under limit → return unchanged                        │
│                          ↓                                       │
│  3. PLAN CONDENSING                                             │
│     ├─ Calculate tokens to remove                               │
│     ├─ Build removal plan by priority                           │
│     └─ Never touch Essential (priority 1)                       │
│                          ↓                                       │
│  4. EXECUTE                                                     │
│     ├─ Remove Optional content (priority 5)                     │
│     ├─ Remove Supporting content (priority 4)                   │
│     ├─ Truncate Important content (priority 3)                  │
│     └─ Summarize Critical content (priority 2)                  │
│                          ↓                                       │
│  5. VERIFY                                                      │
│     ├─ Recount tokens                                           │
│     ├─ Confirm under limit                                      │
│     └─ Return condensed prompt + report                         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Roles & Rules

### PromptCondenser DOES:
- Count tokens using model-specific tokenizers
- Validate prompts against model context limits
- Apply priority-based content removal
- Truncate content with ellipsis markers
- Summarize content using compression rules
- Track what was removed/modified
- Report token savings

### PromptCondenser does NOT:
- Build prompts (PromptBuilder does that)
- Call AI models (AICaller does that)
- Parse AI responses (Parsers does that)
- Store anything (stateless operations)

### Boundaries:
- Read-only access to prompt content
- Returns new prompt (never mutates input)
- Never removes Essential-priority content
- If Essential alone exceeds limit → error (cannot condense)

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| **Core Operations** |
| `condense(prompt, model)` | StructuredPrompt, modelId | CondensedResult | Full pipeline: measure → evaluate → condense → verify |
| `validate(prompt, model)` | StructuredPrompt, modelId | ValidationResult | Check if prompt fits, report margin |
| `willFit(prompt, model)` | StructuredPrompt, modelId | boolean | Quick check without condensing |
| **Measurement** |
| `countTokens(text, model?)` | text, modelId | number | Count tokens for text |
| `measurePrompt(prompt)` | StructuredPrompt | TokenBreakdown | Detailed count by section and priority |
| `getModelLimits(model)` | modelId | ModelLimits | Get context limit and defaults |
| **Strategies** |
| `planCondensing(breakdown, target)` | TokenBreakdown, targetTokens | CondensingPlan | Plan what to remove/summarize |
| `executeCondensing(prompt, plan)` | StructuredPrompt, CondensingPlan | string | Apply condensing plan |

---

## Type Definitions

### StructuredPrompt

```javascript
{
  sections: [
    {
      name: string,              // Section identifier
      content: string,           // Section text
      priority: 1 | 2 | 3 | 4 | 5,  // Condensing priority
      tokens?: number            // Pre-counted tokens (optional)
    }
  ],

  metadata: {
    templateId: string,
    model: string,
    reserveOutput: number        // Tokens to reserve for response
  }
}
```

### TokenBreakdown

```javascript
{
  total: number,                 // Total token count
  available: number,             // Model limit - reserved output
  margin: number,                // available - total (negative if over)
  overBy: number,                // Max(0, total - available)

  bySection: {
    [sectionName: string]: number
  },

  byPriority: {
    essential: number,           // Priority 1 - never remove
    critical: number,            // Priority 2 - summarize only
    important: number,           // Priority 3 - can truncate
    supporting: number,          // Priority 4 - can remove
    optional: number             // Priority 5 - remove first
  }
}
```

### CondensingPlan

```javascript
{
  targetReduction: number,       // Tokens to remove

  actions: [
    {
      section: string,           // Section name
      action: 'remove' | 'truncate' | 'summarize',
      priority: number,
      estimatedSavings: number,
      truncateTo?: number,       // For truncate: target length
      summarizeRatio?: number    // For summarize: compression ratio
    }
  ],

  projectedTotal: number,        // Expected tokens after condensing
  willFit: boolean               // Whether plan achieves target
}
```

### CondensedResult

```javascript
{
  prompt: string,                // Final condensed prompt text

  tokens: {
    original: number,
    condensed: number,
    saved: number,
    available: number,
    margin: number               // Positive = fits, negative = still over
  },

  condensed: boolean,            // Whether condensing was applied

  report: {
    removed: string[],           // Sections removed entirely
    truncated: string[],         // Sections truncated
    summarized: string[],        // Sections summarized
    unchanged: string[]          // Sections kept as-is
  },

  success: boolean               // True if prompt now fits
}
```

### ValidationResult

```javascript
{
  valid: boolean,                // True if prompt fits

  tokens: {
    total: number,
    limit: number,
    reserved: number,
    available: number,
    margin: number
  },

  breakdown: TokenBreakdown,

  warning?: string,              // If margin < 15%
  error?: string                 // If over limit
}
```

### ModelLimits

```javascript
{
  modelId: string,
  contextLimit: number,          // Total context window
  defaultOutputReserve: number,  // Default tokens for response
  tokenizer: string              // Which tokenizer to use
}
```

---

## Token Priority System

Content is tagged with priority for intelligent condensing:

| Tier | Priority | Strategy | Example Content |
|------|----------|----------|-----------------|
| **Essential** | 1 (highest) | Never remove | IDs, names, instructions, output format |
| **Critical** | 2 | Summarize only | Core concept, message, belief shifts, primary truth |
| **Important** | 3 | Truncate examples | Keywords, examples, secondary truths |
| **Supporting** | 4 | Remove if needed | Background, limitations, pairsWellWith |
| **Optional** | 5 (lowest) | Remove first | Extended examples, verbose descriptions |

### Priority Assignment by Context Type

| Context Type | Essential (1) | Critical (2) | Important (3) | Supporting (4) | Optional (5) |
|--------------|---------------|--------------|---------------|----------------|--------------|
| **Brand** | id, name | voice.core, values | voice.examples | philosophy | background |
| **Audience** | id, name, psychographics | painPoints, desiredOutcome | demographics | extended examples | - |
| **Theme** | id, concept, message | beliefShifts, primaryTruth | keywords, tone | secondaryTruths | - |
| **Framework** | id, stages | prompts, purpose | - | background, limitations | - |
| **Section** | type, plan | keyPoints, truthsReferenced | - | suggestions, toneNotes | - |
| **Paragraph** | structure, components | guidance | - | examples | cognitive variants |

---

## Condensing Strategies

### Strategy 1: Priority-Based Removal

```
PHASE 1 - Remove Optional (priority 5)
├─ Extended examples
├─ Verbose descriptions
└─ Historical context
    ↓ (if still over)
PHASE 2 - Remove Supporting (priority 4)
├─ Background information
├─ Limitations
└─ pairsWellWith references
    ↓ (if still over)
PHASE 3 - Truncate Important (priority 3)
├─ Shorten example lists (keep first 2)
├─ Truncate secondary truths
└─ Reduce keyword count
    ↓ (if still over)
PHASE 4 - Summarize Critical (priority 2)
├─ Compress belief shifts (keep from/to only)
├─ Summarize psychographics (key traits only)
└─ Condense framework stages (names + one-line each)
    ↓
NEVER - Essential (priority 1)
└─ IDs, names, core instructions, output format
```

### Strategy 2: Context Budget Enforcement

Per-context-type token budgets from `_constants.md`:

| Context Type | Budget | Action if Exceeded |
|--------------|--------|-------------------|
| Brand context | 2000 | Truncate examples, summarize philosophy |
| Audience context | 1500 | Keep psychographics, drop demographics |
| Theme context | 3000 | Keep concept/message, summarize truths |
| Framework context | 1500 | Keep stage names + prompts, drop background |
| Template context | 1000 | Keep structure, drop verbose descriptions |
| Section context | 500 | Keep plan + key points only |
| Paragraph context | 500 | Keep structure guidance only |

### Strategy 3: Truncation Rules

```javascript
// Truncation preserves beginning, adds marker
function truncate(content, targetTokens) {
  const truncated = tokenSlice(content, 0, targetTokens - 10);
  return truncated + "\n[...truncated...]";
}

// List truncation keeps first N items
function truncateList(items, keepCount = 2) {
  if (items.length <= keepCount) return items;
  return [...items.slice(0, keepCount), `(+${items.length - keepCount} more)`];
}
```

### Strategy 4: Summarization Rules

```javascript
// Summarization compresses content
// Applied to priority 2 (Critical) only

// Belief shifts: keep from/to pairs
"Belief Shifts:\n- From: X → To: Y\n- From: A → To: B"

// Psychographics: key descriptors only
"Psychographics: analytical, skeptical, growth-focused"

// Framework stages: name + one line
"Stage 1 (Foundation): Establish credibility"
```

---

## Configuration

```json
{
  "module": "PromptCondenser",
  "version": "0.0.0",
  "description": "Validate and condense prompts for context limits",
  "type": "ai-services",

  "config": {
    "models": {
      "claude-3-5-sonnet": {
        "contextLimit": 200000,
        "defaultOutputReserve": 8000,
        "tokenizer": "claude"
      },
      "claude-3-opus": {
        "contextLimit": 200000,
        "defaultOutputReserve": 4000,
        "tokenizer": "claude"
      },
      "claude-3-haiku": {
        "contextLimit": 200000,
        "defaultOutputReserve": 4000,
        "tokenizer": "claude"
      }
    },

    "contextBudgets": {
      "brandContext": 2000,
      "audienceContext": 1500,
      "themeContext": 3000,
      "frameworkContext": 1500,
      "templateContext": 1000,
      "sectionContext": 500,
      "paragraphContext": 500
    },

    "thresholds": {
      "warnMarginPercent": 15,
      "minRetainPercent": 20,
      "summarizeMinTokens": 100,
      "truncateMinTokens": 50
    }
  }
}
```

---

## Error Handling

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `ESSENTIAL_EXCEEDS_LIMIT` | Essential content alone exceeds model limit | Use larger model or split into multiple calls |
| `CANNOT_CONDENSE_ENOUGH` | Even max condensing can't fit | Use larger model or reduce scope |
| `UNKNOWN_MODEL` | Model not in config | Use default limits with warning |
| `TOKENIZER_ERROR` | Tokenization failed | Fall back to word count estimate |
| `INVALID_PRIORITY` | Priority not 1-5 | Treat as priority 3 (Important) |
| `EMPTY_PROMPT` | No content to condense | Return empty result |

---

## Integration with PromptBuilder

PromptBuilder calls PromptCondenser as final validation step:

```javascript
// In PromptBuilder.build():

// 1. Assemble prompt from template + context
const assembled = assemblePrompt(template, context);

// 2. Structure with priorities
const structured = {
  sections: [
    { name: 'system', content: systemPrompt, priority: 1 },
    { name: 'brand', content: brandContext, priority: 2 },
    { name: 'audience', content: audienceContext, priority: 3 },
    { name: 'theme', content: themeContext, priority: 2 },
    { name: 'instructions', content: outputInstructions, priority: 1 }
  ],
  metadata: { templateId, model, reserveOutput }
};

// 3. Validate and condense
const result = PromptCondenser.condense(structured, model);

if (!result.success) {
  throw new Error(result.error);
}

return {
  prompt: result.prompt,
  tokens: result.tokens,
  condensed: result.condensed,
  condensingReport: result.report
};
```

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Dev Team Gap Analysis (2026-01-20)

- **Tokenizer implementation**: Need to implement/integrate tokenizers for each model family
- **Caching token counts**: Consider caching counts for repeated content (brand context reuse)
- **Quality metrics**: Track condensing impact on output quality
- **Adaptive budgets**: Learn optimal budgets from successful generations

### Token Counting Approaches

1. **Exact (preferred)**: Use model-specific tokenizer (tiktoken, claude tokenizer)
2. **Approximate**: Use general tokenizer with adjustment factor per model
3. **Estimate**: Word count × 1.3 (least accurate, fastest fallback)

### Performance Considerations

- Token counting is O(n) on content length
- Cache token counts for repeated sections (brand, framework)
- Pre-count during prompt assembly to avoid double-counting

---

## Dependencies

| Depends On | For |
|------------|-----|
| (none) | Stateless module, no dependencies |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PromptBuilder | Validate and condense assembled prompts |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
