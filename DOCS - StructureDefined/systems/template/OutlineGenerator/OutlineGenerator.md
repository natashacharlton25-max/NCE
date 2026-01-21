# OutlineGenerator Module

> **Purpose:** Pre-generate section-by-section outlines from theme + template for coherent content
> **Type:** Content Creation Module
> **Status:** Approved
> **Version:** 1.3.1

---

## Overview

OutlineGenerator creates structured outlines that bridge themes and content generation. It takes a theme (the WHAT) and a template (the STRUCTURE) and produces section-by-section plans (the HOW) that guide TemplateEngine's content generation.

```
Theme        = WHAT to say (truths, concept, message, keywords)
     ↓
Outline      = HOW to structure it (plan per section, which truths where)
     ↓
TemplateEngine = FULL content (complete paragraphs, rendered output)
```

### Why Outlines Matter

Without outlines, each section is generated independently - leading to:
- Repetition across sections
- Inconsistent truth distribution
- No narrative arc
- Disjointed documents

With outlines:
- Truths distributed intentionally across sections
- Coherent narrative flow
- Each section has clear purpose
- Pre-generation during quiet times saves cost

---

## Operational Guarantees

### OutlineGenerator MUST:
- Generate section plans scaled to content type complexity
- Reference theme truths by index for consistency
- Provide image keywords for each section with images
- Store outlines in SQLite for reuse
- Track usage for quality insights

### OutlineGenerator does NOT:
- Generate full content (TemplateEngine does that)
- Build prompts directly (PromptBuilder does that)
- Call AI directly (AICaller does that)
- Modify themes (ThemeGenerator does that)
- Modify templates (TemplateFactory does that)

### Boundaries:
- Read-only access to themes (via ThemeGenerator.get())
- Read-only access to templates (via TemplateEngine/TemplateFactory)
- Full access to own outlines.db database

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| **Lifecycle** |
| `initialize()` | - | initResult | Create database/tables if needed, validate schema |
| **Generation** |
| `generate(input)` | themeId, templateId, options | outline | AI generates outline for theme + template |
| `regenerate(outlineId, options?)` | outlineId, options | outline | Regenerate existing outline (new version) |
| `regenerateSection(outlineId, sectionIndex, options?)` | outlineId, index, options | outline | Regenerate single section (surgical edit) |
| **CRUD** |
| `get(outlineId)` | outlineId | outline | Load existing outline from database |
| `save(outline)` | outline | outlineId | Insert or update outline in database |
| `update(outlineId, updates)` | outlineId, partial outline | outline | Update specific fields |
| `delete(outlineId, options?)` | outlineId, { hard?: boolean } | result | Archive (default) or hard delete |
| **Query** |
| `list(filters?)` | filters | outlines[] | List outlines with optional filters |
| `findByThemeTemplate(themeId, templateId)` | themeId, templateId | outline or null | Find existing outline for theme + template combo |
| **Tracking** |
| `incrementUsage(outlineId)` | outlineId | result | Increment usage count (called by TemplateEngine) |
| `rate(outlineId, score, notes?)` | outlineId, 1-5, notes | result | Rate outline quality |

---

## Options Type Definitions

### GenerateInput

```javascript
{
  themeId: string,              // Required - which theme to use
  templateId: string,           // Required - which template structure

  // Optional overrides
  cognitiveTypeId?: string,     // Override cognitive type (defaults based on contentType)

  options?: {
    planDepth?: "minimal" | "standard" | "detailed",  // Override content type default
    autoSave?: boolean,         // default: true - save to database after generation
    status?: "draft" | "active" // default: "draft"
  }
}
```

### Outline

```javascript
{
  // === IDENTITY ===
  id: string,                   // e.g., "outline_acme_healthy-conversations_entrepreneur-workbook"
  name: string,                 // Human-readable name

  // === SOURCE REFERENCES ===
  themeId: string,              // Source theme ID
  templateId: string,           // Source template ID
  brandId?: string,             // Inherited from theme
  audienceId?: string,          // Inherited from theme
  contentType: string,          // Inherited from template

  // === TEMPLATE SNAPSHOT (drift protection) ===
  templateSnapshot: {
    version: string,            // Template version at generation time
    sectionCount: number,       // Number of sections when generated
    structureHash: string       // Hash of section types array for validation
  },

  // === CONTENT TYPE SNAPSHOT (drift protection) ===
  contentTypeSnapshot?: {
    id: string,                 // Content type ID at generation time
    version: string             // Version for drift detection
  },

  // === THEME SNAPSHOT (truth stability) ===
  themeSnapshot?: {
    version: string,            // Theme version at generation time
    truthCount: number,         // Number of truths when generated
    truthsHash: string          // Hash of truth IDs for reorder detection
  },

  // === PLANNING CONTEXT ===
  cognitiveTypeId?: string,     // Cognitive type used for structure
  planDepth: "minimal" | "standard" | "detailed",

  // === TRUTH STRATEGY (hybrid model) ===
  truthStrategy: {
    sequenceType: "progressive" | "complementary" | "hierarchical" | "standalone",
    selectedTruths: number[],   // Indices from theme's master truths (required, ≥1)
    selectedTruthIds?: string[], // Stable IDs for validation (parallel to indices)
    primaryTruth: number,       // Main focus truth index (must be in selectedTruths)
    primaryTruthId?: string,    // Stable ID for primary truth
    secondaryTruths: number[],  // Supporting truth indices (subset of selectedTruths)
    secondaryTruthIds?: string[], // Stable IDs for secondary truths
    localTruths?: LocalTruth[], // Optional 1-2 context-specific truths
  },

  // === SECTION OUTLINES ===
  sections: SectionOutline[],   // Ordered array matching template.sections

  // === GLOBAL IMAGE SETTINGS (document-level defaults) ===
  globalImageStyle?: string,    // Default style for all sections (e.g., "professional", "warm", "minimalist")
  globalImageMood?: string,     // Default mood for all sections (e.g., "inspiring", "calm", "energetic")
  // Note: Sections can override these, but should flag when doing so for visual consistency review

  // === METADATA ===
  createdAt: string,            // ISO timestamp
  updatedAt?: string,           // ISO timestamp of last modification
  createdBy: string,            // "ai" or userId

  // === TRACKING ===
  usageCount: number,           // Times used in content generation
  lastUsedAt?: string,          // ISO timestamp of last use

  // === STATUS ===
  status: "draft" | "active" | "archived",

  // === QUALITY ===
  quality?: {
    aiConfidence?: number,      // 0-1 score from AI response
    humanReviewed?: boolean,
    reviewedAt?: string,
    reviewedBy?: string
  },

  // === RATING ===
  rating?: {
    score: number,              // 1-5 stars
    ratedAt?: string,
    ratedBy?: string,
    notes?: string
  }
}
```

