# ThemeGenerator Module

> **Purpose:** Generate and manage content themes - the topic angle/concept that drives content generation
> **Type:** Content System Module
> **Status:** Approved
> **Version:** 1.7.0

---

## Overview

ThemeGenerator creates and manages themes - the developed, brand-aligned versions of raw topics. A theme provides the concept, message, keywords, and angle that drive content generation and image search.

```
Outcomes Library (perspectives, belief shifts)
         ↓
Topic → ThemeGenerator → Theme (with truths, angles) → OutlineGenerator → TemplateEngine → Content
```

**What is a Theme?**

| Thing | What It Is | Example |
|-------|------------|---------|
| **Topic** | Raw subject | "healthy conversations" |
| **Theme** | Developed angle with keywords, message, concept | Full theme object with brand perspective |

Topic is input. Theme is the developed, brand-aligned version.

---

## Roles & Rules

### ThemeGenerator DOES:
- Generate themes from raw topics using AI
- Pull transformation context from Outcomes Library (perspectives, belief shifts)
- Generate angles from perspectives (AI by default, human-lockable)
- Store themes in SQLite database with full metadata
- Query themes with rich filtering (scope, temporal, status, rating)
- Full-text search across theme content
- Track usage statistics and ratings
- Support scoped themes (brand, audience, content-type, platform)
- Derive specific themes from universal themes
- Provide keywords for image search
- Align themes with brand voice and values
- Auto-flag underperforming truths for human review

### ThemeGenerator does NOT:
- Generate content (TemplateEngine does that)
- Search for images (ImageBrowser uses theme.keywords)
- Build prompts (PromptBuilder does that)
- Store brand data (BrandManager does that)
- Manage database infrastructure (DatabaseHandler does that)
- Store outcomes/perspectives (Outcomes Library is external data)

### Boundaries:
- Themes have four scope dimensions (brand, audience, content-type, platform)
- Each dimension can be "specific" or "universal"
- Not all themes have all fields - partial data is valid
- Themes are reusable across multiple content pieces
- Brand-specific theme generation requires brand context

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| **Lifecycle** |
| `initialize()` | - | initResult | Create database/tables if needed, validate schema |
| **Generation** |
| `generate(topic, scope, options?)` | topic, scope object, options | theme | AI generates theme with specified scope. Options include `target_outcomes[]` for transformation context. |
| `generateBatch(topic, scopeVariants[], options?)` | topic, array of scope configs | themes[] | Generate multiple scope variants at once |
| `deriveFrom(themeId, newScope)` | themeId, target scope | theme | Create scoped theme from broader theme |
| **CRUD** |
| `get(themeId)` | themeId | theme | Load existing theme from database |
| `save(theme)` | theme | themeId | Insert or update theme in database |
| `update(themeId, updates)` | themeId, partial theme | theme | Update specific fields on existing theme |
| `delete(themeId)` | themeId | result | Soft-delete (archive) or hard-delete theme |
| `exists(themeId)` | themeId | boolean | Check if theme exists |
| **Query** |
| `list(filters?)` | ListFilters | themes[] | List themes with filtering, sorting, pagination |
| `search(query, filters?)` | search string, filters | themes[] | Full-text search across themes |
| `findMatching(scope)` | scope object | themes[] | Find themes matching a scope (for suggestions) |
| **Tracking** |
| `incrementUsage(themeId)` | themeId | result | Increment usage counter (called by TemplateEngine) |
| `rate(themeId, rating)` | themeId, rating object | result | Rate a theme (1-5 stars with optional notes) |
| `getStats(filters?)` | filters | stats | Usage statistics for themes |

---

## Options Type Definitions

### Theme

```javascript
{
  // === REQUIRED FIELDS (always present) ===
  id: string,                    // e.g., "theme_mtb_healthy-conversations"
  name: string,                  // e.g., "Healthy Conversations"
  topic: string,                 // Original topic input (e.g., "healthy conversations")
  createdAt: string,             // ISO timestamp
  createdBy: "ai" | "manual",    // How theme was created

  // === SCOPE (required, determines where theme applies) ===
  scope: {
    brand: "specific" | "universal",      // specific = one brand, universal = any brand
    audience: "specific" | "universal",   // specific = one audience, universal = any audience
    contentType: "specific" | "universal",// specific = one content type, universal = any
    platform: "specific" | "universal"    // specific = one platform, universal = any
  },

  // Scope references (null if universal for that dimension)
  brandId: string | null,        // e.g., "brand_mind-the-box"
  audienceId: string | null,     // e.g., "audience_entrepreneurs"
  contentTypeId: string | null,  // e.g., "workbook", "email", "social-post"
  platformId: string | null,     // e.g., "instagram", "linkedin", "email"

  // === OPTIONAL CONTENT FIELDS (may be null/empty) ===
  concept?: string,              // Core idea in one sentence
  message?: string,              // Key takeaway for audience
  angle?: string,                // Unique perspective on the topic

  // === ANGLE GOVERNANCE ===
  angleMetadata?: {
    source: "ai" | "human",           // How angle was generated
    locked: boolean,                  // If true, AI adapts language but not intent
    derivedFromPerspective?: string,  // Outcome ID this angle derives from
    generatedAngles?: string[],       // All angles generated from perspectives (preserves options)
    selectionRationale?: string       // Why this angle was chosen over others
  },

  // === TRANSFORMATION CONTEXT (from Outcomes Library) ===
  targetOutcomes?: string[],     // Outcome IDs this theme targets (max 3, e.g., ["self-005", "self-012"])

  transformationFramework?: {
    // Primary outcome being targeted
    primaryOutcome: {
      outcomeId: string,           // e.g., "self-005"
      outcomeName: string,         // e.g., "Self-Worth"
      outcomeDefinition: string,   // Full definition from outcomes library
      outcomeTheme: string         // e.g., "Worth & Value"
    },

    // Secondary outcomes (if multiple targets specified)
    secondaryOutcomes?: Array<{
      outcomeId: string,
      outcomeName: string
    }>,

    // Belief shifts this theme facilitates
    beliefShifts: Array<{
      from: string,                // Limiting belief
      to: string                   // Healthier belief
    }>,

    // Natasha's perspective on this outcome (if available)
    natashaPerspective?: {
      coreBelief: string,          // From outcomes library natasha_perspective.core_belief
      brandExpression: string,     // Brand-specific version from how_i_talk_about_this
      signaturePhrases: string[],  // Key phrases to use
      whatImRejecting: string[]    // Counter-narratives to avoid
    },

    // What audience gains/experiences/walks away with
    audienceTransformation: {
      theyGain: string[],          // From desired_outcomes library
      theyExperience: string[],
      theyWalkAwayWith: string[]
    },

    // Explicit transformation goal statement
    contentIntent?: string         // Auto-generated or human-provided
  },

  // For image search and content generation
  keywords?: string[],           // 5-10 words for image search
  tone?: string,                 // e.g., "warm, encouraging"

  // Brand alignment (when scope.brand = "specific")
  values?: string[],             // Brand values this theme aligns with

  // === TRUTHS (continuity anchors for multi-content campaigns) ===
  truths?: Truth[],              // 3-7 core principles that thread through all content from this theme
                                 // (Target: 5 | Min: 3 | Max: 7)

  // === TEMPORAL METADATA ===
  temporal: {
    type: "evergreen" | "seasonal" | "date-specific" | "trending",
    validFrom?: string,          // ISO date - when theme becomes relevant
    validUntil?: string,         // ISO date - when theme expires
    seasonTags?: string[]        // e.g., ["Q1", "new-year", "january"]
  },

  // === TRACKING METADATA ===
  usageCount: number,            // Times used in content generation
  lastUsedAt?: string,           // ISO timestamp of last use
  updatedAt?: string,            // ISO timestamp of last modification (editorial audits, sync)
  derivedFrom?: string,          // If derived from universal, the source theme ID

  // === QUALITY/STATUS FLAGS ===
  status: "draft" | "active" | "archived",
  quality?: {
    aiConfidence?: number,       // 0-1 score - from AI response metadata if available
    humanReviewed?: boolean,     // Has human reviewed this theme
    reviewedAt?: string,         // When reviewed
    reviewedBy?: string          // Who reviewed
  },
  // Note: aiConfidence is self-reported by the model when available,
  // or calculated heuristically (response length, field completeness, retries needed)

  // === RATING ===
  rating?: {
    score: number,               // 1-5 stars
    ratedAt?: string,            // ISO timestamp
    ratedBy?: string,            // Who rated
    notes?: string               // Optional feedback
  }
}
```

