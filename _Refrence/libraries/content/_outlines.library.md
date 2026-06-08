# Outlines Library

> **System:** Content
> **Repository:** Content Repository
> **Path:** `/data/outlines.sqlite`
> **Library Type:** generated
> **Status:** Draft
> **Aligned With:** OutlineGenerator v1.4.0

---

## Overview

AI-generated content outlines that bridge themes and content generation. An outline takes a theme (the WHAT) and a template (the STRUCTURE) and produces section-by-section plans (the HOW) that guide TemplateEngine's content generation.

```
Theme        = WHAT to say (truths, concept, message, keywords)
     ↓
Outline      = HOW to structure it (plan per section, which truths where)
     ↓
TemplateEngine = FULL content (complete paragraphs, rendered output)
```

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `OutlineGenerator` | Read/Write (Owner) | Generate and store outlines |
| `TemplateEngine` | Read via Librarian | Outline for content generation |
| `ContentManager` | Read via Librarian | Browse and rate outlines |

---

## SQLite Schema

### Tables

```sql
-- Lightweight index for fast listing
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,                          -- JSON array
  preview JSON,                       -- Key fields for UI
  ref_status TEXT DEFAULT 'valid',    -- 'valid', 'stale', 'orphaned'
  review_status TEXT DEFAULT 'pending' -- 'pending', 'approved', 'rejected'
);

-- Full versioned data
CREATE TABLE outlines (
  id TEXT,
  version INTEGER,
  data JSON,                          -- Full outline as JSON
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,                    -- 'ai' or userId
  PRIMARY KEY (id, version)
);

-- Latest version view
CREATE VIEW outlines_latest AS
SELECT * FROM outlines t1
WHERE version = (SELECT MAX(version) FROM outlines t2 WHERE t2.id = t1.id);

-- Full-text search
CREATE VIRTUAL TABLE outlines_fts USING fts5(
  id,
  name,
  theme_id,
  template_id,
  content_type,
  content='outlines',
  content_rowid='rowid'
);
```

### Alternative Flat Schema (for direct querying)

```sql
CREATE TABLE outlines (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,

  -- Source references
  theme_id TEXT NOT NULL,
  template_id TEXT NOT NULL,
  brand_id TEXT,                      -- Inherited from theme
  audience_id TEXT,                   -- Inherited from theme
  content_type TEXT NOT NULL,         -- Inherited from template

  -- Template snapshot (drift protection)
  template_snapshot TEXT NOT NULL,    -- JSON: { version, sectionCount, structureHash }

  -- Content type snapshot (drift protection - v1.4)
  content_type_snapshot TEXT,         -- JSON: { id, version }

  -- Theme snapshot (truth stability - v1.4)
  theme_snapshot TEXT,                -- JSON: { version, truthCount, truthsHash }

  -- Planning context
  cognitive_type_id TEXT,
  plan_depth TEXT NOT NULL DEFAULT 'standard',

  -- Truth strategy (v1.4: includes *Ids fields for stability)
  truth_strategy TEXT NOT NULL,       -- JSON: { sequenceType, selectedTruths, selectedTruthIds?, primaryTruth, primaryTruthId?, secondaryTruths, secondaryTruthIds?, localTruths }

  -- Section outlines
  sections TEXT NOT NULL,             -- JSON array of SectionOutline objects

  -- Global image settings
  global_image_style TEXT,
  global_image_mood TEXT,

  -- Framework mapping (2026-01-20)
  framework_mapping TEXT,              -- JSON: { primaryFrameworkId, stageToSectionMap, interpretationApplied }

  -- Metadata
  created_at TEXT DEFAULT (datetime('now')),
  updated_at TEXT,
  created_by TEXT NOT NULL DEFAULT 'ai',

  -- Tracking
  usage_count INTEGER DEFAULT 0,
  last_used_at TEXT,

  -- Status
  status TEXT NOT NULL DEFAULT 'draft',

  -- Quality (nullable JSON)
  quality TEXT,

  -- Rating (nullable JSON)
  rating TEXT,

  -- Constraints
  UNIQUE(theme_id, template_id, status)
);

-- Indexes
CREATE INDEX idx_outlines_theme_id ON outlines(theme_id);
CREATE INDEX idx_outlines_template_id ON outlines(template_id);
CREATE INDEX idx_outlines_brand_id ON outlines(brand_id);
CREATE INDEX idx_outlines_content_type ON outlines(content_type);
CREATE INDEX idx_outlines_status ON outlines(status);
CREATE INDEX idx_outlines_usage_count ON outlines(usage_count);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Outline.ts`
> Aligned with: OutlineGenerator v1.3.1 Outline type

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "outline_acme_healthy-conversations_workbook") |
| `name` | string | Essential | Human-readable outline name |
| `themeId` | string | Essential | Source theme ID |
| `templateId` | string | Essential | Source template ID |
| `contentType` | string | Essential | Inherited from template |
| `planDepth` | enum | Essential | 'minimal' / 'standard' / 'detailed' |
| `truthStrategy` | TruthStrategy | Essential | Truth selection and sequencing |
| `sections` | SectionOutline[] | Essential | Ordered section array |
| `createdAt` | timestamp | Essential | Creation timestamp |
| `createdBy` | string | Essential | 'ai' or userId |
| `status` | EntryStatus | Essential | Lifecycle state |