### LocalTruth

Context-specific truths that extend or complement the theme's master truths for a specific outline.

```javascript
{
  id: string,                   // "local_0" or "local_1" (max 2)
  statement: string,            // The local truth/principle
  application: string,          // How to apply in this content
  extendsTruthIndex?: number,   // If this extends a master truth, its index
  isContextSpecific: boolean,   // true if standalone local context

  // === EXTENSION MODE (when extendsTruthIndex is set) ===
  extensionMode?: "replace" | "append" | "nuance"
  // "replace" - Local truth replaces master truth in this section (use carefully)
  // "append"  - Local truth adds to master truth (both referenced)
  // "nuance"  - Local truth adds context/specificity without removing master (default)
}
```

**Rules:**
- Maximum 2 local truths per outline
- Must declare either `extendsTruthIndex` OR `isContextSpecific: true`
- If `extendsTruthIndex` set without `extensionMode`, defaults to "nuance"
- "replace" should be rare - risks breaking truth continuity in progressive sequences
- Prevents truth sprawl while allowing context-specific depth

### SectionOutline

**Architecture Note:** SectionOutline is a **composite planning artifact** that spans multiple framework layers:
- **Layer 1+2 aware**: `frameworkStageId`, `frameworkPrompts`, `frameworkLanguage`
- **Layer 3 aware**: `frameworkElements`, word count from content-type framework
- **Layer 4 aware**: `paragraphStructure`, `paragraphBreakdown`

This is intentional - SectionOutline is the **planning bridge** between abstract frameworks and concrete content generation. TemplateEngine consumes this composite to produce coherent output.

```javascript
{
  index: number,                // Position in template (0-based)
  type: string,                 // Section type from template (e.g., "title", "body", "exercise")

  // === CORE PLANNING ===
  plan: string,                 // 1-10 sentences depending on planDepth
  planLocked?: boolean,         // If true, regenerateSection() preserves plan text
                                // Protects manual edits from being overwritten
  keyPoints: string[],          // 3-5 bullet points for this section

  // === TRUTH REFERENCES (prioritized) ===
  truthsReferenced: number[],   // Indices from selectedTruths to weave in (0-based into master truths)
  truthsReferencedIds?: string[], // Stable IDs for validation (parallel to indices)
  truthPriority?: {             // Explicit prioritization to prevent "truth crowding"
    driver: number,             // The main truth that drives this section's argument
    driverId?: string,          // Stable ID for driver truth
    support: number[],          // Supporting truths for color/context only
    supportIds?: string[]       // Stable IDs for support truths
  },
  localTruthsReferenced?: string[],  // IDs of local truths to include (e.g., ["local_0"])

  // === TRANSFORMATION ALIGNMENT ===
  transformationStage?: string, // Maps to theme.transformationFramework beliefShifts
                                // Values: "current_belief_validation" | "destabilization" |
                                //         "insight" | "new_belief_installation" | "action"
                                // Ensures emotional arc matches logical structure

  // === NARRATIVE BRIDGING ===
  transitionOut?: string,       // How this section leads into the next
                                // e.g., "Pivot from the problem of anxiety to the concept of 'micro-rest'"
                                // TemplateEngine uses this to write cohesive opening sentences

  // === CONTENT GUIDANCE ===
  wordCountTarget?: {           // For body/long sections
    min: number,
    max: number
  },
  toneNotes?: string,           // Specific tone guidance for this section

  // === TYPE-SPECIFIC SUGGESTIONS ===
  suggestions?: {
    title?: string,             // For title sections
    subtitle?: string,          // For title sections
    cta?: string,               // For cta sections
    hookType?: string           // For intro sections
  },

  exerciseType?: string,        // For exercise sections (e.g., "reflection", "checklist", "fill-in")
  exercisePrompt?: string,      // Specific exercise instruction

  // === IMAGE GUIDANCE ===
  imageKeywords?: string[],     // Keywords for image search (Visual Nouns, Action Verbs)
  imageStyle?: string,          // Overrides outline.globalImageStyle for this section
  imageMood?: string,           // Overrides outline.globalImageMood for this section
  imageStyleOverride?: boolean, // Flag when style differs from global (for visual consistency review)
  imageComposition?: string,    // Composition guidance for image generators
                                // e.g., "close-up", "wide angle", "flat lay", "macro shot", "knolling"
                                // Improves image variety and prevents generic stock-photo looks

  // === REFINEMENT FLAG ===
  needsRefinement?: boolean     // true if section was flagged for regeneration
                                // Used by regenerateSection() to track which sections need work
}
```

### ListFilters

```javascript
{
  themeId?: string,             // Filter by theme
  templateId?: string,          // Filter by template
  brandId?: string,             // Filter by brand
  contentType?: string,         // Filter by content type
  status?: string | string[],   // Filter by status
  planDepth?: string,           // Filter by plan depth
  minUsageCount?: number,       // Minimum usage
  minRating?: number,           // Minimum rating (1-5)
  createdAfter?: string,        // ISO date
  createdBefore?: string,       // ISO date

  // Pagination
  limit?: number,               // default: 50, max: 200
  offset?: number,              // default: 0

  // Sorting
  sortBy?: "createdAt" | "usageCount" | "rating" | "name",
  sortOrder?: "asc" | "desc"    // default: "desc" for counts/dates/rating
}
```

---

## Detailed Function Specs

### initialize()

Create database and tables if needed, validate schema, run migrations.

```javascript
// Input
// (none)

// Process
1. Check database file exists at config.database.path
2. If not exists: create database file via DatabaseHandler
3. Create tables if not exist (outlines, outlines_fts)
4. Validate schema version matches expected
5. If schema outdated: run pending migrations from /migrations/outlines/
6. Return readiness report

// Output (success)
{
  success: true,
  database: "/data/outlines.db",
  outlineCount: 23,
  schemaVersion: "1.0.0",
  migrationsRun: 0
}

// Output (error)
{
  success: false,
  error: {
    code: "DATABASE_INIT_FAILED",
    message: "Could not create database at /data/outlines.db",
    cause: "Permission denied"
  }
}
```

---

### generate(input)

Generate an outline from theme + template.

