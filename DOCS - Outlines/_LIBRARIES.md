# Content Libraries

> **Purpose:** Master index of all SQLite content libraries
> **Status:** Placeholder
> **Version:** 1.0.0

---

## Storage Pattern

**Hierarchy:** System → Repository → Library → SQLite Storage

| Concept | Description |
|---------|-------------|
| **System** | Domain grouping (Brand, Content, AI, Template, Social, Visual) |
| **Repository** | Collection of related libraries owned by a system |
| **Library** | Single SQLite file containing versioned entries |
| **Draft** | JSON file in staging area before finalization |

**Data Flow:**
```
/repos/drafts/{library}/*.json  →  Human editing
        ↓ (approve & finalize)
/data/{library}.sqlite          →  Immutable versioned records
```

---

## All Libraries by System

### Brand System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| brands | `/data/brands.sqlite` | core | BrandManager | ThemeGenerator, TemplateEngine |
| audiences | `/data/audiences.sqlite` | core | AudienceManager | ThemeGenerator, TemplateEngine |
| voices | `/data/voices.sqlite` | core | BrandManager | PromptBuilder |
| tones | `/data/tones.sqlite` | core | BrandManager | PromptBuilder |

### Content System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| themes | `/data/themes.sqlite` | generated | ThemeGenerator | OutlineGenerator, TemplateEngine |
| outlines | `/data/outlines.sqlite` | generated | OutlineGenerator | TemplateEngine |
| outcomes | `/data/outcomes.sqlite` | core | Human (manual) | ThemeGenerator |

### AI System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| prompts | `/data/prompts.sqlite` | reference | Human (manual) | PromptBuilder |
| model-configs | `/data/model-configs.sqlite` | reference | Human (manual) | AICaller |

### Template System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| templates | `/data/templates.sqlite` | derived | TemplateFactory | TemplateEngine, LegalDocCreator |
| section-specs | `/data/section-specs.sqlite` | reference | Human (manual) | TemplateEngine, OutlineGenerator |
| content-types | `/data/content-types.sqlite` | reference | Human (manual) | TemplateEngine, OutlineGenerator |
| cognitive-types | `/data/cognitive-types.sqlite` | reference | Human (manual) | OutlineGenerator |

### Social System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| social-templates | `/data/social-templates.sqlite` | reference | Human (manual) | SocialMediaManager |
| email-templates | `/data/email-templates.sqlite` | reference | Human (manual) | EmailManager |

### Visual System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| visual-brands | `/data/visual-brands.sqlite` | core | VisualManager | Renderers |

---

## Library Types

| Type | Description | Can Rebuild? | Staging Area? |
|------|-------------|--------------|---------------|
| **core** | Human-curated source content | No | Yes |
| **derived** | Computed from other libraries | Yes | Optional |
| **generated** | AI-generated content | Yes | Yes (for review) |
| **reference** | System configuration | No | Yes |

---

## Library Status

| Status | Meaning |
|--------|---------|
| **Draft** | Schema defined, no entries yet |
| **Seeded** | Has example/starter entries |
| **Active** | Production ready with real data |

---

# Individual Library Documentation

---

# 1. Brands Library

> **System:** Brand
> **Path:** `/data/brands.sqlite`
> **Library Type:** core
> **Status:** Draft

---

## Overview

Brand definitions including voice, values, and truthApprovalMode settings. Core source of truth for brand identity across all content generation.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `BrandManager` | Read/Write (Owner) | CRUD operations on brands |
| `ThemeGenerator` | Read via FileManager | Brand context for theme generation |
| `TemplateEngine` | Read via FileManager | Brand context for content generation |

---

