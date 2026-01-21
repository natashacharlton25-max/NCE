# Themes Library

> **System:** Content
> **Repository:** Content Repository
> **Path:** `/data/themes.sqlite`
> **Library Type:** generated
> **Status:** Draft
> **Aligned With:** ThemeGenerator v1.7.0

---

## Overview

AI-generated content themes that serve as the strategic foundation for content creation. Themes combine brand voice, audience targeting, and transformation outcomes into coherent content angles. Each theme provides the concept, message, keywords, and angle that drive content generation.

**What is a Theme?**

| Thing | What It Is | Example |
|-------|------------|---------|
| **Topic** | Raw subject input | "healthy conversations" |
| **Theme** | Developed angle with keywords, message, concept, transformation context | Full theme object with brand perspective |

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `ThemeGenerator` | Read/Write (Owner) | Generate and store themes |
| `OutlineGenerator` | Read via Librarian | Theme context for outline generation (reads truths) |
| `TemplateEngine` | Read via Librarian | Theme context for content generation |
| `ImageBrowser` | Read via Librarian | Access theme.keywords for image search |

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
CREATE TABLE themes (
  id TEXT,
  version INTEGER,
  data JSON,                          -- Full theme as JSON
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,                    -- 'ai' | 'manual'
  PRIMARY KEY (id, version)
);

-- Latest version view
CREATE VIEW themes_latest AS
SELECT * FROM themes t1
WHERE version = (SELECT MAX(version) FROM themes t2 WHERE t2.id = t1.id);

-- Full-text search
CREATE VIRTUAL TABLE themes_fts USING fts5(
  id,
  name,
  topic,
  concept,
  message,
  keywords,
  content='themes',
  content_rowid='rowid'
);
```

### Alternative Flat Schema (for direct querying)

```sql
CREATE TABLE themes (
  -- Primary key
  id TEXT PRIMARY KEY,

  -- Required fields
  name TEXT NOT NULL,
  topic TEXT NOT NULL,                -- Original topic input
  created_at TEXT NOT NULL,
  created_by TEXT NOT NULL,           -- 'ai' | 'manual'

  -- Scope (determines where theme applies)
  scope_brand TEXT NOT NULL,          -- 'specific' | 'universal'
  scope_audience TEXT NOT NULL,
  scope_content_type TEXT NOT NULL,
  scope_platform TEXT NOT NULL,

  -- Scope references (NULL if universal)
  brand_id TEXT,
  audience_id TEXT,
  content_type_id TEXT,
  platform_id TEXT,

  -- Content fields (nullable)
  concept TEXT,
  message TEXT,
  angle TEXT,                         -- Single angle string
  keywords TEXT,                      -- JSON array
  tone TEXT,
  brand_values TEXT,                  -- JSON array (renamed from 'values' to avoid SQL keyword)
  truths TEXT,                        -- JSON array of Truth objects (3-7)

  -- Temporal metadata
  temporal_type TEXT NOT NULL DEFAULT 'evergreen',
  valid_from TEXT,
  valid_until TEXT,
  season_tags TEXT,                   -- JSON array

  -- Tracking
  usage_count INTEGER DEFAULT 0,
  last_used_at TEXT,
  updated_at TEXT,
  derived_from TEXT,                  -- Source theme ID if derived

  -- Status
  status TEXT NOT NULL DEFAULT 'draft',

  -- Quality (JSON)
  quality TEXT,

  -- Rating (JSON)
  rating TEXT,

  -- Transformation context (v1.6.0)
  target_outcomes TEXT,               -- JSON array of outcome IDs (max 3)
  transformation_framework TEXT,      -- JSON object
  angle_metadata TEXT,                -- JSON object

  -- Framework selection (2026-01-20)
  framework_ids TEXT,                 -- JSON array of framework IDs
  primary_framework_id TEXT,          -- Main framework ID
  framework_selection TEXT,           -- JSON object: { category, evidenceFrameworks, proprietaryFrameworks, selectionRationale }

  FOREIGN KEY (derived_from) REFERENCES themes(id)
);