```javascript
// Input
{
  themeId: "theme_acme_healthy-conversations",
  templateId: "template_entrepreneur-workbook",
  options: {
    planDepth: "standard",
    autoSave: true,
    status: "draft"
  }
}

// Process
1. Load theme via ThemeGenerator.get(themeId)
2. If theme not found: return THEME_NOT_FOUND error
3. Load template via DatabaseHandler (TemplateEngine's database) or FileManager
4. If template not found: return TEMPLATE_NOT_FOUND error
5. Load content type from /repos/content-types/ via FileManager
6. Determine planDepth: options.planDepth || contentType.defaultPlanDepth || "standard"
7. Load cognitive type if specified (or use contentType default)
8. Validate cognitive type compatibility with template structure
   - If incompatible: return COGNITIVE_TYPE_INCOMPATIBLE warning (level 1)
9. Load section specs for each template.sections[] via FileManager
10. Build prompt via PromptBuilder.buildOutlinePrompt({
      theme, template, contentType, cognitiveType, sectionSpecs, planDepth,
      transformationFramework: theme.transformationFramework  // Pass through for belief-shift alignment
    })
11. Call AICaller.generate(prompt)
12. Parse response into SectionOutline[] array
13. Validate all sections match template structure
14. Create templateSnapshot: { version, sectionCount, structureHash }
15. Validate truth dependencies:
    - If theme.truthRelationshipType === "progressive":
      - Check theme.truths[].dependsOn values
      - Ensure selectedTruths respects dependency chain
      - If violated: return OUTLINE_TRUTH_DEPENDENCY_VIOLATION error
16. Validate primary truth distribution:
    - Count body-type sections referencing primaryTruth
    - If < 50% of body sections: log warning (non-blocking)
17. If options.autoSave: save(outline)
18. Return outline

// Output (success)
{
  success: true,
  outline: {
    id: "outline_acme_healthy-conversations_entrepreneur-workbook",
    name: "Healthy Conversations - Entrepreneur Workbook",
    themeId: "theme_acme_healthy-conversations",
    templateId: "template_entrepreneur-workbook",
    brandId: "brand_acme",
    contentType: "workbook",
    planDepth: "standard",

    truthStrategy: {
      sequenceType: "progressive",
      selectedTruths: [0, 1, 2, 3, 4],  // Using all 5 master truths
      primaryTruth: 0,                   // "Self-care isn't selfish" as main focus
      secondaryTruths: [1, 2, 3, 4],     // Others support the primary
      localTruths: []                    // No local truths for this outline
    },

    sections: [
      {
        index: 0,
        type: "title",
        plan: "Open with an empowering question that challenges assumptions about workplace communication.",
        keyPoints: [
          "Frame healthy conversations as a skill, not a personality trait",
          "Promise transformation, not just information"
        ],
        truthsReferenced: [0],
        suggestions: {
          title: "The Art of Conversations That Matter",
          subtitle: "A Workbook for Entrepreneurs Who Want to Lead Through Dialogue"
        },
        imageKeywords: ["conversation", "connection", "dialogue", "professional meeting"]
      },
      {
        index: 1,
        type: "body",
        plan: "Establish the problem: most entrepreneurs avoid difficult conversations, leading to festering issues. Use relatable scenarios. Introduce the core framework.",
        keyPoints: [
          "Common avoidance patterns",
          "Cost of avoiding conversations",
          "Preview of the CLEAR framework"
        ],
        truthsReferenced: [0, 1],
        wordCountTarget: { min: 300, max: 500 },
        imageKeywords: ["challenge", "obstacle", "entrepreneur thinking"]
      },
      {
        index: 2,
        type: "body",
        plan: "Deep dive into the CLEAR framework components. Each letter gets explanation and example.",
        keyPoints: [
          "C - Clarify intent",
          "L - Listen actively",
          "E - Express with I-statements",
          "A - Ask questions",
          "R - Resolve together"
        ],
        truthsReferenced: [2, 3],
        wordCountTarget: { min: 400, max: 600 },
        imageKeywords: ["framework", "steps", "process", "methodology"]
      },
      {
        index: 3,
        type: "exercise",
        plan: "Reflection exercise: identify one conversation you've been avoiding and plan approach using CLEAR.",
        keyPoints: [
          "Identify the conversation",
          "Apply each CLEAR step",
          "Set timeline for action"
        ],
        truthsReferenced: [3],
        exerciseType: "reflection",
        exercisePrompt: "Think of a conversation you've been putting off. Use the space below to plan your approach."
      },
      {
        index: 4,
        type: "closing",
        plan: "Reinforce key message: healthy conversations are learnable. Encourage immediate action.",
        keyPoints: [
          "Summary of CLEAR",
          "Call to action: have one conversation this week",
          "Encouragement and support"
        ],
        truthsReferenced: [0, 4],
        imageKeywords: ["success", "achievement", "forward momentum"]
      }
    ],

    createdAt: "2026-01-19T14:30:00Z",
    createdBy: "ai",
    usageCount: 0,
    status: "draft",
    quality: {
      aiConfidence: 0.88,
      humanReviewed: false
    }
  },
  existing: false
}

// Output (existing found)
{
  success: true,
  outline: { ... },
  existing: true,
  message: "Existing outline found for this theme + template combination"
}
```

---

### get(outlineId)

Load an existing outline from database.

```javascript
// Input
"outline_acme_healthy-conversations_entrepreneur-workbook"

// Process
1. Query database for outline with ID
2. If not found: return OUTLINE_NOT_FOUND error
3. Parse JSON fields (sections, quality, rating)
4. Return outline object

// Output (success)
{
  success: true,
  outline: { ... full outline object ... }
}

// Output (not found)
{
  success: false,
  error: {
    code: "OUTLINE_NOT_FOUND",
    message: "Outline not found: outline_acme_healthy-conversations_entrepreneur-workbook"
  }
}
```

---

### findByThemeTemplate(themeId, templateId)

Find existing outline for a theme + template combination.

```javascript
// Input
{
  themeId: "theme_acme_healthy-conversations",
  templateId: "template_entrepreneur-workbook"
}

// Process
1. Query database: SELECT * FROM outlines WHERE theme_id = ? AND template_id = ? AND status != 'archived'
2. If found: return outline
3. If not found: return null (not an error)

// Output (found)
{
  success: true,
  outline: { ... },
  found: true
}

// Output (not found)
{
  success: true,
  outline: null,
  found: false
}
```

---

### incrementUsage(outlineId)