### Inherited Fields (from Theme/Template)

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `brandId` | string / null | Supporting | Inherited from theme |
| `audienceId` | string / null | Supporting | Inherited from theme |

### Optional Outline-Level Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `cognitiveTypeId` | string / null | Supporting | Cognitive type used for structure |
| `globalImageStyle` | string / null | Supporting | Default image style for all sections |
| `globalImageMood` | string / null | Supporting | Default image mood for all sections |
| `frameworkMapping` | FrameworkMapping / null | Supporting | Framework integration metadata |

### Framework Mapping Object

| Field | Type | Description |
|-------|------|-------------|
| `primaryFrameworkId` | string | Main framework driving structure |
| `stageToSectionMap` | object | Maps framework stageId to section index |
| `interpretationApplied` | boolean | Whether proprietary interpretation was used |

### Template Snapshot Object

> Captures template structure at generation time for drift protection.

| Field | Type | Description |
|-------|------|-------------|
| `version` | string | Template version when outline was generated |
| `sectionCount` | number | Number of sections in template |
| `structureHash` | string | Hash of section types array |

**Hash Algorithm:**
```javascript
// structureHash = SHA256 of stringified section types array
structureHash = SHA256(JSON.stringify(template.sections.map(s => s.type)))
// Example: SHA256('["title","body","body","exercise","closing"]')
```

### Truth Strategy Object

| Field | Type | Description |
|-------|------|-------------|
| `sequenceType` | enum | 'progressive' / 'complementary' / 'hierarchical' / 'standalone' |
| `selectedTruths` | number[] | Indices from theme's master truths (required, ≥1) |
| `primaryTruth` | number | Main focus truth index (must be in selectedTruths) |
| `secondaryTruths` | number[] | Supporting truth indices (subset of selectedTruths) |
| `localTruths` | LocalTruth[] | Optional 1-2 context-specific truths |

### Sequence Types

| Type | Description | Best For |
|------|-------------|----------|
| **progressive** | Each truth builds on previous (1→2→3 flow) | Educational content, courses |
| **complementary** | Truths are different facets, no required order | Blog posts, social media |
| **hierarchical** | Foundation → Advanced layering | Framework guides |
| **standalone** | Single truth focus | Social posts, quick tips |

### Local Truth Object

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | "local_0" or "local_1" (max 2) |
| `statement` | string | The local truth/principle |
| `application` | string | How to apply in this content |
| `extendsTruthIndex` | number / null | If extends a master truth, its index |
| `isContextSpecific` | boolean | true if standalone local context |
| `extensionMode` | enum / null | 'replace' / 'append' / 'nuance' (default: 'nuance') |

