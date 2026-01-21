# PromptBuilder Module

> **Purpose:** Build, validate, and condense prompts for AI calls
> **Type:** AI Services Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

PromptBuilder assembles prompts from templates and context data, validates them against model token limits, and condenses when necessary. It ensures prompts are valid and optimized before sending to AICaller.

```
┌─────────────────────────────────────────────────────────────────┐
│                    PROMPT BUILDING PIPELINE                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. LOAD TEMPLATE                                               │
│     └─ Load prompt template from /repos/prompts/                │
│                          ↓                                       │
│  2. INJECT CONTEXT                                              │
│     ├─ Brand context (voice, values)                            │
│     ├─ Audience context (psychographics, language)              │
│     ├─ Theme context (concept, truths, beliefShifts)            │
│     ├─ Framework context (stages, prompts)                      │
│     ├─ Section context (plan, keyPoints)                        │
│     └─ Paragraph context (structure, components)                │
│                          ↓                                       │
│  3. MERGE TONES                                                 │
│     └─ theme.tone + section.toneNotes → merged tone             │
│                          ↓                                       │
│  4. VALIDATE & CONDENSE (via PromptCondenser)                   │
│     ├─ Count tokens                                             │
│     ├─ If over limit: PromptCondenser condenses by priority     │
│     └─ Verify prompt fits model context                         │
│                          ↓                                       │
│  5. RETURN VALIDATED PROMPT                                     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Roles & Rules

### PromptBuilder DOES:
- Load and parse prompt templates
- Inject context data into templates
- Resolve template variables (`{{brand.name}}`, `{{theme.angle}}`)
- Merge tones (theme.tone + section.toneNotes)
- Count tokens for prompts
- Validate prompts against model limits
- Condense prompts using priority-based removal
- Track token usage by context type

### PromptBuilder does NOT:
- Call AI models (AICaller does that)
- Generate content (TemplateEngine does that)
- Store prompts (single-use)
- Manage context data (modules provide their own context)

### Boundaries:
- Owns prompt templates in `/repos/prompts/`
- Read-only access to all context libraries
- Never removes Essential-priority content
- Model-aware (different limits for different models)

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| **Building** |
| `build(templateId, context, model)` | template, context, modelId | ValidatedPrompt | Full pipeline: load → inject → validate → condense |
| `buildThemePrompt(context)` | theme generation context | ValidatedPrompt | Build prompt for theme generation |
| `buildOutlinePrompt(context)` | outline generation context | ValidatedPrompt | Build prompt for outline generation |
| `buildSectionPrompt(context)` | section rendering context | ValidatedPrompt | Build prompt for section content |
| `buildParagraphPrompt(context)` | paragraph context | ValidatedPrompt | Build prompt for paragraph with structure |
| **Validation** |
| `validate(prompt, model)` | prompt, modelId | ValidationResult | Validate and condense if needed |
| `willFit(prompt, model)` | prompt, modelId | boolean | Quick check without condensing |
| **Measurement** |
| `countTokens(text, model?)` | text, modelId | number | Count tokens for text |
| `measurePrompt(prompt)` | structured prompt | TokenBreakdown | Detailed token count by section |

---

## Type Definitions

### ValidatedPrompt

```javascript
{
  prompt: string,                    // Final prompt text

  tokens: {
    total: number,                   // Total token count
    limit: number,                   // Model's context limit
    reserved: number,                // Reserved for output
    available: number                // Tokens available for prompt
  },

  condensed: boolean,                // Whether condensing was applied
  condensingReport?: {
    removed: string[],               // Fields removed
    summarized: string[],            // Fields summarized
    truncated: string[],             // Fields truncated
    tokensSaved: number
  },

  metadata: {
    templateId: string,
    model: string,
    builtAt: string                  // ISO timestamp
  }
}
```

### TokenBreakdown

```javascript
{
  total: number,

  bySection: {
    systemPrompt: number,
    brandContext: number,
    audienceContext: number,
    themeContext: number,
    frameworkContext: number,
    sectionContext: number,
    paragraphContext: number,
    outputInstructions: number
  },

  byPriority: {
    essential: number,               // Never remove
    critical: number,                // Summarize only
    important: number,               // Can truncate
    supporting: number,              // Can remove
    optional: number                 // Remove first
  }
}
```

### BuildContext

```javascript
{
  // Required
  templateId: string,                // Which prompt template to use
  model: string,                     // Target model (affects token limits)

  // Context objects (all optional, based on template needs)
  brand?: Brand,
  audience?: Audience,
  theme?: Theme,
  framework?: Framework,
  contentType?: ContentType,
  template?: Template,
  outline?: Outline,
  section?: SectionOutline,
  paragraph?: ParagraphPlan,

  // Overrides
  toneOverride?: string,             // Override merged tone
  reserveOutput?: number,            // Tokens to reserve for response
  priorityOverrides?: {              // Override field priorities
    [fieldPath: string]: 1 | 2 | 3 | 4 | 5
  }
}
```

---

## Token Priority System

Content is tagged with priority for intelligent condensing:

| Tier | Priority | Condense Strategy | Example Content |
|------|----------|-------------------|-----------------|
| **Essential** | 1 (highest) | Never remove | id, name, instructions, output format |
| **Critical** | 2 | Summarize only | concept, message, beliefShifts, primaryTruth |
| **Important** | 3 | Truncate examples | keywords, examples, secondaryTruths |
| **Supporting** | 4 | Remove if needed | background, limitations, pairsWellWith |
| **Optional** | 5 (lowest) | Remove first | extended examples, verbose descriptions |

### Priority by Context Type

| Context Type | Essential Fields | Critical Fields | Supporting Fields |
|--------------|------------------|-----------------|-------------------|
| **Brand** | id, name, voice.core | values, philosophy | voice.examples, background |
| **Audience** | id, name, psychographics | painPoints, desiredOutcome | demographics, extended examples |
| **Theme** | id, concept, message | beliefShifts, primaryTruth | keywords, tone, secondaryTruths |
| **Framework** | id, stages | prompts, purpose | background, limitations |
| **Section** | type, plan | keyPoints, truthsReferenced | suggestions, toneNotes |
| **Paragraph** | structure, components | guidance | examples, cognitive variants |

---

## Token Budgets by Context Type

From `_constants.md`:

| Context Type | Budget | Notes |
|--------------|--------|-------|
| Brand context | 2000 | Voice, values, name |
| Audience context | 1500 | Psychographics, language |
| Theme context | 3000 | Full theme with outcomes |
| Template context | 1000 | Structure and placeholders |
| Section context | 500 | Per-section allocation |
| Framework context | 1500 | Stage prompts and structure |
| Paragraph context | 500 | Structure guidance |

---

## Prompt Templates

Templates stored in `/repos/prompts/`:

```
/repos/prompts/
├── theme/
│   ├── generate.prompt.md           # Theme generation
│   └── derive.prompt.md             # Theme derivation
├── outline/
│   ├── generate.prompt.md           # Outline generation
│   └── regenerate-section.prompt.md # Section regeneration
├── section/
│   ├── mechanism.prompt.md          # Mechanism section
│   ├── definition.prompt.md         # Definition section
│   ├── reflection.prompt.md         # Reflection section
│   └── ...
└── paragraph/
    ├── pearl.prompt.md              # PEARL structure
    ├── safe.prompt.md               # SAFE structure
    └── cer.prompt.md                # CER structure