Increment usage count after successful content generation.

```javascript
// Input
"outline_acme_healthy-conversations_entrepreneur-workbook"

// Process
1. Update database: SET usage_count = usage_count + 1, last_used_at = NOW()
2. Return updated counts

// Output
{
  success: true,
  outlineId: "outline_acme_healthy-conversations_entrepreneur-workbook",
  usageCount: 5,
  lastUsedAt: "2026-01-19T15:00:00Z"
}
```

**Note:** Called by TemplateEngine after successful render, same pattern as ThemeGenerator.incrementUsage().

---

### regenerateSection(outlineId, sectionIndex, options?)

Regenerate a single section without affecting other sections. Enables "surgical" editing when most of an outline is good but one section needs work.

```javascript
// Input
{
  outlineId: "outline_acme_healthy-conversations_entrepreneur-workbook",
  sectionIndex: 2,  // 0-based index
  options: {
    reason?: string,            // Why regenerating (for AI context)
    preserveKeyPoints?: boolean, // Keep existing keyPoints, regenerate plan (default: false)
    alternateTone?: string       // Try different tone for this section
  }
}

// Process
1. Load outline via get(outlineId)
2. If not found: return OUTLINE_NOT_FOUND error
3. Validate sectionIndex is within bounds
4. If out of bounds: return SECTION_INDEX_OUT_OF_BOUNDS error
5. Load theme, template, content type (same as generate())
6. Check if section.planLocked === true:
   - If locked: AI regenerates keyPoints, tone, suggestions ONLY
   - The `plan` field is preserved exactly as-is
   - Log: "Plan locked - regenerating supporting elements only"
7. Build section-specific prompt via PromptBuilder.buildSectionRegeneratePrompt({
     theme, template, sectionSpec, existingSection: outline.sections[sectionIndex],
     surroundingContext: [outline.sections[sectionIndex-1], outline.sections[sectionIndex+1]],
     previousSectionTransitionOut: outline.sections[sectionIndex-1]?.transitionOut,
     options,
     preservePlan: existingSection.planLocked  // Instruct AI to keep plan, refine other fields
   })
8. Call AICaller.generate(prompt)
9. Parse response into new SectionOutline
10. If planLocked: merge new fields but keep original plan
11. Replace outline.sections[sectionIndex] with merged section
12. Clear needsRefinement flag on new section
13. Update outline.updatedAt
14. Save outline
15. Return updated outline

// Output (success)
{
  success: true,
  outline: { ... updated outline with new section ... },
  regeneratedSection: {
    index: 2,
    previousPlan: "Deep dive into the CLEAR framework...",
    newPlan: "Explore each CLEAR component through real-world scenarios..."
  }
}

// Output (error)
{
  success: false,
  error: {
    code: "SECTION_INDEX_OUT_OF_BOUNDS",
    message: "Section index 5 is out of bounds (outline has 5 sections, indices 0-4)"
  }
}
```

**Benefits:**
- Lower token costs than full regeneration
- Preserves good sections
- Enables iterative refinement
- Maintains narrative coherence (uses surrounding context)

---

### list(filters?)

List outlines with optional filters.

```javascript
// Input
{
  brandId: "brand_acme",
  status: "active",
  sortBy: "usageCount",
  limit: 20
}

// Process
1. Build SQL query with filters
2. Execute query
3. Parse JSON fields for each result
4. Return paginated results

// Output
{
  success: true,
  outlines: [ ... ],
  total: 45,
  limit: 20,
  offset: 0
}
```

---

## Sequence Types

The `sequenceType` in truthStrategy determines how truths relate to each other:

| Type | Description | Best For |
|------|-------------|----------|
| **progressive** | Each truth builds on previous (1→2→3 flow) | Educational content, skill-building, courses |
| **complementary** | Truths are different facets, no required order | Awareness content, social media, blog posts |
| **hierarchical** | Foundation → Advanced layering | Framework-teaching, methodology guides |
| **standalone** | Single truth focus (default for short content) | Social posts, quick tips, single-message emails |

**Validation:**
- `progressive` and `hierarchical`: must have valid dependency chains in section order
- `complementary`: truths should have minimal overlap
- `standalone`: typically uses only primaryTruth

---

## Transformation Stages

The `transformationStage` field in SectionOutline maps sections to the belief transformation journey defined in ThemeGenerator's `transformationFramework`.

### Stage Definitions

| Stage | Purpose | Typical Section Types |
|-------|---------|----------------------|
| **current_belief_validation** | Meet the reader where they are. Acknowledge their current belief without judgment. | intro, opening |
| **destabilization** | Create cognitive dissonance. Show why the current belief is incomplete or problematic. | body (early) |
| **insight** | Offer the "aha moment." Present the new perspective clearly. | body (middle) |
| **new_belief_installation** | Reinforce the new belief with evidence, stories, or frameworks. | body (late), exercise |
| **action** | Translate belief into behavior. Give concrete next steps. | closing, cta, exercise |

### Integration with Theme

```javascript
// Theme.transformationFramework contains:
{
  beliefShifts: [
    { from: "Difficult conversations should be avoided", to: "Difficult conversations build trust" }
  ],
  emotionalJourney: ["frustration", "curiosity", "hope", "confidence"]
}

// OutlineGenerator assigns stages to ensure sections map to this journey:
sections[0].transformationStage = "current_belief_validation"  // intro
sections[1].transformationStage = "destabilization"            // body 1
sections[2].transformationStage = "insight"                    // body 2
sections[3].transformationStage = "new_belief_installation"    // exercise
sections[4].transformationStage = "action"                     // closing
```

### Why This Matters

Without explicit stage assignment:
- AI may introduce the "new belief" too early (before reader is ready)
- The "insight" moment may appear in the closing (anticlimactic)
- Content becomes informational instead of transformational

---

## Tone Merging Strategy

SectionOutline includes `toneNotes` which interact with the Theme's global tone. This section defines the merge strategy.

### Tone Hierarchy

```
Theme.tone (global)
    ↓
SectionOutline.toneNotes (section-specific)
    ↓
PromptBuilder merges for final prompt
```

### Merge Rules

| Theme Tone | Section toneNotes | Result |
|------------|-------------------|--------|
| "Authoritative" | (none) | Authoritative |
| "Authoritative" | "Vulnerable" | "Authoritative but Vulnerable" - admitting mistakes to teach |
| "Warm" | "Direct" | "Warm but Direct" - caring with clear boundaries |
| "Professional" | "Playful" | "Professional with Playful moments" - lightness within structure |