### Truth

```javascript
{
  index: number,               // 0-6 (position in truths array, max 7 truths)
  statement: string,           // The core truth/principle (1-2 sentences)
  application: string,         // How this truth manifests in content (1 sentence)

  // === TRUTH DEPENDENCIES (for progressive sequences) ===
  dependsOn?: number,          // Index of truth that must come before this one
                               // Used when truthRelationshipType: "progressive"
                               // OutlineGenerator enforces: can't select truth[2] without truth[1] if dependsOn: 1

  // === TRUTH GOVERNANCE ===
  status?: "active" | "flagged" | "needs_refinement",  // Performance-based status
  reviewRequired?: boolean,    // Based on brand.truthApprovalMode
  performance?: {
    usageCount: number,        // Times this truth was used in content
    avgRating?: number,        // Average rating of content using this truth
    regenerateCount?: number,  // Times users requested regeneration
    lastUsedAt?: string        // ISO timestamp
  },
  derivedFromUniversal?: string  // If brand-adapted, reference to universal truth
}
```

**Validation:**
- Minimum 3 truths (prevents shallow coverage)
- Target 5 truths (optimal cognitive load)
- Maximum 7 truths (human working memory limit)
- Warning (not error) if outside 5

**Truth Governance:**
- Truths with `avgRating < 2.5` auto-flagged as `needs_refinement`
- System observes, human refines (no auto-mutation)
- Brand `truthApprovalMode` controls workflow: `strict` | `guided` | `trusted`

**Example Truths** (Theme: "Sustainable Self-Care for Busy Parents"):

```javascript
truths: [
  {
    index: 0,
    statement: "Self-care isn't selfish - it's essential modeling for your children.",
    application: "Show how prioritizing yourself teaches kids healthy boundaries."
  },
  {
    index: 1,
    statement: "Imperfect consistency beats perfect intentions.",
    application: "Celebrate small wins over grand plans that never happen."
  },
  {
    index: 2,
    statement: "Small moments count as much as grand gestures.",
    application: "Highlight micro-self-care: 2-minute breathing, one mindful sip of coffee."
  },
  {
    index: 3,
    statement: "You can't pour from an empty cup.",
    application: "Use this metaphor to justify rest without guilt."
  },
  {
    index: 4,
    statement: "Self-care looks different for everyone - comparison is the thief of joy.",
    application: "Encourage readers to define their own version, not Instagram's."
  }
]
```

**Usage:** OutlineGenerator selects a subset of master truths (`selectedTruths: [0, 2, 4]`), ensuring coherent distribution across multi-content campaigns. Outlines can optionally add 1-2 local truths specific to that content piece.

**Field Presence by Scope:**

| Scope Type | Always Has | Sometimes Has |
|------------|------------|---------------|
| Universal (all dimensions) | id, name, topic, scope, temporal | concept, message, keywords, truths |
| Brand-specific | + brandId, values | concept, message, angle, keywords, tone, truths |
| Audience-specific | + audienceId | message, angle, truths |
| Content-type-specific | + contentTypeId | angle, keywords, truths |
| Platform-specific | + platformId | tone, keywords, truths |

Not all themes need all fields. A quick "trending topic" theme might only have `topic`, `keywords`, and `temporal.type: "trending"`. A fully developed brand theme will have most fields populated.

### GenerateOptions

```javascript
{
  // === TRANSFORMATION TARGETING ===
  targetOutcomes?: string[],        // Outcome IDs to target (max 3)
  contentIntent?: string,           // Optional explicit transformation goal
  allowDivergentOutcomes?: boolean, // Allow outcomes from different themes (default: false)
                                    // If false, warns when outcomes have different outcomeTheme values

  // === ANGLE CONTROL ===
  angleLocked?: boolean,            // Lock generated angle to prevent AI variation
                                    // When true: PromptBuilder receives instruction to adapt
                                    // language/tone but preserve core intent and direction
  preferredAngles?: string[],       // Suggest specific angles to consider

  // === TRUTH GENERATION ===
  truthCount?: number,              // 3-7, default 5
  truthRelationshipType?: "progressive" | "complementary" | "hierarchical",

  // === GENERATION OPTIONS ===
  keywordCount?: {               // How many keywords to generate
    min?: number,                // default: 5
    max?: number                 // default: 10
  },
  visualKeywordMin?: number,     // Minimum concrete/visual keywords (default: 3)
                                 // Keywords must be visually searchable for ImageBrowser
                                 // Good: "conversation", "handshake", "meeting"
                                 // Bad: "authenticity", "growth", "transformation"
  temperature?: number,          // AI creativity (default: 0.7)
  autoSave?: boolean,            // Save immediately after generation (default: false)
  allowDuplicate?: boolean,      // Generate even if theme for topic/brand exists (default: false)

  // === TEMPORAL SETTINGS ===
  temporal?: {
    type?: "evergreen" | "seasonal" | "date-specific" | "trending",
    validFrom?: string,          // ISO date
    validUntil?: string,         // ISO date
    seasonTags?: string[]
  },

  // === CACHE CONTROL ===
  useCache?: boolean,            // Default true
  cacheTTL?: number,             // Override default 48h

  // === STATUS ===
  status?: "draft" | "active"    // default: "draft"
}
```

### ListFilters

