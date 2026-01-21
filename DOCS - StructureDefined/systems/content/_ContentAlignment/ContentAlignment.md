# ContentAlignment Module

> **Purpose:** Validate and grade generated content against original input context
> **Type:** Content Quality Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

ContentAlignment validates that generated content aligns with all original input fields: brand voice, tone, audience psychographics, framework elements, theme truths, and content structure requirements. It provides grading scores and identifies specific misalignments.

```
┌─────────────────────────────────────────────────────────────────┐
│                    CONTENT ALIGNMENT PIPELINE                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  INPUTS                          CONTENT                         │
│  ├─ Brand (voice, values)        Generated section/paragraph     │
│  ├─ Audience (psychographics)              │                     │
│  ├─ Theme (truths, beliefShifts)           │                     │
│  ├─ Framework (stages, prompts)            ↓                     │
│  ├─ Tone (merged tone notes)     ┌─────────────────┐            │
│  └─ Structure (section spec)     │ ContentAlignment │            │
│                                  └────────┬────────┘            │
│                                           │                      │
│                                           ↓                      │
│                             ┌─────────────────────────┐         │
│                             │   AlignmentReport       │         │
│                             │   ├─ overallScore       │         │
│                             │   ├─ categoryScores     │         │
│                             │   ├─ misalignments[]    │         │
│                             │   └─ suggestions[]      │         │
│                             └─────────────────────────┘         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Roles & Rules

### ContentAlignment DOES:
- Compare generated content against original input context
- Score alignment across multiple categories
- Identify specific misalignment points
- Suggest corrections for misalignments
- Track alignment patterns over time
- Flag content below quality thresholds

### ContentAlignment does NOT:
- Generate content (TemplateEngine does that)
- Fix content automatically (returns suggestions only)
- Build prompts (PromptBuilder does that)
- Make subjective quality judgments (only alignment checks)

### Boundaries:
- Read-only access to generated content
- Read-only access to input context
- Returns reports (never modifies content)
- Configurable thresholds per brand/content-type

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| **Core Validation** |
| `validate(content, context)` | content, AlignmentContext | AlignmentReport | Full validation against all context |
| `validateSection(section, context)` | section content, context | SectionAlignmentReport | Validate single section |
| `validateParagraph(paragraph, context)` | paragraph content, context | ParagraphAlignmentReport | Validate single paragraph |
| **Category Checks** |
| `checkBrandAlignment(content, brand)` | content, Brand | CategoryScore | Check brand voice/values alignment |
| `checkAudienceAlignment(content, audience)` | content, Audience | CategoryScore | Check audience language/level |
| `checkThemeAlignment(content, theme)` | content, Theme | CategoryScore | Check truth/beliefShift presence |
| `checkToneAlignment(content, tone)` | content, merged tone | CategoryScore | Check tone consistency |
| `checkFrameworkAlignment(content, framework, stage)` | content, Framework, stageId | CategoryScore | Check framework element coverage |
| `checkStructureAlignment(content, sectionSpec)` | content, SectionSpec | CategoryScore | Check structural requirements |
| **Grading** |
| `grade(content, context)` | content, AlignmentContext | Grade | Overall pass/fail with tier |
| `getThresholds(brandId, contentType)` | brandId, contentType | Thresholds | Get grading thresholds |

---

## Type Definitions

### AlignmentContext

```javascript
{
  // All original inputs used to generate content
  brand: Brand,
  audience: Audience,
  theme: Theme,
  framework?: Framework,
  frameworkStage?: FrameworkStage,
  contentTypeFramework?: ContentTypeFramework,
  paragraphStructure?: ParagraphStructure,
  sectionSpec?: SectionSpec,

  // Merged/derived context
  mergedTone: string,                // theme.tone + section.toneNotes

  // Content location
  sectionIndex?: number,
  paragraphIndex?: number,

  // Thresholds (optional, defaults from config)
  thresholds?: Thresholds
}
```

### AlignmentReport

```javascript
{
  overallScore: number,              // 0-100
  grade: 'A' | 'B' | 'C' | 'D' | 'F',
  passed: boolean,                   // Meets minimum threshold

  categoryScores: {
    brand: CategoryScore,
    audience: CategoryScore,
    theme: CategoryScore,
    tone: CategoryScore,
    framework: CategoryScore,
    structure: CategoryScore
  },

  misalignments: Misalignment[],     // Specific issues found
  suggestions: Suggestion[],          // How to fix issues

  metadata: {
    contentLength: number,
    checksPerformed: number,
    validatedAt: string              // ISO timestamp
  }
}
```

### CategoryScore

```javascript
{
  category: string,                  // e.g., "brand", "audience"
  score: number,                     // 0-100
  weight: number,                    // Category weight in overall score
  passed: boolean,

  checks: [
    {
      name: string,                  // e.g., "voice_core_present"
      passed: boolean,
      score: number,
      evidence?: string,             // Quote from content showing alignment
      issue?: string                 // Description of misalignment
    }
  ]
}
```

### Misalignment

```javascript
{
  category: string,                  // Which category
  severity: 'critical' | 'major' | 'minor',
  check: string,                     // Which check failed

  expected: string,                  // What was expected
  found: string,                     // What was found (or "not found")

  location?: {
    paragraph?: number,
    sentence?: number,
    quote?: string                   // Offending text
  },

  impact: string                     // Why this matters
}
```

### Suggestion

```javascript
{
  misalignmentRef: number,           // Index in misalignments array
  priority: 'high' | 'medium' | 'low',

  action: string,                    // What to do
  example?: string,                  // Example of correct approach

  context: {
    original: string,                // Original input being referenced
    field: string                    // Which field to align with
  }
}
```

### Grade

```javascript
{
  letter: 'A' | 'B' | 'C' | 'D' | 'F',
  score: number,
  tier: 'premium' | 'standard' | 'acceptable' | 'revision_needed' | 'reject',
  passed: boolean,

  breakdown: {
    [category: string]: number       // Score per category
  }
}
```

### Thresholds

```javascript
{
  // Minimum scores to pass
  overall: number,                   // Default: 70
  brand: number,                     // Default: 75
  audience: number,                  // Default: 70
  theme: number,                     // Default: 75
  tone: number,                      // Default: 65
  framework: number,                 // Default: 70
  structure: number,                 // Default: 80

  // Category weights (must sum to 1.0)
  weights: {
    brand: number,                   // Default: 0.20
    audience: number,                // Default: 0.15
    theme: number,                   // Default: 0.20
    tone: number,                    // Default: 0.10
    framework: number,               // Default: 0.15
    structure: number                // Default: 0.20
  },

  // Grade boundaries
  grades: {
    A: 90,                           // 90-100
    B: 80,                           // 80-89
    C: 70,                           // 70-79
    D: 60,                           // 60-69
    F: 0                             // 0-59
  }
}
```

---

## Alignment Checks by Category

### Brand Alignment

| Check | What It Validates | Weight |
|-------|-------------------|--------|
| `voice_core_present` | Core voice elements appear in language | 30% |
| `values_reflected` | Brand values implicit in content | 25% |
| `philosophy_consistent` | Content doesn't contradict brand philosophy | 20% |
| `terminology_correct` | Uses brand-specific terms correctly | 15% |
| `avoid_list_honored` | Doesn't use prohibited terms/approaches | 10% |

### Audience Alignment

| Check | What It Validates | Weight |
|-------|-------------------|--------|
| `cognitive_level_match` | Complexity matches audience level | 30% |
| `language_register` | Formality/tone appropriate for audience | 25% |
| `pain_points_addressed` | References audience pain points | 20% |
| `desired_outcome_direction` | Points toward audience goals | 15% |
| `engagement_triggers_used` | Uses positive engagement triggers | 10% |

### Theme Alignment

| Check | What It Validates | Weight |
|-------|-------------------|--------|
| `primary_truth_present` | Primary truth appears or is reinforced | 30% |
| `belief_shift_supported` | Content supports intended belief shift | 25% |
| `concept_coherent` | Stays on-topic with theme concept | 20% |
| `secondary_truths_integrated` | Secondary truths woven in appropriately | 15% |
| `keywords_natural` | Theme keywords used naturally | 10% |

### Tone Alignment

| Check | What It Validates | Weight |
|-------|-------------------|--------|
| `base_tone_consistent` | Maintains theme's base tone | 40% |
| `section_notes_applied` | Section-specific tone adjustments present | 35% |
| `transitions_smooth` | Tone doesn't shift jarringly | 25% |

### Framework Alignment

| Check | What It Validates | Weight |
|-------|-------------------|--------|
| `stage_purpose_met` | Section achieves framework stage goal | 35% |
| `prompts_addressed` | Framework prompts/questions answered | 30% |
| `elements_included` | Required framework elements present | 20% |
| `proprietary_lens_applied` | Brand interpretation used (if applicable) | 15% |

### Structure Alignment

| Check | What It Validates | Weight |
|-------|-------------------|--------|
| `section_type_correct` | Content matches section type (definition, mechanism, etc.) | 25% |
| `paragraph_structure_followed` | PEARL/SAFE/CER components present | 30% |
| `word_count_range` | Within specified word count | 15% |
| `component_order_correct` | Structure components in right order | 20% |
| `transition_present` | Has appropriate transition element | 10% |

---

## Severity Classification

| Severity | Criteria | Impact on Grade |
|----------|----------|-----------------|
| **Critical** | Brand voice violation, wrong audience level, contradicts theme truth | -20 to -30 points |
| **Major** | Missing framework element, structure incomplete, tone mismatch | -10 to -15 points |
| **Minor** | Keyword missing, suboptimal word choice, minor structure deviation | -3 to -5 points |

---

## Configuration

```json
{
  "module": "ContentAlignment",
  "version": "0.0.0",
  "description": "Validate content alignment with original context",
  "type": "content-quality",

  "config": {
    "defaultThresholds": {
      "overall": 70,
      "brand": 75,
      "audience": 70,
      "theme": 75,
      "tone": 65,
      "framework": 70,
      "structure": 80
    },

    "defaultWeights": {
      "brand": 0.20,
      "audience": 0.15,
      "theme": 0.20,
      "tone": 0.10,
      "framework": 0.15,
      "structure": 0.20
    },

    "gradeThresholds": {
      "A": 90,
      "B": 80,
      "C": 70,
      "D": 60,
      "F": 0
    },

    "qualityTiers": {
      "premium": { "minGrade": "A", "minScore": 90 },
      "standard": { "minGrade": "B", "minScore": 80 },
      "acceptable": { "minGrade": "C", "minScore": 70 },
      "revision_needed": { "minGrade": "D", "minScore": 60 },
      "reject": { "minGrade": "F", "minScore": 0 }
    }
  }
}
```

---

## Integration with Generation Pipeline

```
┌─────────────────────────────────────────────────────────────────┐
│                    POST-GENERATION VALIDATION                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  TemplateEngine.renderSection()                                 │
│           │                                                      │
│           ↓                                                      │
│  ┌─────────────────────┐                                        │
│  │ Generated Content   │                                        │
│  └──────────┬──────────┘                                        │
│             │                                                    │
│             ↓                                                    │
│  ContentAlignment.validate(content, originalContext)            │
│             │                                                    │
│             ├─── Grade A/B ─────→ Accept content                │
│             │                                                    │
│             ├─── Grade C ────────→ Accept with warnings         │
│             │                                                    │
│             └─── Grade D/F ──────→ Trigger regeneration         │
│                                    or flag for review            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Usage Example