## SQLite Schema

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE TABLE brands (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW brands_latest AS
SELECT * FROM brands t1
WHERE version = (SELECT MAX(version) FROM brands t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Brand.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "brand_mind-the-box") |
| `name` | string | Essential | Human-readable brand name |
| `schemaVersion` | string | Essential | Schema version (e.g., "1.0.0") |
| `status` | EntryStatus | Essential | Lifecycle state |
| `voice` | object | Essential | Brand voice definition |
| `values` | string[] | Essential | Core brand values |
| `truthApprovalMode` | enum | Essential | strict / guided / trusted |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `description` | string | null | Supporting | Brand description |
| `guidelines` | object | {} | Truncatable | Extended brand guidelines |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `truthApprovalMode` | Quick filter indicator |

---

## Staging Area

**Path:** `/repos/drafts/brands/`

Draft files follow pattern: `brand-{slug}-{timestamp}.json`

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Few brands per business |
| **Entry Size** | medium | 1-5KB typical |
| **FTS Enabled** | yes | name field |
| **Lazy Loading** | no | Small entries, load fully |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `BrandManager` | Via staging → finalize |
| Update | `BrandManager` | Creates new version |
| Archive | `BrandManager` | Soft delete |
| Delete | Never | Archive instead |

---

# 2. Audiences Library

> **System:** Brand
> **Path:** `/data/audiences.sqlite`
> **Library Type:** core
> **Status:** Draft

---

## Overview

Target audience profiles for content personalization and theme targeting.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `AudienceManager` | Read/Write (Owner) | CRUD operations on audiences |
| `ThemeGenerator` | Read via FileManager | Audience context for theme generation |
| `TemplateEngine` | Read via FileManager | Audience context for content generation |

---

## SQLite Schema

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE TABLE audiences (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW audiences_latest AS
SELECT * FROM audiences t1
WHERE version = (SELECT MAX(version) FROM audiences t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Audience.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "audience_entrepreneurs") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `demographics` | object | {} | Supporting | Demographic details |
| `painPoints` | string[] | [] | Supporting | Key challenges |
| `goals` | string[] | [] | Supporting | Desired outcomes |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |

---

## Staging Area

**Path:** `/repos/drafts/audiences/`

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Few audiences per brand |
| **Entry Size** | medium | 1-5KB typical |
| **FTS Enabled** | yes | name field |
| **Lazy Loading** | no | Small entries |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `AudienceManager` | Via staging → finalize |
| Update | `AudienceManager` | Creates new version |
| Archive | `AudienceManager` | Soft delete |
| Delete | Never | Archive instead |

---

# 3. Outcomes Library

> **System:** Content
> **Path:** `/data/outcomes.sqlite`
> **Library Type:** core
> **Status:** Draft

---

## Overview

Transformation outcomes that content aims to produce in audiences. Used for theme targeting via `target_outcomes[]`.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `ThemeGenerator` | Read via FileManager | target_outcomes field |

---

## SQLite Schema

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE TABLE outcomes (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW outcomes_latest AS
SELECT * FROM outcomes t1
WHERE version = (SELECT MAX(version) FROM outcomes t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Outcome.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "outcome_clarity") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Curated outcome library |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | yes | name field |
| **Lazy Loading** | no | Small entries |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | Curated content |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

# 4. Themes Library

> **System:** Content
> **Path:** `/data/themes.sqlite`
> **Library Type:** generated
> **Status:** Draft

---

## Overview

AI-generated content themes. Generated by ThemeGenerator, reviewed by humans before production use.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `ThemeGenerator` | Read/Write (Owner) | Generate and store themes |
| `OutlineGenerator` | Read via Librarian | Theme context for outline generation |
| `TemplateEngine` | Read via Librarian | Theme context for content generation |

---

## SQLite Schema

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid',
  review_status TEXT DEFAULT 'pending'  -- AI-generated: pending/approved/rejected
);

CREATE TABLE themes (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW themes_latest AS
SELECT * FROM themes t1
WHERE version = (SELECT MAX(version) FROM themes t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Theme.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier |
| `name` | string | Essential | Theme name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `brandId` | string | Essential | Reference to brand |
| `audienceId` | string | Essential | Reference to audience |
| `target_outcomes` | string[] | Essential | Outcome IDs |

---

## Review Status (AI-generated)

| Value | Description | Can AI Use? |
|-------|-------------|-------------|
| `pending` | AI-generated, awaiting review | No |
| `approved` | Human verified | Yes |
| `rejected` | Human marked incorrect | No |

---

## Staging Area

**Path:** `/repos/drafts/themes/`

AI-generated themes go to staging for human review before finalization.

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | 100-1000 | Many themes per brand |
| **Entry Size** | medium | 1-5KB typical |
| **FTS Enabled** | yes | name, angles |
| **Lazy Loading** | yes | Preview in index |

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `brands` | `brandId` | hard | orphan if brand archived |
| `audiences` | `audienceId` | hard | orphan if audience archived |
| `outcomes` | `target_outcomes[]` | soft | stale if outcome archived |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `ThemeGenerator` | AI-generated, via staging |
| Update | `ThemeGenerator` | Creates new version |
| Archive | `ThemeGenerator` | Soft delete |
| Delete | Never | Archive instead |

---

# 5. Prompts Library

> **System:** AI
> **Path:** `/data/prompts.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

Prompt templates for AI interactions. Human-curated, version-controlled.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `PromptBuilder` | Read via FileManager | Load prompt templates |

---

## SQLite Schema

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE TABLE prompts (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW prompts_latest AS
SELECT * FROM prompts t1
WHERE version = (SELECT MAX(version) FROM prompts t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Prompt.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "prompt_theme-generation") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `template` | string | Essential | Prompt template text |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | 100-500 | Many prompts |
| **Entry Size** | medium | 1-10KB (prompt text) |
| **FTS Enabled** | yes | name, tags |
| **Lazy Loading** | yes | Load template on demand |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | Curated prompts |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

# 6. Section Specs Library

> **System:** Template
> **Path:** `/data/section-specs.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

Section type definitions for templates. Defines structure and requirements for content sections.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `TemplateEngine` | Read via FileManager | Section specs for generation |
| `OutlineGenerator` | Read via FileManager | Section specs for outline structure |

---

## SQLite Schema

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE TABLE section_specs (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW section_specs_latest AS
SELECT * FROM section_specs t1
WHERE version = (SELECT MAX(version) FROM section_specs t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/SectionSpec.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "section_title") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Finite section types |
| **Entry Size** | medium | 1-5KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | yes | Load fields on demand |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | System-defined |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

# 7. Content Types Library

> **System:** Template
> **Path:** `/data/content-types.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

Content type configurations (workbook, blog-post, email, etc.).

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `TemplateEngine` | Read via FileManager | Content type config |
| `OutlineGenerator` | Read via FileManager | Content type for structure |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Finite content types |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | no | Small entries |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | System-defined |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

# 8. Cognitive Types Library

> **System:** Template
> **Path:** `/data/cognitive-types.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

Cognitive framework definitions for content structure and reasoning patterns.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `OutlineGenerator` | Read via FileManager | Cognitive types for outline reasoning |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Finite frameworks |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | no | Small entries |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | System-defined |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

# 9. Templates Library

> **System:** Template
> **Path:** `/data/templates.sqlite`
> **Library Type:** derived
> **Status:** Draft

---

## Overview

Document templates for content generation. Managed by TemplateFactory.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `TemplateFactory` | Read/Write (Owner) | CRUD operations |
| `TemplateEngine` | Read via Librarian | Template for content generation |
| `LegalDocCreator` | Read via Librarian | Template for document generation |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Finite template types |
| **Entry Size** | medium | 1-10KB typical |
| **FTS Enabled** | yes | name, category |
| **Lazy Loading** | yes | Load sections on demand |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `TemplateFactory` | Module-managed |
| Update | `TemplateFactory` | Creates new version |
| Archive | `TemplateFactory` | Soft delete |
| Delete | Never | Archive instead |

---

# 10. Social Templates Library

> **System:** Social
> **Path:** `/data/social-templates.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

Social media post templates by platform and format.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `SocialMediaManager` | Read via FileManager | Social templates for post generation |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Finite social formats |
| **Entry Size** | medium | 1-5KB typical |
| **FTS Enabled** | yes | name, platform |
| **Lazy Loading** | yes | Load template on demand |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | Curated templates |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

# 11. Email Templates Library

> **System:** Social
> **Path:** `/data/email-templates.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

Email template definitions for email generation.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `EmailManager` | Read via FileManager | Email templates for generation |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Finite email types |
| **Entry Size** | medium | 1-5KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | yes | Load template on demand |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | Curated templates |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

# 12. Voices Library

> **System:** Brand
> **Path:** `/data/voices.sqlite`
> **Library Type:** core
> **Status:** Draft

---

## Overview

Voice definitions for brand communication style.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `BrandManager` | Read/Write (Owner) | Voice management |
| `PromptBuilder` | Read via FileManager | Voice context for prompts |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Few voices per brand |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | no | ID lookup only |
| **Lazy Loading** | no | Small entries |

---

# 13. Tones Library

> **System:** Brand
> **Path:** `/data/tones.sqlite`
> **Library Type:** core
> **Status:** Draft

---

## Overview

Tone modifiers for voice variations (e.g., formal, casual, urgent).

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `BrandManager` | Read/Write (Owner) | Tone management |
| `PromptBuilder` | Read via FileManager | Tone context for prompts |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Finite tone options |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | no | ID lookup only |
| **Lazy Loading** | no | Small entries |

---

# 14. Outlines Library

> **System:** Content
> **Path:** `/data/outlines.sqlite`
> **Library Type:** generated
> **Status:** Draft

---

## Overview

AI-generated content outlines. Generated by OutlineGenerator.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `OutlineGenerator` | Read/Write (Owner) | Generate and store outlines |
| `TemplateEngine` | Read via Librarian | Outline for content generation |

---

## Review Status (AI-generated)

| Value | Description | Can AI Use? |
|-------|-------------|-------------|
| `pending` | AI-generated, awaiting review | No |
| `approved` | Human verified | Yes |
| `rejected` | Human marked incorrect | No |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | 100-1000+ | Many outlines |
| **Entry Size** | medium | 2-10KB typical |
| **FTS Enabled** | yes | name, sections |
| **Lazy Loading** | yes | Preview in index |

---

# 15. Model Configs Library

> **System:** AI
> **Path:** `/data/model-configs.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

AI model configuration settings (temperature, max tokens, etc.).

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `AICaller` | Read via FileManager | Model settings for API calls |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Few model configs |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | no | ID lookup only |
| **Lazy Loading** | no | Small entries |

---

# 16. Visual Brands Library

> **System:** Visual
> **Path:** `/data/visual-brands.sqlite`
> **Library Type:** core
> **Status:** Draft

---

## Overview

Visual brand assets and style definitions.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `VisualManager` | Read/Write (Owner) | Visual brand management |
| `Renderers` | Read via FileManager | Visual styling for output |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Few visual brands |
| **Entry Size** | medium | 1-5KB (may ref external assets) |
| **FTS Enabled** | no | ID lookup only |
| **Lazy Loading** | yes | Load assets on demand |

---

## Entry Status Enum (All Libraries)

| Value | Description | AI Visibility |
|-------|-------------|---------------|
| `draft` | Incomplete, not ready | Hidden |
| `active` | Production-ready | Visible |
| `archived` | Deprecated, kept for history | Hidden |
| `flagged` | Needs human review | Hidden |

---

## Referential Integrity

All libraries track reference status via `ref_status` in their `_index` table:

| Status | Meaning | Action |
|--------|---------|--------|
| `valid` | All dependencies exist and are active | None |
| `stale` | Soft dependency archived | Warning on access |
| `orphaned` | Hard dependency missing | Block usage |

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Status: Placeholder - all libraries Draft*