```javascript
{
  // Scope filters (all optional)
  brandId?: string,              // Filter by brand
  audienceId?: string,           // Filter by audience
  contentTypeId?: string,        // Filter by content type (workbook, email, etc.)
  platformId?: string,           // Filter by platform (instagram, linkedin, etc.)

  // Scope type filters
  scope?: {
    brand?: "specific" | "universal",
    audience?: "specific" | "universal",
    contentType?: "specific" | "universal",
    platform?: "specific" | "universal"
  },

  // Temporal filters
  temporal?: {
    type?: "evergreen" | "seasonal" | "date-specific" | "trending",
    activeOn?: string            // ISO date - themes valid on this date
  },

  // Status filters
  status?: "draft" | "active" | "archived" | "all",

  // Search
  search?: string,               // Full-text search on topic, name, keywords

  // Pagination
  limit?: number,                // default: 50
  offset?: number,               // default: 0

  // Rating filter
  minRating?: number,            // Only themes rated >= this (1-5)

  // Sorting
  sortBy?: "usageCount" | "createdAt" | "lastUsedAt" | "name" | "rating",
  sortOrder?: "asc" | "desc"     // default: "desc" for counts/dates/rating, "asc" for name
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
3. Create tables if not exist (themes, themes_fts)
4. Validate schema version matches expected (stored in metadata table)
5. If schema outdated: run pending migrations from /migrations/themes/
6. Return readiness report

// Output (success)
{
  success: true,
  database: "/data/themes.db",
  themeCount: 45,
  schemaVersion: "1.2.0",
  migrationsRun: 0
}

// Output (migrations ran)
{
  success: true,
  database: "/data/themes.db",
  themeCount: 45,
  schemaVersion: "1.2.0",
  migrationsRun: 2,
  migrations: ["1.1.0_add_rating", "1.2.0_add_platform_scope"]
}

// Output (error)
{
  success: false,
  error: {
    code: "DATABASE_INIT_FAILED",
    message: "Could not create database at /data/themes.db",
    cause: "Permission denied"
  }
}
```

---

### generate(topic, scope, options?)

Generate a theme from a raw topic with specified scope.

```javascript
// Input
{
  topic: "healthy conversations",
  scope: {
    brandId: "brand_mind-the-box",       // null for universal
    audienceId: "audience_entrepreneurs", // null for universal
    contentTypeId: null,                  // null = any content type
    platformId: null                      // null = any platform
  },
  options: {
    keywordCount: { min: 5, max: 10 },
    temporal: { type: "evergreen" },
    autoSave: true,
    status: "draft"
  }
}

// Process
1. Determine scope types from provided IDs (specific if ID present, universal if null)
2. Load contexts for provided IDs:
   - BrandManager.get(brandId) if brandId provided
   - AudienceManager.get(audienceId) if audienceId provided
3. Generate theme ID based on scope
4. Check for existing theme if !options.allowDuplicate
5. Build prompt via PromptBuilder.buildThemePrompt({...})
6. Call AICaller.generate(prompt)
7. Parse response, populate required fields
8. If autoSave: insert into database
9. Return theme

// Output (success)
{
  success: true,
  theme: {
    id: "theme_mtb_ent_healthy-conversations",
    name: "Healthy Conversations",
    topic: "healthy conversations",

    scope: {
      brand: "specific",
      audience: "specific",
      contentType: "universal",
      platform: "universal"
    },
    brandId: "brand_mind-the-box",
    audienceId: "audience_entrepreneurs",
    contentTypeId: null,
    platformId: null,

    concept: "Building authentic connection through mindful communication",
    message: "Transform your relationships through intentional dialogue",
    angle: "personal growth through better communication",
    keywords: ["communication", "connection", "mindfulness", "dialogue"],
    tone: "warm, encouraging",
    values: ["authenticity", "growth"],

    temporal: {
      type: "evergreen",
      validFrom: null,
      validUntil: null,
      seasonTags: null
    },

    status: "draft",
    usageCount: 0,
    lastUsedAt: null,
    updatedAt: null,
    createdAt: "2026-01-19T10:30:00Z",
    createdBy: "ai",
    quality: {
      aiConfidence: 0.85,      // From AI response metadata if available
      humanReviewed: false,
      reviewedAt: null,
      reviewedBy: null
    }
  },
  existing: false
}
```

---

### get(themeId)

Load an existing theme from the database.

```javascript
// Input
{
  themeId: "theme_mtb_ent_healthy-conversations"
}

// Process
1. Query database: SELECT * FROM themes WHERE id = ?
2. Parse JSON fields (keywords, values, quality, rating, etc.)
3. Map DB columns to Theme object
4. Return theme

// Output (success)
{
  success: true,
  theme: {
    id: "theme_mtb_ent_healthy-conversations",
    name: "Healthy Conversations",
    topic: "healthy conversations",
    scope: { brand: "specific", audience: "specific", contentType: "universal", platform: "universal" },
    brandId: "brand_mind-the-box",
    audienceId: "audience_entrepreneurs",
    concept: "Building authentic connection...",
    keywords: ["communication", "connection", "mindfulness"],
    tone: "warm, encouraging",
    values: ["authenticity", "growth"],
    temporal: { type: "evergreen" },
    status: "active",
    usageCount: 5,
    lastUsedAt: "2026-01-18T15:00:00Z",
    rating: { score: 4, ratedAt: "2026-01-18T16:00:00Z" },
    createdAt: "2026-01-15T10:30:00Z",
    createdBy: "ai"
  }
}

// Output (error - theme not found)
{
  success: false,
  error: {
    code: "THEME_NOT_FOUND",
    message: "Theme 'theme_mtb_ent_healthy-conversations' not found",
    themeId: "theme_mtb_ent_healthy-conversations"
  }
}
```

---

### list(filters?)

List themes with optional filtering, sorting, and pagination.

```javascript
// Input
{
  filters: {
    brandId: "brand_mind-the-box",
    status: "active",
    temporal: { type: "evergreen" },
    minRating: 3,
    sortBy: "usageCount",
    sortOrder: "desc",
    limit: 20,
    offset: 0
  }
}

// Process
1. Build SQL query with WHERE clauses from filters
2. Add ORDER BY, LIMIT, OFFSET
3. Execute query
4. Parse JSON fields for each result
5. Get total count (without LIMIT)
6. Return paginated results

// Output (success)
{
  success: true,
  themes: [
    {
      id: "theme_mtb_ent_healthy-conversations",
      name: "Healthy Conversations",
      topic: "healthy conversations",
      scope: { brand: "specific", audience: "specific", contentType: "universal", platform: "universal" },
      brandId: "brand_mind-the-box",
      status: "active",
      usageCount: 12,
      lastUsedAt: "2026-01-19T08:00:00Z",
      rating: { score: 4 },
      createdAt: "2026-01-10T10:30:00Z"
    },
    {
      id: "theme_mtb_ent_boundaries",
      name: "Setting Boundaries",
      topic: "boundaries",
      scope: { brand: "specific", audience: "specific", contentType: "universal", platform: "universal" },
      brandId: "brand_mind-the-box",
      status: "active",
      usageCount: 8,
      lastUsedAt: "2026-01-18T14:20:00Z",
      rating: { score: 5 },
      createdAt: "2026-01-08T14:20:00Z"
    }
  ],
  total: 15,
  limit: 20,
  offset: 0
}
```

---

### search(query, filters?)

Full-text search across themes.

```javascript
// Input
{
  query: "communication relationships",
  filters: {
    brandId: "brand_mind-the-box",
    status: "active"
  }
}

// Process
1. Query FTS table: SELECT * FROM themes_fts WHERE themes_fts MATCH ?
2. Join with main table for additional filters
3. Rank by relevance
4. Return results

// Output (success)
{
  success: true,
  themes: [
    {
      id: "theme_mtb_ent_healthy-conversations",
      name: "Healthy Conversations",
      matchScore: 0.95,    // Relevance score
      matchedIn: ["topic", "keywords", "concept"]
    }
  ],
  total: 1,
  query: "communication relationships"
}
```