### Implementation Note

PromptBuilder receives both values and constructs the merge:

```javascript
// PromptBuilder.buildSectionPrompt()
const mergedTone = section.toneNotes
  ? `${theme.tone} with ${section.toneNotes} elements`
  : theme.tone;
```

### Best Practices

- **Section toneNotes should modify, not contradict** - "Vulnerable" modifies "Authoritative," doesn't replace it
- **Use sparingly** - Most sections should inherit theme tone
- **Reserve for narrative beats** - Opening sections might be "Inviting," closings might be "Empowering"
- **Match content type** - Workbooks can handle more tone variety than social posts

---

## Cognitive Type Compatibility

Cognitive types often dictate structure (e.g., "Problem → Agitation → Solution"). Some combinations are incompatible with certain templates.

### Compatibility Matrix

| Cognitive Type | Compatible Templates | Incompatible Templates |
|---------------|---------------------|------------------------|
| Socratic Questioning | Workbook, Course, Guide | Checklist, Quick Reference |
| Problem-Agitation-Solution | Email, Landing Page, Blog | Top 10 List, Checklist |
| Story Arc | Ebook, Case Study, Presentation | Quick Reference, Checklist |
| Step-by-Step | Tutorial, Checklist, Guide | Narrative Blog, Opinion Piece |
| Compare-Contrast | Buyer Guide, Review, Whitepaper | Single-Product, Checklist |

### Validation Behavior

```javascript
// In generate() process step 8
if (cognitiveTypeId && !isCompatible(cognitiveType, template)) {
  return {
    success: false,
    error: {
      code: "COGNITIVE_TYPE_INCOMPATIBLE",
      level: 1,  // Warning, not critical
      message: `Cognitive type "${cognitiveType.name}" may not work well with template "${template.name}"`,
      suggestion: "Consider using a different cognitive type or template",
      proceed: true  // Can still proceed if user confirms
    }
  };
}
```

**Note:** This is a warning (level 1), not a hard failure. User can override if they have a specific creative intent.

---

## Template Drift Handling

When an outline is created, `templateSnapshot` captures the template's structure. If the template is later modified, drift is detected at job validation time.

### Architecture Overview

```
Content Job starts
    ↓
Validator checks outline ↔ template alignment
    ↓
OUTLINE_TEMPLATE_DRIFT_DETECTED (with severity in payload)
    ↓
FailureHandler receives error + log
    ↓
FailureHandler decides recovery strategy:
    ├─ minor → use archived template OR migrate outline
    ├─ major → generate fresh outline
    └─ executes recovery, retries job
```

**Key principle:** Validator detects, FailureHandler decides.

### Template Versioning

Templates are versioned so outlines don't break when templates change:

```
Template v1.0 (5 sections) ──→ Outline A (snapshotVersion: "1.0")
                           ──→ Outline B (snapshotVersion: "1.0")

Template v1.1 (6 sections) ──→ Outline C (snapshotVersion: "1.1")
                           ──→ NEW outlines use v1.1

Outline A + Template v1.1 = FailureHandler decides:
  1. Use Template v1.0 (archived version) - works as-is
  2. Generate new outline against v1.1 - fresh start
  3. Migrate outline: add section to match v1.1 - surgical update
```

### Detection Logic

```javascript
// Validator (pre-flight check at job start)
function detectTemplateDrift(outline, currentTemplate) {
  const snapshot = outline.templateSnapshot;
  const currentHash = hashSectionTypes(currentTemplate.sections);

  return {
    versionChanged: snapshot.version !== currentTemplate.version,
    sectionCountChanged: snapshot.sectionCount !== currentTemplate.sections.length,
    structureChanged: snapshot.structureHash !== currentHash,
    severity: calculateSeverity(snapshot, currentTemplate)  // "none" | "minor" | "major"
  };
}
```

### Drift Severity & Recovery Strategy

| Scenario | Severity | FailureHandler Strategy |
|----------|----------|------------------------|
| Snapshot matches current template | none | Proceed normally |
| Version bump only (cosmetic) | minor | Proceed with warning |
| 1 section added/removed | minor | Option: migrate outline OR use archived template v1.0 |
| Multiple sections changed | major | Generate fresh outline against current template |
| Template restructured | major | Generate fresh outline (no migration possible) |
| No outline exists | — | Generate new outline against current template |

### Error Payload

```javascript
// Validator returns this to FailureHandler
{
  success: false,
  error: {
    code: "OUTLINE_TEMPLATE_DRIFT_DETECTED",
    level: 2,
    message: "Template has changed since outline was generated",
    drift: {
      severity: "minor",  // FailureHandler uses this to decide
      versionChanged: true,
      sectionCountChanged: true,
      structureChanged: false,
      originalSections: 5,
      currentSections: 6,
      originalVersion: "1.0.0",
      currentVersion: "1.1.0"
    },
    recoveryOptions: [
      { strategy: "use_archived_template", available: true },
      { strategy: "migrate_outline", available: true },
      { strategy: "generate_fresh", available: true }
    ]
  }
}
```

### FailureHandler Recovery Actions

| Strategy | What FailureHandler Does |
|----------|--------------------------|
| `use_archived_template` | Load Template v1.0, proceed with existing outline as-is |
| `migrate_outline` | Call `OutlineGenerator.regenerateSection()` to add missing section(s) |
| `generate_fresh` | Call `OutlineGenerator.generate()` with current template |

### Design Decision: Why FailureHandler Owns This

1. **Validator stays simple** - detects drift, reports error with details
2. **FailureHandler has context** - error details, severity, job metadata
3. **Centralized recovery logic** - consistent handling across all job types
4. **No mass-update required** - existing outlines don't need migration when templates change

---

## Plan Depth by Content Type

| Content Type | Default Depth | Plan Length | Key Points |
|--------------|---------------|-------------|------------|
| social-post | minimal | 1-2 sentences | 2-3 |
| email | minimal | 2-3 sentences | 3-4 |
| blog-post | standard | 3-5 sentences | 4-5 |
| newsletter | standard | 3-4 sentences | 3-4 |
| workbook | standard | 4-6 sentences | 4-5 |
| whitepaper | detailed | 6-8 sentences | 5-6 |
| ebook | detailed | 6-10 sentences | 5-7 |
| course-module | detailed | 5-8 sentences | 5-6 |

Content types define their defaults in `/repos/content-types/`.

---

## Configuration