-- Indexes
CREATE INDEX idx_themes_brand ON themes(brand_id);
CREATE INDEX idx_themes_audience ON themes(audience_id);
CREATE INDEX idx_themes_content_type ON themes(content_type_id);
CREATE INDEX idx_themes_platform ON themes(platform_id);
CREATE INDEX idx_themes_status ON themes(status);
CREATE INDEX idx_themes_temporal ON themes(temporal_type);
CREATE INDEX idx_themes_usage ON themes(usage_count DESC);
CREATE INDEX idx_themes_outcomes ON themes(target_outcomes);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Theme.ts`
> Aligned with: ThemeGenerator v1.7.0 Theme type

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "theme_mtb_ent_healthy-conversations") |
| `name` | string | Essential | Human-readable theme name |
| `topic` | string | Essential | Original topic input |
| `createdAt` | timestamp | Essential | Creation timestamp |
| `createdBy` | enum | Essential | 'ai' / 'manual' |
| `scope` | Scope | Essential | Where theme applies |
| `status` | EntryStatus | Essential | Lifecycle state |

### Scope Object

| Field | Type | Description |
|-------|------|-------------|
| `brand` | enum | 'specific' / 'universal' |
| `audience` | enum | 'specific' / 'universal' |
| `contentType` | enum | 'specific' / 'universal' |
| `platform` | enum | 'specific' / 'universal' |

### Scope Reference Fields

| Field | Type | Description |
|-------|------|-------------|
| `brandId` | string / null | Reference to brand (null if universal) |
| `audienceId` | string / null | Reference to audience (null if universal) |
| `contentTypeId` | string / null | Reference to content type (null if universal) |
| `platformId` | string / null | Reference to platform (null if universal) |

### Optional Content Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `concept` | string | null | Essential | Core idea in one sentence |
| `message` | string | null | Essential | Key takeaway for audience |
| `angle` | string | null | Essential | Unique perspective on the topic |
| `keywords` | string[] | [] | Supporting | 5-10 words for image search |
| `tone` | string | null | Supporting | e.g., "warm, encouraging" |
| `values` | string[] | [] | Supporting | Brand values this theme aligns with |

### Framework Selection Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `frameworkIds` | string[] | [] | Supporting | Selected evidence-based + proprietary framework IDs |
| `primaryFrameworkId` | string / null | null | Supporting | Main framework driving structure |
| `frameworkSelection` | FrameworkSelection / null | null | Supporting | Framework selection metadata |

### Framework Selection Object

| Field | Type | Description |
|-------|------|-------------|
| `category` | string | Framework category (e.g., "skill_building") |
| `evidenceFrameworks` | string[] | Selected Layer 1 framework IDs |
| `proprietaryFrameworks` | string[] | Selected Layer 2 framework IDs (if brand has them) |
| `selectionRationale` | string | Why these frameworks were chosen |

### Truths Array (3-7 items)

Truths are continuity anchors for multi-content campaigns.

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `truths` | Truth[] | Essential | 3-7 core principles that thread through content |

**Validation:**
- Minimum 3 truths (prevents shallow coverage)
- Target 5 truths (optimal cognitive load)
- Maximum 7 truths (human working memory limit)

### Truth Object

| Field | Type | Description |
|-------|------|-------------|
| `index` | number | 0-6 (position in truths array) |
| `statement` | string | The core truth/principle (1-2 sentences) |
| `application` | string | How this truth manifests in content |
| `dependsOn` | number / null | Index of prerequisite truth (for progressive sequences) |
| `status` | enum | 'active' / 'flagged' / 'needs_refinement' |
| `reviewRequired` | boolean | Based on brand.truthApprovalMode |
| `performance` | object | { usageCount, avgRating, regenerateCount, lastUsedAt } |
| `derivedFromUniversal` | string / null | Reference to universal truth |

### Angle Metadata Object

| Field | Type | Description |
|-------|------|-------------|
| `source` | enum | 'ai' / 'human' |
| `locked` | boolean | If true, AI adapts language but not intent |
| `derivedFromPerspective` | string / null | Outcome ID this angle derives from |
| `generatedAngles` | string[] | All angles generated from perspectives |
| `selectionRationale` | string | Why this angle was chosen |

### Transformation Framework Object

> Critical for Tier 1 theme generation quality

| Field | Type | Description |
|-------|------|-------------|
| `primaryOutcome` | PrimaryOutcome | Main outcome being targeted |
| `secondaryOutcomes` | SecondaryOutcome[] | Additional outcomes (if multiple) |
| `beliefShifts` | BeliefShift[] | From → To belief shifts |
| `natashaPerspective` | BrandPerspective / null | Brand owner's perspective (Tier 1 only) |
| `audienceTransformation` | AudienceTransformation | What audience gains |
| `contentIntent` | string / null | Explicit transformation goal statement |

### PrimaryOutcome Object

```json
{
  "outcomeId": "self-005",
  "outcomeName": "Self-Worth",
  "outcomeDefinition": "Recognising inherent value independent of achievements",
  "outcomeTheme": "Worth & Value"
}
```

### BrandPerspective Object

```json
{
  "coreBelief": "Your worth isn't earned through productivity—it's baseline humanity",
  "brandExpression": "You don't have to earn permission to exist",
  "signaturePhrases": ["Worth is baseline, not earned"],
  "whatImRejecting": ["Hustle culture's productivity-as-worth equation"]
}
```

### AudienceTransformation Object

```json
{
  "theyGain": ["Permission to rest without guilt"],
  "theyExperience": ["Relief from constant proving"],
  "theyWalkAwayWith": ["Language to challenge worth-through-productivity beliefs"]
}
```

### Temporal Object

| Field | Type | Description |
|-------|------|-------------|
| `type` | enum | 'evergreen' / 'seasonal' / 'date-specific' / 'trending' |
| `validFrom` | date / null | When theme becomes relevant |
| `validUntil` | date / null | When theme expires |
| `seasonTags` | string[] | e.g., ["Q1", "new-year", "january"] |

### Tracking Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `usageCount` | number | 0 | Times used in content generation |
| `lastUsedAt` | timestamp / null | null | Last use timestamp |
| `updatedAt` | timestamp / null | null | Last modification timestamp |
| `derivedFrom` | string / null | null | Source theme ID if derived |
| `targetOutcomes` | string[] | [] | Outcome IDs this theme targets (max 3) |

### Quality Object

```json
{
  "aiConfidence": 0.85,
  "humanReviewed": true,
  "reviewedAt": "2026-01-19T12:00:00Z",
  "reviewedBy": "natasha"
}
```

### Rating Object

```json
{
  "score": 4,
  "ratedAt": "2026-01-19T14:00:00Z",
  "ratedBy": "natasha",
  "notes": "Good keywords, message could be stronger"
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
| `topic` | Original topic input |
| `brandId` | Filter by brand |
| `audienceId` | Filter by audience |
| `temporalType` | Evergreen vs seasonal |
| `usageCount` | Popularity indicator |
| `rating` | Quality indicator |

**Example preview JSON:**

```json
{
  "name": "Rest as Radical Resistance",
  "topic": "rest and productivity",
  "brandId": "brand_bylw",
  "audienceId": null,
  "temporalType": "evergreen",
  "usageCount": 12,
  "rating": 4
}
```

---

## Staging Area (Drafts)

> AI-generated themes require human review before production use.

**Staging Path:** `/repos/drafts/themes/`

### Draft Lifecycle

```
1. ThemeGenerator.generate()    → Creates draft JSON
2. Save draft                   → /repos/drafts/themes/theme-{slug}-{timestamp}.json
3. Human review                 → Edit angle, verify truths, check transformation framework
4. Approve                      → review_status: approved, quality.humanReviewed: true
5. Finalize                     → INSERT INTO themes (id, version, data, created_by)
6. Update index                 → UPDATE _index
7. Delete draft                 → Remove JSON file from staging
```

**Rule:** Drafts are NOT in the library until approved. The library only contains finalized, immutable records.

---

## Dependencies

> Other libraries this library references.

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `brands` | `brandId` | soft | Theme valid but scope-limited if brand missing |
| `audiences` | `audienceId` | soft | Theme valid but scope-limited if audience missing |
| `content-types` | `contentTypeId` | soft | Optional scope dimension |
| `outcomes` | `targetOutcomes[]` | soft | Theme stale if outcome archived |

**Note:** Themes with `scope.brand: "universal"` have null `brandId` - this is valid, not an error.

**Dependency Types:**
- **hard** = Required, entry invalid if dependency missing
- **soft** = Optional enrichment, warning if missing

---

## Reference Integrity

> Cross-library integrity rules enforced by Librarian.

| Field | References | On Archive | On Delete |
|-------|------------|------------|-----------|
| `brandId` | brands.id | warn | warn |
| `audienceId` | audiences.id | warn | warn |
| `contentTypeId` | content-types.id | warn | warn |
| `targetOutcomes[]` | outcomes.id | mark stale | mark stale |

**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| `outlines` | themeId | mark `ref_status='stale'`, block if active |

---

## Queryable Fields

> Which fields support search via Librarian.

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `topic` | Yes | Yes | Yes | Full-text |
| `brandId` | No | Yes | No | Exact match |
| `audienceId` | No | Yes | No | Exact match |
| `contentTypeId` | No | Yes | No | Exact match |
| `platformId` | No | Yes | No | Exact match |
| `status` | No | Yes | No | Enum filter |
| `temporal.type` | No | Yes | No | Enum filter |
| `usageCount` | No | No | Yes | Numeric sort |
| `lastUsedAt` | No | No | Yes | Date sort |
| `rating.score` | No | Yes | Yes | Numeric filter/sort |
| `concept` | Yes | No | No | Full-text |
| `message` | Yes | No | No | Full-text |
| `keywords` | Yes | No | No | Full-text |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | 100-1000 | Many themes per brand |
| **Entry Size** | medium-large | 5-15KB typical (depends on truths, transformation) |
| **FTS Enabled** | yes | name, topic, concept, message, keywords |
| **Lazy Loading** | yes | Preview in _index, full load on demand |
| **Cache TTL** | 48h | Themes are brand assets, not volatile data |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "theme_universal_healthy-conversations",
  "name": "Healthy Conversations",
  "topic": "healthy conversations",
  "createdAt": "2026-01-20T10:30:00Z",
  "createdBy": "ai",
  "scope": {
    "brand": "universal",
    "audience": "universal",
    "contentType": "universal",
    "platform": "universal"
  },
  "brandId": null,
  "audienceId": null,
  "contentTypeId": null,
  "platformId": null,
  "status": "draft",
  "temporal": {
    "type": "evergreen"
  },
  "usageCount": 0
}
```

### Tier 4 (Legacy - no transformation context)

```json
{
  "id": "theme_mtb_ent_healthy-conversations",
  "name": "Healthy Conversations",
  "topic": "healthy conversations",
  "createdAt": "2026-01-20T10:30:00Z",
  "createdBy": "ai",
  "scope": {
    "brand": "specific",
    "audience": "specific",
    "contentType": "universal",
    "platform": "universal"
  },
  "brandId": "brand_mind-the-box",
  "audienceId": "audience_entrepreneurs",
  "contentTypeId": null,
  "platformId": null,
  "concept": "Building authentic connection through mindful communication",
  "message": "Transform your relationships through intentional dialogue",
  "angle": "personal growth through better communication",
  "keywords": ["communication", "connection", "mindfulness", "dialogue", "listening"],
  "tone": "warm, encouraging",
  "values": ["authenticity", "growth"],
  "temporal": {
    "type": "evergreen"
  },
  "status": "draft",
  "usageCount": 0,
  "quality": {
    "aiConfidence": 0.85,
    "humanReviewed": false
  }
}
```

### Complete (Tier 1 - Full transformation context)

```json
{
  "id": "theme_bylw_rest-productivity",
  "name": "Rest as Radical Resistance",
  "topic": "rest and productivity",
  "createdAt": "2026-01-20T10:00:00Z",
  "createdBy": "ai",

  "scope": {
    "brand": "specific",
    "audience": "universal",
    "contentType": "universal",
    "platform": "universal"
  },
  "brandId": "brand_be-you-love-wins",
  "audienceId": null,
  "contentTypeId": null,
  "platformId": null,

  "concept": "Rejecting hustle culture's worth-through-productivity equation",
  "message": "Your worth isn't earned—rest is infrastructure, not indulgence",
  "angle": "Rest as radical resistance to hustle culture",

  "angleMetadata": {
    "source": "ai",
    "locked": false,
    "derivedFromPerspective": "self-005",
    "generatedAngles": [
      "Rest as radical resistance to hustle culture",
      "Worth beyond productivity metrics",
      "Strategic rest as business infrastructure"
    ],
    "selectionRationale": "Best alignment with anti-hustle positioning"
  },

  "targetOutcomes": ["self-005"],

  "transformationFramework": {
    "primaryOutcome": {
      "outcomeId": "self-005",
      "outcomeName": "Self-Worth",
      "outcomeDefinition": "Recognising inherent value independent of achievements",
      "outcomeTheme": "Worth & Value"
    },
    "secondaryOutcomes": [],
    "beliefShifts": [
      {
        "from": "I'm only valuable when I'm productive",
        "to": "My worth is inherent—productivity is what I do, not who I am"
      }
    ],
    "natashaPerspective": {
      "coreBelief": "Your worth isn't earned through productivity—it's baseline humanity",
      "brandExpression": "You don't have to earn permission to exist. Your worth is not on your to-do list.",
      "signaturePhrases": [
        "Worth is baseline, not earned",
        "Rest as infrastructure, not indulgence"
      ],
      "whatImRejecting": [
        "Hustle culture's productivity-as-worth equation",
        "The 'earn your rest' mentality"
      ]
    },
    "audienceTransformation": {
      "theyGain": [
        "Permission to rest without guilt",
        "Understanding that worth ≠ productivity"
      ],
      "theyExperience": [
        "Relief from constant proving",
        "First moment of 'maybe I'm enough as I am'"
      ],
      "theyWalkAwayWith": [
        "Language to challenge worth-through-productivity beliefs",
        "Reminder that worth is baseline, not earned"
      ]
    },
    "contentIntent": "Help audience recognize they don't have to earn their worth through constant productivity"
  },

  "truths": [
    {
      "index": 0,
      "statement": "Your worth isn't something you earn—it's your baseline humanity",
      "application": "Challenge productivity-as-worth belief in opening section",
      "status": "active"
    },
    {
      "index": 1,
      "statement": "Rest is not a reward for productivity; it's a requirement for being human",
      "application": "Permission-giving content throughout",
      "status": "active"
    },
    {
      "index": 2,
      "statement": "Doing less doesn't make you less",
      "application": "Counter comparison and achievement culture",
      "status": "active"
    },
    {
      "index": 3,
      "statement": "Productivity is what you do, not who you are",
      "application": "Separation of doing from being",
      "status": "active"
    },
    {
      "index": 4,
      "statement": "Strategic rest creates capacity—it's infrastructure, not indulgence",
      "application": "Reframe rest as practical, not indulgent",
      "status": "active"
    }
  ],

  "keywords": ["rest", "self-worth", "anti-hustle", "permission", "humanity", "productivity"],
  "tone": "warm, permission-giving",
  "values": ["self-compassion", "authenticity", "rest"],

  "temporal": {
    "type": "evergreen",
    "validFrom": null,
    "validUntil": null,
    "seasonTags": null
  },

  "status": "active",
  "usageCount": 12,
  "lastUsedAt": "2026-01-19T15:30:00Z",
  "updatedAt": "2026-01-19T12:00:00Z",
  "derivedFrom": null,

  "quality": {
    "aiConfidence": 0.92,
    "humanReviewed": true,
    "reviewedAt": "2026-01-20T09:00:00Z",
    "reviewedBy": "natasha"
  },

  "rating": {
    "score": 5,
    "ratedAt": "2026-01-19T16:00:00Z",
    "ratedBy": "natasha",
    "notes": "Strong alignment with brand voice, truths feel authentic"
  }
}
```

### Theme ID Format by Scope

```javascript
// ID format: theme_{brandPrefix}_{audiencePrefix}_{slugified-topic}
// Prefixes omitted for universal dimensions