---

### save(theme)

Insert or update a theme in the database.

```javascript
// Input
{
  theme: {
    id: "theme_mtb_ent_healthy-conversations",
    name: "Healthy Conversations",
    topic: "healthy conversations",
    scope: { brand: "specific", audience: "specific", contentType: "universal", platform: "universal" },
    brandId: "brand_mind-the-box",
    audienceId: "audience_entrepreneurs",
    concept: "Building authentic connection...",
    keywords: ["communication", "connection", "mindfulness"],
    tone: "warm, encouraging",
    values: ["authenticity", "growth"],
    temporal: { type: "evergreen" },
    status: "draft",
    createdAt: "2026-01-19T10:30:00Z",
    createdBy: "ai"
  }
}

// Process
1. Validate required fields (id, name, topic, scope, createdAt, createdBy)
2. Validate references if provided (brandId, audienceId via their managers)
3. Serialize JSON fields (keywords, values, quality, rating)
4. INSERT OR REPLACE into themes table
5. Update FTS index
6. Return confirmation

// Output (success)
{
  success: true,
  themeId: "theme_mtb_ent_healthy-conversations",
  operation: "insert" | "update"
}
```

---

### update(themeId, updates)

Update specific fields on an existing theme.

```javascript
// Input
{
  themeId: "theme_mtb_ent_healthy-conversations",
  updates: {
    status: "active",
    keywords: ["communication", "connection", "mindfulness", "empathy"],
    quality: { humanReviewed: true, reviewedAt: "2026-01-19T12:00:00Z" }
  }
}

// Process
1. Verify theme exists
2. Validate update fields (can't change id, createdAt, createdBy)
3. Build UPDATE query for provided fields
4. Execute update
5. Update FTS index if searchable fields changed
6. Return updated theme

// Output (success)
{
  success: true,
  theme: { /* full updated theme */ },
  updatedFields: ["status", "keywords", "quality"]
}
```

---

### delete(themeId, options?)

Delete or archive a theme.

```javascript
// Input
{
  themeId: "theme_mtb_ent_healthy-conversations",
  options: {
    hard: false    // false = soft delete (archive), true = permanent delete
  }
}

// Process (soft delete - default)
1. Verify theme exists
2. UPDATE themes SET status = 'archived' WHERE id = ?
3. Return confirmation

// Process (hard delete)
1. Verify theme exists
2. Check no other themes reference this (derivedFrom)
3. DELETE FROM themes WHERE id = ?
4. Remove from FTS index
5. Return confirmation

// Output (success)
{
  success: true,
  themeId: "theme_mtb_ent_healthy-conversations",
  operation: "archived" | "deleted"
}
```

---

### exists(themeId)

Check if a theme exists.

```javascript
// Input
{
  themeId: "theme_mtb_ent_healthy-conversations"
}

// Process
1. SELECT 1 FROM themes WHERE id = ? LIMIT 1
2. Return boolean

// Output
true | false
```

---

### generateBatch(topic, scopeVariants, options?)

Generate multiple themes with different scopes from one topic.

```javascript
// Input
{
  topic: "healthy conversations",
  scopeVariants: [
    { brandId: "brand_mind-the-box", audienceId: "audience_entrepreneurs" },
    { brandId: "brand_mind-the-box", audienceId: "audience_executives" },
    { brandId: "brand_be-you-love-wins", audienceId: null }
  ],
  options: {
    autoSave: true,
    temporal: { type: "evergreen" }
  }
}

// Process
1. For each scope variant in parallel:
   - Call generate(topic, scope, options)
2. Collect all results
3. Return summary with all themes

// Output (success)
{
  success: true,
  themes: [
    { id: "theme_mtb_ent_healthy-conversations", brandId: "brand_mind-the-box", audienceId: "audience_entrepreneurs", ... },
    { id: "theme_mtb_exe_healthy-conversations", brandId: "brand_mind-the-box", audienceId: "audience_executives", ... },
    { id: "theme_bylw_healthy-conversations", brandId: "brand_be-you-love-wins", audienceId: null, ... }
  ],
  total: 3,
  failed: []
}

// Output (partial success)
{
  success: true,
  themes: [ /* 2 successful themes */ ],
  total: 2,
  failed: [
    { scope: { brandId: "brand_acme" }, error: { code: "BRAND_NOT_FOUND", ... } }
  ]
}
```

---

### deriveFrom(themeId, newScope)

Create a more specific theme from a broader theme.

```javascript
// Input
{
  themeId: "theme_universal_healthy-conversations",  // Fully universal theme
  newScope: {
    brandId: "brand_mind-the-box",
    audienceId: "audience_entrepreneurs",
    contentTypeId: null,
    platformId: null
  }
}

// Process
1. Load source theme via get(themeId)
2. Validate source theme has broader scope than target
   (can't derive universal from specific)
3. Load context for new scope IDs
4. Build refinement prompt using source theme + new context
5. AI adapts message/tone/keywords to new scope
6. Generate new theme with derivedFrom reference
7. Return derived theme

// Output (success)
{
  success: true,
  theme: {
    id: "theme_mtb_ent_healthy-conversations",
    name: "Healthy Conversations",
    topic: "healthy conversations",
    scope: { brand: "specific", audience: "specific", contentType: "universal", platform: "universal" },
    brandId: "brand_mind-the-box",
    audienceId: "audience_entrepreneurs",
    derivedFrom: "theme_universal_healthy-conversations",
    concept: "Building authentic connection...",  // Adapted for brand+audience
    message: "As an entrepreneur, transform...",  // Audience-specific
    keywords: ["communication", "leadership", "team", "dialogue"],  // Adjusted
    tone: "warm, empowering",
    values: ["authenticity", "growth"],
    createdAt: "2026-01-19T11:00:00Z",
    createdBy: "ai",
    usageCount: 0,
    status: "draft"
  }
}

// Output (error - can't derive)
{
  success: false,
  error: {
    code: "INVALID_DERIVATION",
    message: "Cannot derive broader scope from narrower scope",
    sourceScope: { brand: "specific", ... },
    targetScope: { brand: "universal", ... }
  }
}
```

---

### rate(themeId, rating)

Rate a theme for quality tracking.

```javascript
// Input
{
  themeId: "theme_mtb_ent_healthy-conversations",
  rating: {
    score: 4,           // 1-5 stars
    notes: "Good keywords, message could be stronger"
  }
}

// Process
1. Verify theme exists
2. Validate score (1-5)
3. Update rating field with timestamp
4. Return confirmation

// Output (success)
{
  success: true,
  themeId: "theme_mtb_ent_healthy-conversations",
  rating: {
    score: 4,
    ratedAt: "2026-01-19T14:00:00Z",
    notes: "Good keywords, message could be stronger"
  }
}
```

---

### incrementUsage(themeId)

Increment usage counter (called by TemplateEngine after successful render).

```javascript
// Input
{
  themeId: "theme_mtb_ent_healthy-conversations"
}

// Process
1. UPDATE themes SET
     usage_count = usage_count + 1,
     last_used_at = ?
   WHERE id = ?
2. Return new count

// Output (success)
{
  success: true,
  themeId: "theme_mtb_ent_healthy-conversations",
  usageCount: 13,
  lastUsedAt: "2026-01-19T15:30:00Z"
}
```