### Section Outline Object

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `index` | number | Essential | Position in template (0-based) |
| `type` | string | Essential | Section type from template |
| `plan` | string | Essential | 1-10 sentences depending on planDepth |
| `keyPoints` | string[] | Essential | 3-5 bullet points for this section |
| `truthsReferenced` | number[] | Essential | Indices from selectedTruths to weave in |

### Section Outline - Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `planLocked` | boolean | If true, regenerateSection() preserves plan text |
| `truthPriority` | object | { driver: number, support: number[] } - prevents truth crowding |
| `localTruthsReferenced` | string[] | IDs of local truths to include |
| `transformationStage` | enum | Maps to theme belief transformation journey (see enum below) |
| `beliefShiftFocused` | number / null | Index into theme.transformationFramework.beliefShifts[] this section addresses |
| `promisesOutcome` | string[] | What audience gains from this section (validated by ContentValidator) |
| `transitionOut` | string | How this section leads into the next |
| `wordCountTarget` | object | { min: number, max: number } |
| `toneNotes` | string | Specific tone guidance for this section |
| `suggestions` | object | { title?, subtitle?, cta?, hookType? } |
| `exerciseType` | string | For exercise sections |
| `exercisePrompt` | string | Specific exercise instruction |
| `imageKeywords` | string[] | Keywords for image search |
| `imageStyle` | string | Overrides outline.globalImageStyle |
| `imageMood` | string | Overrides outline.globalImageMood |
| `imageStyleOverride` | boolean | Flag when style differs from global |

### Section Outline - Framework Fields (Layer 1-2)

| Field | Type | Description |
|-------|------|-------------|
| `frameworkStageId` | string / null | Framework stage this section represents (e.g., "concrete_experience") |
| `frameworkPrompts` | string[] | Stage-specific prompts from framework |
| `frameworkLanguage` | FrameworkLanguage / null | Proprietary interpretation context |
| `frameworkElements` | FrameworkElements / null | Framework-driven element requirements |

### Framework Language Object

| Field | Type | Description |
|-------|------|-------------|
| `interpretationApplied` | boolean | True if proprietary interpretation used |
| `proprietaryTerms` | object | Term mappings from interpretation (e.g., { "concrete_experience": "Notice the Box" }) |
| `stageRename` | string / null | Proprietary name for this stage (if different) |

### Framework Elements Object

| Field | Type | Description |
|-------|------|-------------|
| `required` | string[] | Elements mandated by framework stage |
| `recommended` | string[] | Elements suggested by framework stage |
| `wordCountGuidance` | number / null | Word count from content-type framework |

### Section Outline - Paragraph Structure Fields (Layer 4)

| Field | Type | Description |
|-------|------|-------------|
| `paragraphStructure` | string / null | Paragraph structure ID (e.g., "pearl", "safe", "cer") |
| `expectedParagraphs` | number / null | How many paragraphs in this section |
| `paragraphBreakdown` | ParagraphPlan[] | Per-paragraph planning |

### Paragraph Plan Object

| Field | Type | Description |
|-------|------|-------------|
| `paragraphNum` | number | Position in section (1-indexed) |
| `focus` | string | What this paragraph covers |
| `structure` | string | Which structure to use (may vary within section) |
| `imageComposition` | string | e.g., "close-up", "wide angle", "flat lay" |
| `needsRefinement` | boolean | Flagged for regeneration |

### Transformation Stages

> **Enum values for `transformationStage` field:**
> `current_belief_validation` | `destabilization` | `insight` | `new_belief_installation` | `action`

| Stage | Purpose | Typical Section Types |
|-------|---------|----------------------|
| **current_belief_validation** | Meet reader where they are | intro, opening |
| **destabilization** | Create cognitive dissonance | body (early) |
| **insight** | Offer the "aha moment" | body (middle) |
| **new_belief_installation** | Reinforce new belief | body (late), exercise |
| **action** | Translate belief into behavior | closing, cta, exercise |