```

### Template Variable Syntax

```markdown
# Section Generation: {{section.type}}

## Brand Context
You are writing for {{brand.name}}.
Voice: {{brand.voice.core}}
Values: {{brand.values | join(", ")}}

## Theme
Topic: {{theme.topic}}
Concept: {{theme.concept}}
Message: {{theme.message}}

## Framework Stage
This section maps to: {{section.frameworkStageId}}
Stage prompts:
{{#each section.frameworkPrompts}}
- {{this}}
{{/each}}

## Section Plan
{{section.plan}}

Key points to cover:
{{#each section.keyPoints}}
- {{this}}
{{/each}}

## Paragraph Structure
Use {{section.paragraphStructure | upper}} structure:
{{#each paragraphStructure.components}}
**{{name}}**: {{guidance}}
{{/each}}

## Output
Write the {{section.type}} section following the plan above.
Use {{theme.tone}}{{#if section.toneNotes}} with {{section.toneNotes}} elements{{/if}}.
```

---

## Condensing Strategies

### Strategy 1: Priority-Based Removal

```
1. Remove Optional (priority 5):
   - Extended examples
   - Verbose descriptions
   - Historical context

2. Remove Supporting (priority 4):
   - Background information
   - Limitations
   - pairsWellWith references

3. Truncate Important (priority 3):
   - Shorten example lists (keep first 2)
   - Truncate secondary truths
   - Reduce keyword count

4. Summarize Critical (priority 2):
   - Summarize belief shifts (keep from/to)
   - Compress psychographics
   - Condense framework stages

5. NEVER Remove Essential (priority 1):
   - IDs, names, core instructions
   - Output format specifications
   - Primary truth, main concept
```

### Strategy 2: Context-Type Budgets

Enforce maximum tokens per context type:

```javascript
// If brand context exceeds budget:
if (brandTokens > tokenBudgets.brandContext) {
  condenseBrandContext(brandContext, tokenBudgets.brandContext);
}
```

---

## Tone Merging

Section toneNotes modify (not replace) theme tone:

```javascript
// Merge rules:
// - Theme tone is base
// - Section toneNotes adds nuance

function mergeTones(themeTone, sectionToneNotes) {
  if (!sectionToneNotes) return themeTone;

  // Examples:
  // "Authoritative" + "Vulnerable" → "Authoritative but Vulnerable"
  // "Warm" + "Direct" → "Warm but Direct"
  // "Professional" + "Playful" → "Professional with Playful moments"

  return `${themeTone} with ${sectionToneNotes} elements`;
}
```

---

## Configuration

```json
{
  "module": "PromptBuilder",
  "version": "0.0.0",
  "description": "Build and validate prompts for AI calls",
  "type": "ai-services",

  "config": {
    "templates": {
      "path": "/repos/prompts/",
      "extension": ".prompt.md"
    },

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

    "tokenBudgets": {
      "brandContext": 2000,
      "audienceContext": 1500,
      "themeContext": 3000,
      "frameworkContext": 1500,
      "templateContext": 1000,
      "sectionContext": 500,
      "paragraphContext": 500
    },

    "condensing": {
      "warnThreshold": 0.85,
      "minRetainPercent": 20,
      "summarizeThreshold": 100
    }
  }
}
```

---

## Error Handling

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `TEMPLATE_NOT_FOUND` | Prompt template doesn't exist | Check templateId |
| `TEMPLATE_PARSE_ERROR` | Template syntax error | Fix template |
| `MISSING_REQUIRED_CONTEXT` | Template requires context not provided | Provide required context |
| `PROMPT_EXCEEDS_LIMIT` | Even after condensing, prompt too large | Split into multiple calls or use larger model |
| `ESSENTIAL_EXCEEDS_LIMIT` | Essential content alone exceeds limit | Reduce essential content or use larger model |
| `UNKNOWN_MODEL` | Model not in config | Use default limits |
| `TOKENIZER_ERROR` | Tokenization failed | Fall back to word count estimate |

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Dev Team Gap Analysis (2026-01-20)

- **Template parser**: Choose templating engine (Handlebars, Mustache, custom)
- **Variable resolution**: Implement path resolution for nested context (`{{brand.voice.core}}`)
- **Caching**: Consider caching assembled prompts for repeated context combinations
- **Metrics**: Track template usage, context sizes, assembly performance

### PromptCondenser Integration

PromptBuilder delegates all validation and condensing to PromptCondenser:

```javascript
// PromptBuilder.build() final step:
const structured = structurePrompt(assembled, priorities);
const result = PromptCondenser.condense(structured, model);
return result.success ? result : throw new Error(result.error);
```

See [PromptCondenser](../_PromptCondenser/PromptCondenser.md) for token counting, priority system, and condensing strategies.

### Integration with Generation Stack

```javascript
// ThemeGenerator calls PromptBuilder:
const prompt = await PromptBuilder.buildThemePrompt({
  templateId: "theme/generate",
  model: "claude-3-haiku",
  brand: brand,
  audience: audience,
  targetOutcomes: outcomes
});

// AICaller receives validated prompt:
const response = await AICaller.generate(prompt.prompt, model);
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Load prompt templates |
| PromptCondenser | Validate token limits and condense when needed |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| ThemeGenerator | Theme generation prompts |
| OutlineGenerator | Outline generation prompts |
| TemplateEngine | Section rendering prompts |
| AICaller | Pre-validated prompts |

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Updated: 2026-01-20 - Delegated condensing to PromptCondenser module*
*Status: Placeholder*