---

### findMatching(scope)

Find themes that match a given scope (for suggestions).

```javascript
// Input
{
  scope: {
    brandId: "brand_mind-the-box",
    audienceId: "audience_entrepreneurs",
    contentTypeId: "workbook",
    platformId: null
  }
}

// Process
1. Find themes that match OR are broader than the given scope
   - Exact matches (same brand, audience, content type)
   - Universal themes that can be used
   - Brand-specific but audience-universal themes
2. Order by relevance (exact matches first, then by usage)
3. Return matches

// Output (success)
{
  success: true,
  themes: [
    { id: "theme_mtb_ent_wb_...", matchType: "exact", usageCount: 5 },
    { id: "theme_mtb_ent_...", matchType: "broader", usageCount: 12 },
    { id: "theme_mtb_...", matchType: "broader", usageCount: 8 },
    { id: "theme_universal_...", matchType: "universal", usageCount: 3 }
  ],
  total: 4
}
```

---

### getStats(filters?)

Get usage statistics for themes.

```javascript
// Input
{
  filters: {
    brandId: "brand_mind-the-box",
    dateRange: {
      from: "2026-01-01",
      to: "2026-01-19"
    }
  }
}

// Process
1. Aggregate queries on themes table
2. Calculate totals, averages, distributions

// Output (success)
{
  success: true,
  stats: {
    totalThemes: 45,
    activeThemes: 32,
    totalUsage: 156,
    avgUsagePerTheme: 3.5,
    avgRating: 3.8,
    topThemes: [
      { id: "theme_mtb_ent_healthy-conversations", usageCount: 12, rating: 4 },
      { id: "theme_mtb_ent_boundaries", usageCount: 8, rating: 5 }
    ],
    byTemporalType: {
      evergreen: 30,
      seasonal: 10,
      trending: 5
    },
    byStatus: {
      draft: 5,
      active: 32,
      archived: 8
    },

    // === STALE THEMES (unused 90+ days with prior usage) ===
    staleThemes: [
      {
        id: "theme_mtb_ent_old-topic",
        name: "Old Topic",
        usageCount: 5,              // Was used before
        lastUsedAt: "2025-10-15",   // But not in 90+ days
        daysSinceUse: 96
      }
    ],
    staleThemeCount: 1
  }
}
```

---

## Configuration

```json
{
  "module": "ThemeGenerator",
  "version": "1.2.0",
  "description": "Generate and manage content themes",
  "type": "content-system",

  "config": {
    "database": {
      "path": "/data/themes.db",
      "table": "themes"
    },

    "cache": {
      "ttl": 172800,               // 48 hours in seconds (themes are brand assets)
      "staleWhileRevalidate": 86400  // Serve stale for 24h while regenerating
    },

    "ai": {
      "model": "claude-3-haiku",
      "temperature": 0.7,
      "maxRetries": 2
    },

    "defaults": {
      "keywordCount": {
        "min": 5,
        "max": 10
      },
      "temporal": {
        "type": "evergreen"
      },
      "status": "draft"
    },

    "naming": {
      "prefix": "theme_",
      "slugSeparator": "-"
    },

    "search": {
      "minQueryLength": 2,
      "maxResults": 100
    }
  }
}
```

### Config Explained

| Setting | Value | Purpose |
|---------|-------|---------|
| `database.path` | /data/themes.db | SQLite database file path |
| `database.table` | themes | Table name for theme storage |
| `cache.ttl` | 172800 (48h) | Theme cache duration. Themes are brand assets, not volatile data. |
| `cache.staleWhileRevalidate` | 86400 (24h) | Serve stale theme while regenerating in background |
| `ai.model` | claude-3-haiku | Default model for generation |
| `ai.temperature` | 0.7 | Creativity level (higher = more creative) |
| `defaults.keywordCount` | 5-10 | Default keyword range |
| `defaults.temporal.type` | evergreen | Default temporal type |
| `defaults.status` | draft | New themes start as draft |
| `naming.prefix` | theme_ | ID prefix for all themes |
| `search.minQueryLength` | 2 | Minimum characters for search |

---

## Error Handling

### Core Errors

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `BRAND_NOT_FOUND` | Brand ID doesn't exist | Check brandId |
| `AUDIENCE_NOT_FOUND` | Audience ID doesn't exist | Check audienceId |
| `THEME_NOT_FOUND` | Theme ID doesn't exist | Check themeId or generate new |
| `THEME_ALREADY_EXISTS` | Theme with same ID exists | Use different name or set allowDuplicate: true |
| `INVALID_DERIVATION` | Can't derive broader scope from narrower | Use a broader theme as source |
| `INVALID_RATING` | Rating score not 1-5 | Provide valid score |
| `INVALID_UPDATE` | Tried to update immutable field | Can't change id, createdAt, createdBy |
| `HAS_DEPENDENTS` | Theme has derived themes (hard delete) | Archive instead or delete dependents first |
| `AI_GENERATION_FAILED` | AICaller failed to generate | Check AICaller logs, retry |
| `INVALID_THEME_STRUCTURE` | Theme missing required fields | Check theme object structure |
| `DATABASE_ERROR` | SQLite operation failed | Check database file, logs |
| `DATABASE_INIT_FAILED` | Could not create/initialize database | Check permissions, disk space |
| `MIGRATION_FAILED` | Schema migration failed | Check migration scripts, rollback |
| `MODULE_NOT_READY` | Dependencies not initialized | Call initialize() on startup |

### Outcomes Integration Errors (v1.6.0)

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `OUTCOME_NOT_FOUND` | Outcome ID doesn't exist in library | Verify ID format and existence |
| `OUTCOME_LIMIT_EXCEEDED` | More than 3 target outcomes specified | Narrow focus to max 3 outcomes |
| `PERSPECTIVE_MISSING` | Outcome exists but no perspective defined | Generate without perspective (Tier 2), flag for addition |
| `BELIEF_SHIFT_UNAVAILABLE` | No belief shifts for this outcome | Generate with outcome focus only (Tier 3) |
| `TRANSFORMATION_CONTEXT_INCOMPLETE` | Missing required transformation data | Request human input or generate partial |
| `TRUTHS_OUTCOME_MISALIGNMENT` | Truths don't align with target outcomes | Regenerate with stronger outcome focus |
| `TRUTHS_DONT_FACILITATE_SHIFT` | Truths don't support belief shift | Regenerate with explicit shift focus |
| `THEME_PERSPECTIVE_MISMATCH` | Theme doesn't reflect Natasha's perspective | Regenerate using perspective components |
| `ANGLE_CONTRADICTS_VALUES` | Angle contradicts what_im_rejecting | Regenerate avoiding rejected narratives |

### Validation Checks

**On Theme Generation:**
1. Validate target_outcomes exist in library (if provided)
2. Load transformation context (outcomes, perspectives, beliefs, desired)
3. Generate angles that align with perspectives (if Tier 1)
4. Generate truths that facilitate belief shifts
5. Validate theme language reflects perspective (if Tier 1)
6. Validate truths relate to outcomes (min 3 of 5)
7. Validate no use of "what_im_rejecting" concepts (if Tier 1)