**Validation:** Stages should follow logical flow in content. Early sections use validation/destabilization, middle sections use insight, late sections use installation/action.

### Optional Top-Level Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `cognitiveTypeId` | string | null | Supporting | Cognitive type used for structure |
| `globalImageStyle` | string | null | Supporting | Default style for all sections |
| `globalImageMood` | string | null | Supporting | Default mood for all sections |
| `updatedAt` | timestamp | null | Truncatable | Last modification timestamp |
| `usageCount` | number | 0 | Truncatable | Times used in content generation |
| `lastUsedAt` | timestamp | null | Truncatable | Last use timestamp |
| `quality` | Quality | null | Truncatable | AI confidence and review status |
| `rating` | Rating | null | Truncatable | Human rating |

### Quality Object

```json
{
  "aiConfidence": 0.88,
  "humanReviewed": false,
  "reviewedAt": null,
  "reviewedBy": null
}
```

### Rating Object

```json
{
  "score": 4,
  "ratedAt": "2026-01-19T14:00:00Z",
  "ratedBy": "natasha",
  "notes": "Good flow, truths well distributed"
}
```

### Entry Status Enum

| Value | Description | AI Visibility |
|-------|-------------|---------------|
| `draft` | Incomplete, not ready | Hidden |
| `active` | Production-ready | Visible |
| `archived` | Deprecated, kept for history | Hidden |

### Review Status Enum (AI-generated)

| Value | Description | Can AI Use? |
|-------|-------------|-------------|
| `pending` | AI-generated, awaiting review | No |
| `approved` | Human verified | Yes |
| `rejected` | Human marked incorrect | No |

---

## Preview Fields

> Fields included in `_index.preview` for fast UI display.

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `themeId` | Filter by theme |
| `templateId` | Filter by template |
| `contentType` | Filter by content type |
| `planDepth` | Complexity indicator |
| `sectionCount` | Quick indicator |
| `usageCount` | Popularity indicator |

**Example preview JSON:**

```json
{
  "name": "Healthy Conversations - Entrepreneur Workbook",
  "themeId": "theme_acme_healthy-conversations",
  "templateId": "template_entrepreneur-workbook",
  "contentType": "workbook",
  "planDepth": "standard",
  "sectionCount": 5,
  "usageCount": 12
}
```

---

## Staging Area (Drafts)

> AI-generated outlines require human review before production use.

**Staging Path:** `/repos/drafts/outlines/`

### Draft Lifecycle

```
1. OutlineGenerator.generate()  → Creates draft JSON
2. Save draft                   → /repos/drafts/outlines/outline-{slug}-{timestamp}.json
3. Human review                 → Adjust sections, verify truth distribution
4. Approve                      → review_status: approved, quality.humanReviewed: true
5. Finalize                     → INSERT INTO outlines (id, version, data, created_by)
6. Update index                 → UPDATE _index
7. Delete draft                 → Remove JSON file from staging
```

**Rule:** Drafts are NOT in the library until approved. One active outline per theme+template combination.

---

## Dependencies

> Other libraries this library references.

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `themes` | `themeId` | hard | Outline orphaned if theme archived |
| `templates` | `templateId` | hard | Outline orphaned if template archived |
| `content-types` | `contentType` | soft | Reference only |
| `cognitive-types` | `cognitiveTypeId` | soft | Optional reference |
| `section-specs` | `sections[].type` | soft | Reference only |

**Note:** Outlines have UNIQUE constraint on (themeId, templateId, status) - one active outline per combination.

**Dependency Types:**
- **hard** = Required, entry invalid if dependency missing
- **soft** = Optional enrichment, warning if missing

---

## Reference Integrity

> Cross-library integrity rules enforced by Librarian.

| Field | References | On Archive | On Delete |
|-------|------------|------------|-----------|
| `themeId` | themes.id | mark `ref_status='orphaned'` | block |
| `templateId` | templates.id | mark `ref_status='orphaned'` | block |
| `cognitiveTypeId` | cognitive-types.id | warn | set null |
| `sections[].type` | section-specs.id | warn | warn |