```javascript
// After TemplateEngine generates a section
const content = await TemplateEngine.renderSection(sectionOutline, context);

// Validate alignment
const report = await ContentAlignment.validate(content, {
  brand: context.brand,
  audience: context.audience,
  theme: context.theme,
  framework: context.framework,
  frameworkStage: sectionOutline.frameworkStage,
  paragraphStructure: sectionOutline.paragraphStructure,
  sectionSpec: sectionOutline.sectionSpec,
  mergedTone: `${context.theme.tone} with ${sectionOutline.toneNotes}`
});

if (!report.passed) {
  // Handle misalignment
  if (report.grade === 'F') {
    // Regenerate with feedback
    return TemplateEngine.renderSection(sectionOutline, context, {
      feedback: report.suggestions
    });
  } else {
    // Log warnings but accept
    Logger.warn('Content alignment issues', report.misalignments);
  }
}

return { content, alignmentReport: report };
```

---

## Error Handling

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `MISSING_CONTEXT` | Required context field not provided | Return partial report with missing categories |
| `INVALID_CONTENT` | Content is empty or malformed | Return failed report |
| `THRESHOLD_NOT_FOUND` | Custom thresholds invalid | Use default thresholds |
| `CHECK_FAILED` | Individual check threw error | Skip check, note in report |

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Dev Team Gap Analysis (2026-01-20)

- **Check implementations**: Need NLP/embedding-based checks for semantic alignment
- **Baseline calibration**: Establish baseline scores from successful content
- **Learning**: Track which misalignments correlate with poor content performance
- **Custom checks**: Allow brands to add custom alignment checks
- **Batch validation**: Support validating entire outlines efficiently

### Alignment Check Approaches

1. **Keyword matching**: Check for presence of required terms
2. **Semantic similarity**: Embedding-based comparison to reference content
3. **Pattern matching**: Regex for structure verification
4. **LLM-based**: Use AI to evaluate subjective alignment (tone, voice)

### Performance Considerations

- Cache brand/audience embeddings for similarity checks
- Run checks in parallel where independent
- Skip expensive checks for draft content (full checks on final only)

---

## Dependencies

| Depends On | For |
|------------|-----|
| (none) | Stateless validation module |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Alignment scoring (runs in parallel with other checkers) |

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Updated: 2026-01-20 - Full validation and grading spec*
*Updated: 2026-01-20 - Now reports to PostCreationCheckManager*
*Status: Placeholder*