```json
{
  "module": "OutlineGenerator",
  "version": "1.0.0",
  "description": "Pre-generate section outlines from theme + template",
  "type": "content-creation",

  "config": {
    "database": {
      "path": "/data/outlines.db",
      "schemaVersion": "1.0.0"
    },

    "defaults": {
      "planDepth": "standard",
      "autoSave": true,
      "status": "draft"
    },

    "limits": {
      "maxSections": 20,
      "maxKeyPointsPerSection": 7,
      "maxTruthsPerSection": 3
    },

    "ai": {
      "defaultModel": "claude-3-haiku",
      "temperature": 0.7
    }
  }
}
```

### Config Explained

| Setting | Value | Purpose |
|---------|-------|---------|
| `database.path` | /data/outlines.db | SQLite database for outlines |
| `defaults.planDepth` | standard | Default if not specified and no content type default |
| `defaults.autoSave` | true | Auto-save generated outlines |
| `limits.maxSections` | 20 | Maximum sections per outline |
| `limits.maxKeyPointsPerSection` | 7 | Maximum key points per section |
| `limits.maxTruthsPerSection` | 3 | Maximum truth references per section |
| `ai.defaultModel` | claude-3-haiku | Model for outline generation |
| `ai.temperature` | 0.7 | Creativity level for planning |

---

## SQLite Schema

```sql
-- Main outlines table
CREATE TABLE outlines (
  id TEXT PRIMARY KEY,                    -- e.g., "outline_acme_healthy-conversations_entrepreneur-workbook"
  name TEXT NOT NULL,                     -- Human-readable name

  -- Source references
  theme_id TEXT NOT NULL,                 -- Source theme ID
  template_id TEXT NOT NULL,              -- Source template ID
  brand_id TEXT,                          -- Inherited from theme
  audience_id TEXT,                       -- Inherited from theme
  content_type TEXT NOT NULL,             -- Inherited from template

  -- Template snapshot (drift protection)
  template_snapshot TEXT NOT NULL,        -- JSON: { version, sectionCount, structureHash }

  -- Planning context
  cognitive_type_id TEXT,                 -- Cognitive type used
  plan_depth TEXT NOT NULL DEFAULT 'standard',

  -- Truth strategy (JSON object)
  truth_strategy TEXT NOT NULL,           -- JSON: { sequenceType, selectedTruths, primaryTruth, secondaryTruths, localTruths }

  -- Section outlines (JSON array)
  sections TEXT NOT NULL,                 -- JSON array of SectionOutline objects

  -- Metadata
  created_at TEXT DEFAULT (datetime('now')),
  updated_at TEXT,
  created_by TEXT NOT NULL DEFAULT 'ai',

  -- Tracking
  usage_count INTEGER DEFAULT 0,
  last_used_at TEXT,

  -- Status
  status TEXT NOT NULL DEFAULT 'draft',

  -- Global image settings (document-level defaults)
  global_image_style TEXT,                -- Default style for all sections
  global_image_mood TEXT,                 -- Default mood for all sections

  -- Quality (nullable JSON)
  quality TEXT,                           -- JSON: { aiConfidence, humanReviewed, ... }

  -- Rating (nullable JSON)
  rating TEXT,                            -- JSON: { score, ratedAt, ratedBy, notes }

  -- Constraints
  UNIQUE(theme_id, template_id, status)   -- One active outline per theme+template
);

-- Full-text search index
CREATE VIRTUAL TABLE outlines_fts USING fts5(
  id,
  name,
  theme_id,
  template_id,
  content_type,
  content='outlines',
  content_rowid='rowid'
);

-- Trigger to keep FTS in sync on INSERT
CREATE TRIGGER outlines_ai AFTER INSERT ON outlines BEGIN
  INSERT INTO outlines_fts(rowid, id, name, theme_id, template_id, content_type)
  VALUES (new.rowid, new.id, new.name, new.theme_id, new.template_id, new.content_type);
END;

-- Trigger to keep FTS in sync on UPDATE
CREATE TRIGGER outlines_au AFTER UPDATE ON outlines BEGIN
  DELETE FROM outlines_fts WHERE rowid = old.rowid;
  INSERT INTO outlines_fts(rowid, id, name, theme_id, template_id, content_type)
  VALUES (new.rowid, new.id, new.name, new.theme_id, new.template_id, new.content_type);
END;

-- Trigger to keep FTS in sync on DELETE
CREATE TRIGGER outlines_ad AFTER DELETE ON outlines BEGIN
  DELETE FROM outlines_fts WHERE rowid = old.rowid;
END;

-- Indexes for common queries
CREATE INDEX idx_outlines_theme_id ON outlines(theme_id);
CREATE INDEX idx_outlines_template_id ON outlines(template_id);
CREATE INDEX idx_outlines_brand_id ON outlines(brand_id);
CREATE INDEX idx_outlines_content_type ON outlines(content_type);
CREATE INDEX idx_outlines_status ON outlines(status);
CREATE INDEX idx_outlines_usage_count ON outlines(usage_count);
```

### Schema Migrations

When schema changes:
1. **Backup first**: DatabaseHandler creates `outlines.db.bak` before any migration
2. DatabaseHandler manages migrations via version tracking
3. Migrations are forward-only (no downgrades)
4. Migrations run automatically on `initialize()` if needed
5. Migration scripts stored in `/migrations/outlines/`
6. On failure: restore from backup, log error, return DATABASE_MIGRATION_FAILED

---

## Error Handling