**On Theme Save:**
1. If targetOutcomes present, validate transformationFramework is populated
2. If angleMetadata.derivedFromPerspective present, validate outcome exists
3. Validate truths array length (3-7)

---

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| SQLite storage | Yes | Rich querying, full-text search, indexing, no external deps |
| 4-dimension scope | Yes | Themes can be specific/universal for brand, audience, content-type, platform |
| Optional fields | Yes | Not all themes need all data - partial themes are valid |
| Temporal metadata | Yes | Support evergreen, seasonal, date-specific, trending themes |
| Rating system | Yes | Track theme quality for future selection |
| Usage tracking | Yes | Helps identify valuable themes |
| Derivation chain | Yes | Universal → specific saves AI calls and ensures consistency |
| Status workflow | Yes | draft → active → archived lifecycle |
| AI-generated keywords | Yes | Consistent keyword style for image search |
| Truths (3-7) | Yes | Continuity anchors for multi-content campaigns. Min 3, target 5, max 7. |
| Cache TTL 48h+ | Yes | Themes are brand assets, not volatile data. Reduce API costs/latency. |
| Full-text search | Yes | Fast search across topic, keywords, concept |
| Schema migrations | Yes | Forward-only migrations via DatabaseHandler |

### Schema Migrations

When schema changes in future versions:
1. **Backup first**: DatabaseHandler creates `themes.db.bak` before any migration
2. DatabaseHandler manages migrations via version tracking (metadata table)
3. Migrations are forward-only (no downgrades)
4. Migrations run automatically on `initialize()` if schema version is outdated
5. Migration scripts stored in `/migrations/themes/`
6. Each migration is named: `{version}_{description}.sql`
7. On failure: restore from backup, log error, return DATABASE_MIGRATION_FAILED

### FTS Index Synchronization

Full-text search index is kept in sync with main table:

```javascript
// After INSERT/UPDATE on themes table (fields that affect search):
DELETE FROM themes_fts WHERE id = ?;
INSERT INTO themes_fts(id, name, topic, concept, message, keywords)
  SELECT id, name, topic, concept, message, keywords FROM themes WHERE id = ?;

// Fields that trigger FTS refresh: name, topic, concept, message, keywords
// Fields that do NOT trigger FTS refresh: truths, rating, usageCount, lastUsedAt, status
```

### Future Enhancements (v2)

For batch pre-generation during quiet times:

| Function | Input | Output | Purpose |
|----------|-------|--------|---------|
| `bulkSave(themes[])` | themes[] | result | Save multiple themes in one transaction |
| `bulkUpdateStatus(themeIds[], status)` | ids, status | result | Update status for multiple themes |

Not required for v1 - can add when batch generation patterns are established.

### Usage Count Increment

**Who increments `usageCount`?** TemplateEngine calls `ThemeGenerator.incrementUsage(themeId)` after successful content generation (not on preview, only on final render).

```javascript
// TemplateEngine.run() - after successful render
if (context.themeId) {
  await ThemeGenerator.incrementUsage(context.themeId);
}
```

This ensures:
- Reads don't inflate counts
- Previews don't inflate counts
- Only successful renders count as "usage"

---

## Operational Guarantees

### 1. Theme ID Uniqueness

Theme IDs encode scope to prevent collisions:

```javascript
// ID format: theme_{brandPrefix}_{audiencePrefix}_{slugified-topic}
// Prefixes omitted for universal dimensions

"theme_universal_healthy-conversations"           // Fully universal
"theme_mtb_healthy-conversations"                 // Brand-specific, audience-universal
"theme_mtb_ent_healthy-conversations"             // Brand + audience specific
"theme_mtb_ent_wb_healthy-conversations"          // Brand + audience + content-type specific
```

### 2. Keyword Quality

Keywords are optimized for image search:
- Relevant to topic and brand
- Common enough for good image results
- Specific enough to avoid generic images

```javascript
// Good keywords
["communication", "connection", "mindfulness", "dialogue", "listening"]

// Bad keywords (too generic or abstract)
["good", "things", "stuff", "important", "concept"]
```

### 3. Brand Alignment

Every generated theme reflects the brand's:
- Voice and tone
- Values and beliefs
- Target audience perspective

---

## Storage Structure

Themes are stored in a SQLite database at `/data/themes.db`.

### Database Schema

```sql
CREATE TABLE themes (
  -- Primary key
  id TEXT PRIMARY KEY,           -- e.g., "theme_mtb_healthy-conversations"

  -- Required fields
  name TEXT NOT NULL,
  topic TEXT NOT NULL,
  created_at TEXT NOT NULL,      -- ISO timestamp
  created_by TEXT NOT NULL,      -- "ai" | "manual"

  -- Scope (determines where theme applies)
  scope_brand TEXT NOT NULL,     -- "specific" | "universal"
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
  angle TEXT,
  keywords TEXT,                 -- JSON array stored as text
  tone TEXT,
  values TEXT,                   -- JSON array stored as text
  truths TEXT,                   -- JSON array of Truth objects (5 continuity anchors)

  -- Temporal metadata
  temporal_type TEXT NOT NULL DEFAULT 'evergreen',
  valid_from TEXT,               -- ISO date
  valid_until TEXT,              -- ISO date
  season_tags TEXT,              -- JSON array stored as text

  -- Tracking
  usage_count INTEGER DEFAULT 0,
  last_used_at TEXT,
  updated_at TEXT,               -- Last modification timestamp
  derived_from TEXT,             -- Reference to source theme ID

  -- Status
  status TEXT NOT NULL DEFAULT 'draft',

  -- Quality (nullable JSON)
  quality TEXT,                  -- JSON object: { aiConfidence, humanReviewed, ... }

  -- Rating (nullable JSON)
  rating TEXT,                   -- JSON object: { score, ratedAt, ratedBy, notes }

  -- === TRANSFORMATION CONTEXT (v1.6.0) ===
  target_outcomes TEXT,          -- JSON array of outcome IDs (max 3)
  transformation_framework TEXT, -- JSON object (full framework)
  angle_metadata TEXT,           -- JSON object (enhanced angle governance)

  -- Indexes created separately
  FOREIGN KEY (derived_from) REFERENCES themes(id)
);

-- Index for outcome-based queries (v1.6.0)
CREATE INDEX idx_themes_outcomes ON themes(target_outcomes);

-- Indexes for common queries
CREATE INDEX idx_themes_brand ON themes(brand_id);
CREATE INDEX idx_themes_audience ON themes(audience_id);
CREATE INDEX idx_themes_content_type ON themes(content_type_id);
CREATE INDEX idx_themes_platform ON themes(platform_id);
CREATE INDEX idx_themes_status ON themes(status);
CREATE INDEX idx_themes_temporal ON themes(temporal_type);
CREATE INDEX idx_themes_usage ON themes(usage_count DESC);
CREATE INDEX idx_themes_rating ON themes(rating);

-- Full-text search index
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

### Why SQLite?

| Benefit | Explanation |
|---------|-------------|
| Rich querying | Filter by any combination of scope, temporal, status, rating |
| Full-text search | Fast search across topic, keywords, concept |
| Indexing | Fast lookups by brand, audience, content type, platform |
| Transactions | Atomic updates for incrementUsage, ratings |
| No external deps | SQLite is embedded, no server needed |
| Portable | Single file can be backed up, versioned |

### JSON Fields

Some fields store JSON as TEXT for flexibility:
- `keywords`: `["communication", "connection", "mindfulness"]`
- `values`: `["authenticity", "growth"]`
- `season_tags`: `["Q1", "new-year"]`
- `quality`: `{"aiConfidence": 0.85, "humanReviewed": true}`
- `rating`: `{"score": 4, "ratedAt": "2026-01-19T12:00:00Z"}`

---

## Theme in Content Pipeline

```
User: "Create content about healthy conversations"
                    ↓