"theme_universal_healthy-conversations"           // Fully universal
"theme_mtb_healthy-conversations"                 // Brand-specific, audience-universal
"theme_mtb_ent_healthy-conversations"             // Brand + audience specific
"theme_mtb_ent_wb_healthy-conversations"          // Brand + audience + content-type specific
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `theme_{scope}_{slug}` pattern | `THEME_INVALID_ID` |
| Required fields | All required fields present | `THEME_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `THEME_DUPLICATE_ID` |
| Scope consistency | If brandId provided, scope.brand must be "specific" | `THEME_SCOPE_MISMATCH` |
| Brand valid | If brandId not null, brand must exist | `THEME_INVALID_BRAND` |
| Audience valid | If audienceId not null, audience must exist | `THEME_INVALID_AUDIENCE` |
| Outcomes exist | All targetOutcomes must exist in outcomes library | `THEME_INVALID_OUTCOME` |
| Outcome limit | Maximum 3 target outcomes | `THEME_OUTCOME_LIMIT` |
| Truths count | If truths present, must be 3-7 items | `THEME_INVALID_TRUTHS_COUNT` |
| Truths indexed | Truths must have sequential index 0 to N-1 | `THEME_INVALID_TRUTHS_INDEX` |
| Active requirements | Active themes must have concept, message, angle, truths | `THEME_INCOMPLETE_FOR_ACTIVE` |

### Transformation Context Validation

| Check | Rule | Error Code |
|-------|------|------------|
| Framework required | If targetOutcomes present, transformationFramework should be populated | `THEME_MISSING_FRAMEWORK` |
| Perspective source | If angleMetadata.derivedFromPerspective set, outcome must exist | `THEME_INVALID_PERSPECTIVE_REF` |
| Truths-outcome alignment | Min 3 of 5 truths should relate to target outcomes | `THEME_TRUTHS_MISALIGNED` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `ThemeGenerator` | Via staging → finalize |
| Update | `ThemeGenerator` | Creates new version |
| Archive | `ThemeGenerator` | Soft delete |
| Delete | Never | Archive instead |

**Rules:**
- ThemeGenerator owns writes to this library
- Others read via Librarian
- All writes create new versions (immutable records)
- incrementUsage() called by TemplateEngine after successful content generation

---

## Generation Quality Tiers

> Theme generation quality depends on available transformation context.

| Tier | Context Available | Result Quality |
|------|-------------------|----------------|
| **Tier 1: Full** | Outcome + Perspective + Beliefs + Desired | Authentic voice, strategic alignment, full framework |
| **Tier 2: Good** | Outcome + Beliefs + Desired (no perspective) | Strategic alignment, generic voice |
| **Tier 3: Basic** | Outcome only | Topic-focused, minimal transformation strategy |
| **Tier 4: Legacy** | No targetOutcomes provided | Traditional theme generation (backward compatible) |

---

## Migration Rules

| Version Change | Type | Handling |
|----------------|------|----------|
| 1.0.0 → 1.0.1 | Patch | Auto-migrate on read |
| 1.0.x → 1.1.0 | Minor | Auto-migrate, backward-compatible |
| 1.x.x → 2.0.0 | Major | Manual review required |

**Migration Script Location:** `/migrations/themes/`

---

## Related

| Related To | Relationship |
|------------|--------------|
| `brands` | Theme may belong to one brand (or universal) |
| `audiences` | Theme may target one audience (or universal) |
| `content-types` | Theme may be specific to content type |
| `outcomes` | Theme targets 1-3 outcomes (transformation context) |
| `outlines` | Outlines reference themes, select subset of truths |
| `OutlineGenerator` | Consumes themes to generate outlines |
| `TemplateEngine` | Reads theme for content generation |
| `ImageBrowser` | Uses theme.keywords for image search |

---

## ThemeGenerator Integration

Themes are the output of ThemeGenerator. The library schema must match ThemeGenerator.Theme type.

**Key Integration Points:**
- `transformationFramework` enables Tier 1 generation (brand voice alignment)
- `truths[]` provide continuity anchors for multi-content campaigns
- `angleMetadata` tracks angle governance (AI vs human, locked status)
- `quality` and `rating` enable performance tracking
- `usageCount` and `lastUsedAt` track theme popularity

**OutlineGenerator Usage:**
- OutlineGenerator receives theme with truths array
- Selects subset of master truths for specific content piece
- Ensures coherent distribution across multi-content campaigns

---

## Notes

- Themes are the strategic bridge between brand identity and content
- Scope system allows universal → specific derivation (saves AI calls)
- Truths (3-7) are continuity anchors, not TruthClaims (those were removed)
- truthApprovalMode from brand controls whether truths need human review
- Keywords should be visually searchable for ImageBrowser (concrete nouns preferred)

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Aligned with: ThemeGenerator v1.7.0*