| Error Code | Level | Cause | Recovery |
|------------|-------|-------|----------|
| `OUTLINE_NOT_FOUND` | 3 | Outline ID doesn't exist | Check outlineId spelling |
| `THEME_NOT_FOUND` | 3 | ThemeGenerator.get() failed | Check themeId or generate theme first |
| `TEMPLATE_NOT_FOUND` | 3 | Template doesn't exist | Check templateId spelling |
| `CONTENT_TYPE_NOT_FOUND` | 3 | Content type not in /repos/content-types/ | Add content type definition |
| `COGNITIVE_TYPE_NOT_FOUND` | 2 | Cognitive type not found | Check cognitiveTypeId or omit |
| `COGNITIVE_TYPE_INCOMPATIBLE` | 1 | Cognitive type doesn't suit template structure | Use different type or proceed with caution |
| `SECTION_SPEC_NOT_FOUND` | 2 | Section type has no spec | Add spec to /repos/section-specs/ |
| `OUTLINE_ALREADY_EXISTS` | 2 | Active outline exists for theme+template | Use existing or regenerate |
| `AI_GENERATION_FAILED` | 3 | AICaller failed to generate | Check AICaller logs, retry |
| `INVALID_OUTLINE_STRUCTURE` | 3 | Outline missing required fields | Check outline object structure |
| `SECTION_COUNT_MISMATCH` | 3 | Generated sections don't match template | Regenerate or check template |
| `TEMPLATE_DRIFT_DETECTED` | 2 | Template structure changed since outline generation | Regenerate outline or use archived template |
| `SECTION_INDEX_OUT_OF_BOUNDS` | 3 | regenerateSection() index invalid | Check section count |
| `OUTLINE_TRUTH_DEPENDENCY_VIOLATION` | 2 | Selected truths violate dependency chain | Include required dependent truths |
| `PRIMARY_TRUTH_UNDERREPRESENTED` | 1 | Primary truth in <50% of body sections | Redistribute truths or accept as-is |
| `INVALID_RATING` | 2 | Rating score not 1-5 | Provide valid score |
| `INVALID_UPDATE` | 3 | Tried to update immutable field | Can't change id, createdAt, createdBy |
| `DATABASE_INIT_FAILED` | 3 | Could not create/initialize database | Check permissions, disk space |
| `DATABASE_MIGRATION_FAILED` | 3 | Schema migration failed | Check migration scripts, rollback |
| `MODULE_NOT_READY` | 3 | Dependencies not initialized | Call initialize() on startup |

**Error Levels:**
- **Level 1** (Warning): Non-blocking, can proceed. Log and continue.
- **Level 2** (Recoverable): Requires attention but workarounds exist.
- **Level 3** (Critical): Operation cannot proceed.

---

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| SQLite storage | Yes | Consistency with ThemeGenerator, enables pre-generation and reuse |
| Composite ID | theme + template slug | Unique per combination, human-readable |
| Hybrid truth model | Yes | Outline selects subset from theme's master truths + optional local truths |
| Sequence types | 4 types | progressive, complementary, hierarchical, standalone - guides narrative flow |
| Primary/secondary truths | Yes | Clear hierarchy: one main focus, others support |
| Local truths (max 2) | Yes | Context-specific depth without truth sprawl |
| Local truth extensionMode | 3 modes | "replace", "append", "nuance" - explicit control over master truth relationship |
| Plan depth scaling | By content type | Social posts need less planning than workbooks |
| Image keywords per section | Yes | Each section may need different imagery |
| Image composition hints | Yes | Prevents generic stock-photo looks, improves variety |
| One active per theme+template | UNIQUE constraint | Prevents confusion about which outline to use |
| Usage tracking | Yes | Identify high-value outlines |
| Rating system | Yes | Track quality for future improvements |
| Template snapshot | Yes | Prevents "template drift" - outline remains valid even if source template changes |
| Section-level regeneration | Yes | Lower cost, better UX - fix one section without losing others |
| Truth dependency enforcement | Yes | Respects progressive sequence ordering from ThemeGenerator |
| Primary truth weighting | 50% minimum | Ensures primary truth anchors narrative, not just "item #1" |
| Tone merge strategy | Section modifies theme | toneNotes complement (not replace) global tone |
| Cognitive type validation | Warning only | Incompatible types warn but don't block (creative override allowed) |
| TransformationFramework pass-through | Yes | Sections align with belief-shift goals, not just truth distribution |
| Transformation stages | Explicit mapping | Each section tagged with stage in belief transformation journey |
| Narrative bridging | transitionOut field | Explicit plan for how sections connect - prevents "listicle" feel |
| Truth prioritization | driver + support | Prevents "truth crowding" - one truth drives, others add flavor |
| Plan locking | planLocked field | Protects manual edits during regenerateSection() |
| Global image settings | Document-level defaults | Ensures visual consistency - sections override only when needed |
| Template drift handling | Validator detects, FailureHandler recovers | Validator reports severity; FailureHandler chooses strategy (archived template / migrate / fresh) |
| Template versioning support | Yes | Outlines can use archived template versions - no mass-update when templates change |

### FTS Index Synchronization

Full-text search index is kept in sync with main table:

```javascript
// Fields that trigger FTS refresh: id, name, theme_id, template_id, content_type
// Fields that do NOT trigger FTS refresh: sections, rating, usageCount, status
```

### Integration with TemplateEngine

```javascript
// TemplateEngine.run() uses outline like this:
1. If context.outlineId provided:
   - Load outline via OutlineGenerator.get(outlineId)
   - Pass outline.sections[index] to each section's prompt
2. If no outlineId but themeId + templateId:
   - Optionally auto-generate: OutlineGenerator.generate({ themeId, templateId })
3. After successful render:
   - Call OutlineGenerator.incrementUsage(outlineId)
```

---

## Consumers (Who Uses This)

| Consumer | Function | Purpose |
|----------|----------|---------|
| **TemplateEngine** | get() | Load outline for content generation |
| **TemplateEngine** | incrementUsage() | Track successful usage |
| **Orchestrator** | generate() | Pre-generate outlines during quiet times |
| **ContentManager** | list(), rate() | Browse and rate outlines |

---

## Dependencies

| Depends On | For |
|------------|-----|
| DatabaseHandler | Direct access to outlines.db (own database) |
| ThemeGenerator | Load theme context via get() |
| FileManager | Read templates, content types, section specs, cognitive types |
| PromptBuilder | Build outline generation prompt |
| AICaller | Generate outline from theme + template |

**Data Access Pattern:**
- **Own database**: Direct via DatabaseHandler (OutlineGenerator → outlines.db)
- **Other modules**: Via their public APIs (ThemeGenerator.get())
- **Repos**: Via FileManager (content-types, section-specs, cognitive-types)

---

## Build Notes

*Notes for implementation and future updates:*

### Version Compatibility (Dev Team Gap - 2026-01-20)
| Dependency | Min Version | Notes |
|------------|-------------|-------|
| outlines.library | 1.0.0+ | Library schema must match Outline type |
| themes.library | 1.0.0+ | Theme with truths[] for truth selection |
| templates.library | 1.0.0+ | Template structure for sections |
| section-specs | 1.0.0+ | Section type definitions |

### Cross-Library Integrity
- When theme archived: Outlines using it become orphaned (ref_status='stale')
- When template archived: Outlines using it become orphaned
- templateSnapshot enables drift detection between outline and template versions

### Framework Population Integration (2026-01-20)

OutlineGenerator maps framework stages to section sequences (Layer 1 + Layer 2 → Outline).