ThemeGenerator.generate("healthy conversations", brandId, { targetOutcomes: ["self-005"] })
                    ↓
                 Theme (with truths[], transformationFramework)
                    ↓
OutlineGenerator.generate({ themeId, templateId })
                    ↓
              Outline (references truths by index)
                    ↓
TemplateEngine.run(templateId, { themeId, outlineId, ... })
                    ↓
PromptBuilder uses theme.concept, theme.message, theme.transformationFramework, outline.sections[i]
                    ↓
ImageBrowser uses theme.keywords + outline.imageKeywords
                    ↓
                 Content (truths distributed across sections, transformation-aligned)
```

---

## Example: Theme With Full Transformation Context

**Input:**
```javascript
generate(
  "rest and productivity",
  { brand: "be_you_love_wins", audience: "universal", contentType: "universal", platform: "universal" },
  { targetOutcomes: ["self-005"] }  // Self-Worth
)
```

**Output (Tier 1 - Full Context):**
```javascript
{
  id: "theme_bylw_rest-productivity",
  name: "Rest as Radical Resistance",
  topic: "rest and productivity",

  concept: "Rejecting hustle culture's worth-through-productivity equation",
  message: "Your worth isn't earned—rest is infrastructure, not indulgence",
  angle: "Rest as radical resistance to hustle culture",

  angleMetadata: {
    source: "ai",
    locked: false,
    derivedFromPerspective: "self-005",
    generatedAngles: [
      "Rest as radical resistance to hustle culture",
      "Worth beyond productivity metrics",
      "Strategic rest as business infrastructure"
    ],
    selectionRationale: "Best alignment with anti-hustle positioning"
  },

  targetOutcomes: ["self-005"],

  transformationFramework: {
    primaryOutcome: {
      outcomeId: "self-005",
      outcomeName: "Self-Worth",
      outcomeDefinition: "Recognising inherent value independent of achievements",
      outcomeTheme: "Worth & Value"
    },

    beliefShifts: [
      {
        from: "I'm only valuable when I'm productive",
        to: "My worth is inherent—productivity is what I do, not who I am"
      }
    ],

    natashaPerspective: {
      coreBelief: "Your worth isn't earned through productivity—it's baseline humanity",
      brandExpression: "You don't have to earn permission to exist. Your worth is not on your to-do list.",
      signaturePhrases: [
        "Worth is baseline, not earned",
        "Rest as infrastructure, not indulgence"
      ],
      whatImRejecting: [
        "Hustle culture's productivity-as-worth equation",
        "The 'earn your rest' mentality"
      ]
    },

    audienceTransformation: {
      theyGain: [
        "Permission to rest without guilt",
        "Understanding that worth ≠ productivity"
      ],
      theyExperience: [
        "Relief from constant proving",
        "First moment of 'maybe I'm enough as I am'"
      ],
      theyWalkAwayWith: [
        "Language to challenge worth-through-productivity beliefs",
        "Reminder that worth is baseline, not earned"
      ]
    },

    contentIntent: "Help audience recognize they don't have to earn their worth through constant productivity"
  },

  truths: [
    {
      index: 0,
      statement: "Your worth isn't something you earn—it's your baseline humanity",
      application: "Challenge productivity-as-worth belief in opening section"
    },
    {
      index: 1,
      statement: "Rest is not a reward for productivity; it's a requirement for being human",
      application: "Permission-giving content throughout"
    },
    {
      index: 2,
      statement: "Doing less doesn't make you less",
      application: "Counter comparison and achievement culture"
    },
    {
      index: 3,
      statement: "Productivity is what you do, not who you are",
      application: "Separation of doing from being"
    },
    {
      index: 4,
      statement: "Strategic rest creates capacity—it's infrastructure, not indulgence",
      application: "Reframe rest as practical, not indulgent"
    }
  ],

  keywords: ["rest", "self-worth", "anti-hustle", "permission", "humanity"],

  scope: {
    brand: "specific",
    audience: "universal",
    contentType: "universal",
    platform: "universal"
  },
  brandId: "brand_be-you-love-wins",

  status: "active",
  createdBy: "ai",
  createdAt: "2026-01-19T10:00:00Z",
  usageCount: 0
}
```

**How This Gets Used:**

1. **TemplateEngine** reads `transformationFramework` to understand:
   - What belief shift to facilitate
   - What Natasha's perspective is
   - What audience should gain/experience

2. **ContentTextAssessor** validates:
   - Content reflects `natashaPerspective.coreBelief`
   - Content uses signature phrases naturally
   - Content doesn't use language from `whatImRejecting`

3. **AnalyticsHandler** tracks:
   - Which outcomes resonate most with audience
   - Which belief shifts show evidence in comments/engagement
   - Performance by `transformationFramework.primaryOutcome`

---

## Used By

| Module | Function | Purpose |
|--------|----------|---------|
| **OutlineGenerator** | get() | Load theme for outline generation (reads truths) |
| **TemplateEngine** | get() | Load theme for content generation |
| **Orchestrator** | generate() | Create theme from user request |
| **ContentManager** | list() | Show available themes |
| **ImageBrowser** | get() | Access theme.keywords for image search |

---

## Dependencies

| Depends On | For |
|------------|-----|
| DatabaseHandler | SQLite database operations (CRUD, queries, FTS) |
| BrandManager | Load brand context for generation, truthApprovalMode |
| AudienceManager | Load audience context for generation |
| FileManager | Read Outcomes Library (perspectives, belief shifts) |
| PromptBuilder | Build theme generation prompt |
| AICaller | Generate theme from topic |

### Outcomes Library (External Data)

**Location:** `C:\Users\Business\BrandLib\outcomes`

The Outcomes Library contains transformation context that feeds into theme generation:
- Outcome definitions (what we're transforming)
- Belief shifts (from → to)
- Desired outcomes (what audience gains)

**Recommended Structure:** Outcomes library entries SHOULD include `natasha_perspective` field for optimal theme generation:

```javascript
// outcomes.json entry structure
{
  "id": "self-005",
  "name": "Self-Worth",
  "definition": "Recognising inherent value independent of achievements",
  "theme": "Worth & Value",
  // ... existing fields ...

  "natasha_perspective": {  // RECOMMENDED, not required
    "core_belief": "Your worth isn't earned through productivity—it's baseline humanity",
    "what_this_means_to_me": [
      "Worth is inherent, not conditional",
      "Rest doesn't need to be earned"
    ],
    "what_im_rejecting": [
      "Hustle culture's productivity-as-worth equation"
    ],
    "how_i_talk_about_this": {
      "mind_the_box": "Strategic rest creates capacity—worth exists whether producing or not",
      "be_you_love_wins": "You don't have to earn permission to exist"
    },
    "signature_phrases": [
      "Worth is baseline, not earned",
      "Rest as infrastructure, not indulgence"
    ],
    "content_angles_this_generates": [  // Optional - if empty, auto-generate from above
      "Rest as radical resistance to hustle culture",
      "Worth beyond productivity metrics"
    ]
  }
}
```

**Key Insight:** Angles are VIEWS, not DATA. They derive from perspectives, not stored separately.

---

## Generation Quality Tiers

Theme generation quality depends on available transformation context:

| Tier | Context Available | Result Quality | Warning |
|------|-------------------|----------------|---------|
| **Tier 1: Full** | Outcome + Perspective + Beliefs + Desired | Authentic voice, strategic alignment, full framework | None |
| **Tier 2: Good** | Outcome + Beliefs + Desired (no perspective) | Strategic alignment, generic voice | "Theme generated without perspective guidance" |
| **Tier 3: Basic** | Outcome only (no perspective, beliefs, desired) | Topic-focused, minimal transformation strategy | "Theme generated with minimal transformation context" |
| **Tier 4: Legacy** | No targetOutcomes provided | Traditional theme generation (pre-integration) | None (backward compatible) |

**Fallback Behavior:**

| Missing Component | System Behavior | Warning Level |
|-------------------|-----------------|---------------|
| `natasha_perspective` | Generate without perspective, use generic angles | Warning |
| `belief_shifts` | Generate without targeted belief shift | Silent warning |
| `desired_outcomes` | Generate without audience transformation detail | Silent warning |
| All transformation data | Fall back to legacy generation | Warning |

**Rollout Strategy:**
1. Start with 5 most-used outcomes (add perspectives)
2. Test theme generation quality
3. Gradually add more perspectives over time
4. System works throughout rollout (graceful degradation)

---

## Generation Process with Outcomes

### Step-by-Step Flow

**1. Input Validation**
```javascript
input: {
  topic: "self-care for busy parents",
  scope: { brand: "be_you_love_wins", ... },
  options: {
    targetOutcomes: ["self-005", "self-004"]  // Self-Worth, Self-Compassion
  }
}
```

**2. Load Transformation Context**
- Load outcome definitions from outcomes library
- Load Natasha's perspectives on these outcomes (if available)
- Load belief shifts (from → to)
- Load desired outcomes (what audience gains/experiences/walks away with)

**3. Determine Quality Tier**
- If perspective available → Tier 1 (Full)
- If no perspective but outcomes → Tier 2 (Good) or Tier 3 (Basic)
- If no outcomes → Tier 4 (Legacy)

**4. Generate Content Angles**
- If `content_angles_this_generates` exists → use pre-defined angles
- If not → auto-generate from perspective components:
  - core_belief
  - what_this_means_to_me
  - what_im_rejecting
  - brand_expression (for current brand)
  - signature_phrases

**5. Build AI Prompt**
- Include transformation context (at available tier)
- Include Natasha's perspective (if Tier 1)
- Include generated angles
- Include belief shifts to facilitate
- Include brand context

**6. Generate Theme**
- AI generates theme reflecting perspectives
- Theme language uses signature phrases (if Tier 1)
- Theme avoids "what_im_rejecting" concepts (if Tier 1)
- Angle selected from generated options

**7. Generate Truths (3-7)**
- Truths challenge "from" beliefs
- Truths reinforce "to" beliefs
- Truths use perspective language (if Tier 1)
- Truths provide continuity across multi-content

**8. Validate & Enrich**
- Check truths align with outcomes (min 3 of 5)
- Check truths facilitate belief shifts
- Check theme reflects perspective (if Tier 1)
- Add transformationFramework to theme
- Add angleMetadata to theme

**9. Save & Return**
- Save theme to database
- Return theme with full transformation context

---

## Build Notes

*Notes for implementation and future updates:*

### Version Compatibility (Dev Team Gap - 2026-01-20)
| Dependency | Min Version | Notes |
|------------|-------------|-------|
| themes.library | 1.0.0+ | Library schema must match Theme type |
| outcomes.library | 1.0.0+ | For transformation context (targetOutcomes) |
| DatabaseHandler | 1.0.0+ | SQLite operations |

### Cross-Library Integrity
- When theme archived: OutlineGenerator should warn if active outlines reference it
- `ref_status` field tracks dependency health

### Audience Integration (Dev Team Gap - 2026-01-20)
- **engagementTriggers usage**: Theme.angle should be selected from Audience.engagementTriggers.positive
- Pass engagement triggers to PromptBuilder as angle selection criteria
- Validate generated angles align with audience's positive triggers
- Avoid themes that conflict with audience's negative triggers

### Framework Selection Integration (2026-01-20)

ThemeGenerator selects appropriate frameworks (Layer 1 + Layer 2) for the theme:

```
┌─────────────────────────────────────────────────────────────────┐
│                  FRAMEWORK SELECTION IN THEMEGENERATOR           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Identify content category from targetOutcomes               │
│     (skill_building, emotional_processing, behaviour_change)    │
│                                                                  │
│  2. Query frameworks library by category                        │
│     - Filter by category                                        │
│     - Filter by bestFor match                                   │
│     - Filter by cognitiveLevels match                           │
│                                                                  │
│  3. Check for proprietary frameworks                            │
│     - If brand has proprietary framework for category, include  │
│     - Load framework_interpretations for evidence→proprietary   │
│                                                                  │
│  4. Score and select 1-3 frameworks                             │
│     - Relevance to theme goal                                   │
│     - Audience cognitive level compatibility                    │
│     - pairsWellWith complementarity                             │
│                                                                  │
│  5. Store in theme:                                             │
│     theme.frameworkIds = ["kolb_experiential", "mtb_five_step"] │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Theme Type Fields (Framework-Related):**
```javascript
{
  // ... existing theme fields ...

  // Framework selection (populated by ThemeGenerator)
  frameworkIds?: string[],           // Selected evidence-based + proprietary framework IDs
  primaryFrameworkId?: string,       // Main framework driving structure

  // Framework selection metadata
  frameworkSelection?: {
    category: string,                // e.g., "skill_building"
    evidenceFrameworks: string[],    // Selected Layer 1 frameworks
    proprietaryFrameworks: string[], // Selected Layer 2 frameworks (if brand has them)
    selectionRationale: string       // Why these frameworks were chosen
  }
}
```

**Dependencies:**
- Frameworks Library (read): Query frameworks by category, bestFor, cognitiveLevels
- Librarian (read): Search/recommend frameworks

**Handoff to OutlineGenerator:**
- OutlineGenerator receives `theme.frameworkIds`
- OutlineGenerator maps framework stages to section sequence
- OutlineGenerator applies proprietary interpretations

---

*Spec version: 1.7.0*
*Created: 2026-01-19*
*Updated: 2026-01-19 - v1.6.0: Full transformationFramework, expanded angleMetadata, quality tiers, processing flow, Outcomes Library integration*
*Updated: 2026-01-19 - v1.7.0: Added truth dependsOn, visual keywords validation, outcome cohesion check, stale themes reporting*
*Status: Approved*