**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| (none - outlines are leaf entities) | - | - |

---

## Queryable Fields

> Which fields support search via Librarian.

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `themeId` | Yes | Yes | No | Exact match |
| `templateId` | Yes | Yes | No | Exact match |
| `brandId` | No | Yes | No | Exact match |
| `contentType` | Yes | Yes | No | Exact match |
| `planDepth` | No | Yes | No | Enum filter |
| `status` | No | Yes | No | Enum filter |
| `usageCount` | No | No | Yes | Numeric sort |
| `createdAt` | No | No | Yes | Date sort |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | 100-1000+ | Many outlines per theme |
| **Entry Size** | medium | 2-10KB typical (depends on section count) |
| **FTS Enabled** | yes | name, themeId, templateId, contentType |
| **Lazy Loading** | yes | Preview in _index, full load on demand |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "outline_acme_healthy-conversations_blog",
  "name": "Healthy Conversations - Blog Post",
  "themeId": "theme_acme_healthy-conversations",
  "templateId": "template_blog-standard",
  "contentType": "blog-post",
  "planDepth": "standard",
  "templateSnapshot": {
    "version": "1.0.0",
    "sectionCount": 5,
    "structureHash": "abc123"
  },
  "truthStrategy": {
    "sequenceType": "complementary",
    "selectedTruths": [0, 1, 2],
    "primaryTruth": 0,
    "secondaryTruths": [1, 2]
  },
  "sections": [],
  "createdAt": "2026-01-20T10:00:00Z",
  "createdBy": "ai",
  "status": "draft",
  "usageCount": 0
}
```

### Complete (all fields)

```json
{
  "id": "outline_acme_healthy-conversations_entrepreneur-workbook",
  "name": "Healthy Conversations - Entrepreneur Workbook",

  "themeId": "theme_acme_healthy-conversations",
  "templateId": "template_entrepreneur-workbook",
  "brandId": "brand_acme",
  "audienceId": "audience_entrepreneurs",
  "contentType": "workbook",

  "templateSnapshot": {
    "version": "1.0.0",
    "sectionCount": 5,
    "structureHash": "def456"
  },

  "cognitiveTypeId": "cognitive_problem-solution",
  "planDepth": "standard",

  "truthStrategy": {
    "sequenceType": "progressive",
    "selectedTruths": [0, 1, 2, 3, 4],
    "primaryTruth": 0,
    "secondaryTruths": [1, 2, 3, 4],
    "localTruths": []
  },

  "globalImageStyle": "professional",
  "globalImageMood": "inspiring",

  "sections": [
    {
      "index": 0,
      "type": "title",
      "plan": "Open with an empowering question that challenges assumptions about workplace communication. Frame healthy conversations as a learnable skill, not a personality trait.",
      "keyPoints": [
        "Frame healthy conversations as a skill, not a personality trait",
        "Promise transformation, not just information"
      ],
      "truthsReferenced": [0],
      "truthPriority": {
        "driver": 0,
        "support": []
      },
      "transformationStage": "current_belief_validation",
      "suggestions": {
        "title": "The Art of Conversations That Matter",
        "subtitle": "A Workbook for Entrepreneurs Who Want to Lead Through Dialogue"
      },
      "imageKeywords": ["conversation", "connection", "dialogue", "professional meeting"],
      "imageComposition": "wide angle"
    },
    {
      "index": 1,
      "type": "body",
      "plan": "Establish the problem: most entrepreneurs avoid difficult conversations, leading to festering issues. Use relatable scenarios to create cognitive dissonance. Introduce the core CLEAR framework as a preview.",
      "keyPoints": [
        "Common avoidance patterns",
        "Cost of avoiding conversations",
        "Preview of the CLEAR framework"
      ],
      "truthsReferenced": [0, 1],
      "truthPriority": {
        "driver": 0,
        "support": [1]
      },
      "transformationStage": "destabilization",
      "transitionOut": "Pivot from the problem to the solution framework",
      "wordCountTarget": { "min": 300, "max": 500 },
      "toneNotes": "empathetic but challenging",
      "imageKeywords": ["challenge", "obstacle", "entrepreneur thinking"]
    },
    {
      "index": 2,
      "type": "body",
      "plan": "Deep dive into the CLEAR framework components. Each letter gets clear explanation with real-world examples. This is the insight moment - the 'aha' that shifts perspective.",
      "keyPoints": [
        "C - Clarify intent before speaking",
        "L - Listen actively, not just waiting to respond",
        "E - Express with I-statements",
        "A - Ask questions to understand",
        "R - Resolve together, not win/lose"
      ],
      "truthsReferenced": [2, 3],
      "truthPriority": {
        "driver": 2,
        "support": [3]
      },
      "transformationStage": "insight",
      "transitionOut": "Now that you understand the framework, let's practice applying it",
      "wordCountTarget": { "min": 400, "max": 600 },
      "imageKeywords": ["framework", "steps", "process", "methodology"]
    },
    {
      "index": 3,
      "type": "exercise",
      "plan": "Reflection exercise: identify one conversation you've been avoiding and plan your approach using the CLEAR framework. Make it immediately actionable.",
      "keyPoints": [
        "Identify the specific conversation",
        "Apply each CLEAR step",
        "Set a timeline for action"
      ],
      "truthsReferenced": [3],
      "truthPriority": {
        "driver": 3,
        "support": []
      },
      "transformationStage": "new_belief_installation",
      "exerciseType": "reflection",
      "exercisePrompt": "Think of a conversation you've been putting off. Use the space below to plan your approach using CLEAR."
    },
    {
      "index": 4,
      "type": "closing",
      "plan": "Reinforce key message: healthy conversations are a learnable skill that transforms leadership. Encourage immediate action with one small step. End with empowerment, not pressure.",
      "keyPoints": [
        "Summary of CLEAR",
        "Call to action: have one conversation this week",
        "Encouragement and ongoing support"
      ],
      "truthsReferenced": [0, 4],
      "truthPriority": {
        "driver": 4,
        "support": [0]
      },
      "transformationStage": "action",
      "toneNotes": "empowering, warm",
      "imageKeywords": ["success", "achievement", "forward momentum"],
      "imageComposition": "wide angle"
    }
  ],

  "createdAt": "2026-01-19T14:30:00Z",
  "updatedAt": "2026-01-19T16:00:00Z",
  "createdBy": "ai",
  "usageCount": 5,
  "lastUsedAt": "2026-01-20T09:00:00Z",
  "status": "active",

  "quality": {
    "aiConfidence": 0.88,
    "humanReviewed": true,
    "reviewedAt": "2026-01-19T15:00:00Z",
    "reviewedBy": "natasha"
  },

  "rating": {
    "score": 4,
    "ratedAt": "2026-01-19T17:00:00Z",
    "ratedBy": "natasha",
    "notes": "Good truth distribution, clear transformation arc"
  }
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `outline_{brand}_{theme}_{template}` pattern | `OUTLINE_INVALID_ID` |
| Required fields | All required fields present | `OUTLINE_MISSING_FIELD` |
| Unique combo | One active outline per theme+template | `OUTLINE_ALREADY_EXISTS` |
| Theme exists | themeId must reference active theme | `OUTLINE_INVALID_THEME` |
| Template exists | templateId must reference active template | `OUTLINE_INVALID_TEMPLATE` |
| Truth selection | selectedTruths must reference valid theme truths | `OUTLINE_INVALID_TRUTHS` |
| Primary truth | primaryTruth must be in selectedTruths | `OUTLINE_INVALID_PRIMARY_TRUTH` |
| Section count | sections.length must match template section count | `OUTLINE_SECTION_MISMATCH` |
| Section indices | Section indices must be 0 to N-1 | `OUTLINE_INVALID_SECTION_INDEX` |

### Truth Dependency Validation

| Check | Rule | Error Code |
|-------|------|------------|
| Progressive sequence | If theme.truthRelationshipType = "progressive", selectedTruths must respect dependsOn | `OUTLINE_TRUTH_DEPENDENCY_VIOLATION` |
| Primary coverage | Primary truth should appear in ≥50% of body sections | Warning only |
| Local truth limit | Maximum 2 local truths | `OUTLINE_LOCAL_TRUTH_LIMIT` |

### Template Drift Validation

| Check | Rule | Error Code |
|-------|------|------------|
| Template version | If template version differs from templateSnapshot.version | `OUTLINE_TEMPLATE_DRIFT_DETECTED` |
| Section count | If template sections differ from snapshot | Severity calculated |
| Structure hash | If section types changed | Severity: minor or major |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `OutlineGenerator` | Via staging → finalize |
| Update | `OutlineGenerator` | Creates new version |
| Archive | `OutlineGenerator` | Soft delete |
| Delete | Never | Archive instead |

**Rules:**
- OutlineGenerator owns writes to this library
- Others read via Librarian
- All writes create new versions (immutable records)
- incrementUsage() called by TemplateEngine after successful content generation

---

## Template Drift Handling

When template changes after outline generation, drift is detected:

| Severity | Condition | Recovery Strategy |
|----------|-----------|-------------------|
| none | Snapshot matches template | Proceed normally |
| minor | 1 section added/removed | Use archived template OR migrate outline |
| major | Multiple sections changed | Generate fresh outline |

---

## Plan Depth by Content Type

| Content Type | Default Depth | Plan Length | Key Points |
|--------------|---------------|-------------|------------|
| social-post | minimal | 1-2 sentences | 2-3 |
| email | minimal | 2-3 sentences | 3-4 |
| blog-post | standard | 3-5 sentences | 4-5 |
| workbook | standard | 4-6 sentences | 4-5 |
| whitepaper | detailed | 6-8 sentences | 5-6 |
| ebook | detailed | 6-10 sentences | 5-7 |

---

## Migration Rules

| Version Change | Type | Handling |
|----------------|------|----------|
| 1.0.0 → 1.0.1 | Patch | Auto-migrate on read |
| 1.0.x → 1.1.0 | Minor | Auto-migrate, backward-compatible |
| 1.x.x → 2.0.0 | Major | Manual review required |

**Migration Script Location:** `/migrations/outlines/`

---

## Related

| Related To | Relationship |
|------------|--------------|
| `themes` | Outline based on one theme (references truths by index) |
| `templates` | Outline follows template structure (sections match) |
| `content-types` | planDepth defaults from content type |
| `cognitive-types` | Outline may use cognitive framework |
| `section-specs` | Section types reference section specs |
| `TemplateEngine` | Consumes outlines for content generation |
| `OutlineGenerator` | Owns this library (CRUD operations) |

---

## OutlineGenerator Integration

Outlines are the output of OutlineGenerator. The library schema must match OutlineGenerator.Outline type.

**Key Integration Points:**
- `truthStrategy` determines which truths from theme are used and how
- `sections[].truthsReferenced` ensures coherent truth distribution
- `sections[].transformationStage` aligns with theme's belief transformation
- `templateSnapshot` enables drift detection when templates change
- `quality` and `rating` enable performance tracking
- `usageCount` tracks outline popularity

**TemplateEngine Usage:**
- Loads outline via OutlineGenerator.get()
- Passes sections[index] to each section's prompt
- Calls incrementUsage() after successful render

---

## Notes

- Outlines bridge themes to content - they plan HOW to structure the WHAT
- truthStrategy allows selective use of theme truths (not all truths in every outline)
- localTruths extend master truths for content-specific depth (max 2)
- transitionOut provides narrative bridging between sections
- transformationStage ensures emotional arc matches belief transformation
- One active outline per theme+template combination (UNIQUE constraint)
- Template drift is detected, not auto-fixed - FailureHandler chooses strategy

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Aligned with: OutlineGenerator v1.3.1*