```
┌─────────────────────────────────────────────────────────────────┐
│                 FRAMEWORK POPULATION IN OUTLINEGENERATOR         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Load theme.frameworkIds                                     │
│     (Evidence-based + proprietary frameworks from ThemeGenerator)│
│                                                                  │
│  2. Load content-type framework for template.contentType        │
│     (Section sequence + required elements from Layer 3)         │
│                                                                  │
│  3. For each framework in theme.frameworkIds:                   │
│     a. Load framework.structure.stages                          │
│     b. Load framework_interpretations (if proprietary exists)   │
│     c. Map stages to section sequence                           │
│                                                                  │
│  4. Populate SectionOutline with framework context:             │
│     - frameworkStageId: which stage this section represents     │
│     - frameworkPrompts: stage-specific prompts/questions        │
│     - frameworkLanguage: proprietary interpretation language    │
│                                                                  │
│  5. Store framework mapping in outline:                         │
│     outline.frameworkMapping = {                                │
│       primaryFrameworkId: string,                               │
│       stageToSectionMap: { stageId: sectionIndex },             │
│       interpretationApplied: boolean                            │
│     }                                                           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**SectionOutline Extension (Framework-Related):**
```javascript
{
  // ... existing SectionOutline fields ...

  // Framework stage mapping (populated from Layer 1 + 2)
  frameworkStageId?: string,          // e.g., "concrete_experience"
  frameworkPrompts?: string[],        // Stage-specific prompts from framework
  frameworkLanguage?: {
    interpretationApplied: boolean,   // True if proprietary interpretation used
    proprietaryTerms?: Record<string, string>,  // Term mappings from interpretation
    stageRename?: string              // Proprietary name for this stage (if different)
  },

  // Framework-driven element requirements
  frameworkElements?: {
    required: string[],               // Elements mandated by framework stage
    recommended: string[],            // Elements suggested by framework stage
    wordCountGuidance?: number        // Word count from content-type framework
  }
}
```

**Dependencies:**
- Frameworks Library (read): Load framework stages and structure
- Content-Type Frameworks Library (read): Load section sequence and word counts
- Framework Interpretations (read): Apply proprietary lens to evidence framework

**Handoff to TemplateEngine:**
- TemplateEngine receives `section.frameworkStageId` and `section.frameworkPrompts`
- TemplateEngine uses framework context to guide section content generation
- TemplateEngine validates required framework elements are present

### Paragraph Structure Assignment (Layer 4 - 2026-01-20)

OutlineGenerator assigns paragraph structures to each section for Layer 4 integration:

```
┌─────────────────────────────────────────────────────────────────┐
│             PARAGRAPH STRUCTURE ASSIGNMENT FLOW                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. For each section in outline:                                │
│     a. Lookup section type → default paragraph structure        │
│        (e.g., "mechanism" → PEARL, "reflection" → SAFE)         │
│     b. Check if framework stage has structure preference        │
│     c. Load cognitive level to select appropriate variant       │
│                                                                  │
│  2. Store in SectionOutline:                                    │
│     section.paragraphStructure = "pearl"                        │
│     section.expectedParagraphs = 4                              │
│     section.paragraphBreakdown = [...]                          │
│                                                                  │
│  3. Handoff to TemplateEngine for rendering                     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**SectionOutline Extension (Paragraph Structure):**
```javascript
{
  // ... existing SectionOutline fields ...

  // Paragraph structure assignment (Layer 4)
  paragraphStructure?: string,          // e.g., "pearl", "safe", "cer"
  expectedParagraphs?: number,          // How many paragraphs in this section
  paragraphBreakdown?: Array<{
    paragraphNum: number,
    focus: string,                      // What this paragraph covers
    structure: string                   // Which structure to use (may vary within section)
  }>
}
```

**Default Structure Mapping:**

| Section Type | Primary Structure | Alternative |
|--------------|-------------------|-------------|
| `definition` | PEARL | SAFE |
| `mechanism` | PEARL | CER |
| `recognition` | PEARL | SAFE |
| `self_mapping` | SAFE | PEARL |
| `reflection_before` | SAFE | — |
| `reflection_after` | SAFE | — |
| `experiential_exercise` | SAFE | — |
| `response_repertoire` | PEARL | — |
| `practice_protocol` | PEARL | — |
| `summary` | PEARL | — |

**Dependencies:**
- Paragraph Structures Library (read): Load structure definitions and cognitive variants

### Drift Detection (2026-01-20)

Three sources of drift require snapshot protection:

**A. Truth Index Stability**

Risk: If theme truths are reordered, indices silently break meaning.

Mitigation:
- `truthStrategy.selectedTruthIds[]` parallel to `selectedTruths[]`
- `SectionOutline.truthsReferencedIds[]` parallel to `truthsReferenced[]`
- `themeSnapshot.truthsHash` for reorder detection

Validator can sanity-check indices against IDs at job start.

**B. Content Type Drift**

Risk: Template declares contentType, but `/repos/content-types/` changes defaultPlanDepth or framework assumptions.

Mitigation:
- `contentTypeSnapshot: { id, version }` captures state at generation time
- Validator detects version mismatch, FailureHandler decides recovery

**C. SectionOutline Scope Creep**

SectionOutline now includes Layer 3 + Layer 4 fields:
- `frameworkStageId`, `frameworkPrompts`, `frameworkLanguage`, `frameworkElements`
- `paragraphStructure`, `paragraphBreakdown`

This is intentional - SectionOutline is the **composite planning artifact** bridging abstract frameworks to concrete generation. Document this clearly to prevent confusion.

---

*Spec version: 1.4.0*
*Created: 2026-01-19*
*Updated: 2026-01-19 - v1.2.0: regenerateSection(), templateSnapshot, imageComposition, needsRefinement, extensionMode for localTruths, tone merging strategy, cognitive type compatibility, truth dependency enforcement, transformationFramework pass-through*
*Updated: 2026-01-19 - v1.3.0: transitionOut (narrative bridging), transformationStage (belief journey mapping), truthPriority (driver/support), planLocked, globalImageStyle/globalImageMood*
*Updated: 2026-01-19 - v1.3.1: Template drift architecture - Validator detects, FailureHandler recovers (use_archived_template / migrate_outline / generate_fresh)*
*Updated: 2026-01-20 - v1.4.0: Drift protection (truthIds, contentTypeSnapshot, themeSnapshot), SectionOutline documented as composite Layer 3+4 artifact*
*Status: Approved*
