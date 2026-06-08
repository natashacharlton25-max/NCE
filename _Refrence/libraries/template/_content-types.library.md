# Content Types Library

> **System:** Template
> **Repository:** Template Repository
> **Path:** `/data/content-types.sqlite`
> **Library Type:** reference
> **Status:** Draft
> **Source:** Migrated from BrandKit `content/types`

---

## Overview

Content type configurations defining structure and requirements for different content formats. Each content type defines the expected structure, typical length, tone variations, and required/optional sections for that format.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `TemplateEngine` | Read via FileManager | Content type config |
| `OutlineGenerator` | Read via FileManager | Content type for planDepth |
| `TemplateFactory` | Read via FileManager | Template creation |

---

## SQLite Schema

### Tables

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

CREATE TABLE content_types (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW content_types_latest AS
SELECT * FROM content_types t1
WHERE version = (SELECT MAX(version) FROM content_types t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/ContentType.ts`
> Source: BrandKit content/types

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "contenttype_personal-story") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `purpose` | string | Essential | What this content type achieves |
| `planDepth` | enum | Essential | 'minimal' / 'standard' / 'detailed' |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `structure` | string[] | [] | Essential | Ordered section structure |
| `typicalLength` | string | null | Supporting | Expected length (words/pages) |
| `toneVariation` | string | null | Supporting | How tone differs from default |
| `requiredSections` | string[] | [] | Essential | Section specs required |
| `optionalSections` | string[] | [] | Supporting | Optional section specs |
| `platforms` | string[] | [] | Supporting | Where this content goes |
| `constraints` | Constraints | {} | Supporting | Length, format constraints |
| `bestFor` | string[] | [] | Supporting | Use cases this type suits |

### Constraints Object

```json
{
  "minLength": 800,
  "maxLength": 2000,
  "format": "long-form",
  "readingLevel": "general"
}
```

### planDepth Enum

| Value | Description | Outline Detail |
|-------|-------------|----------------|
| `minimal` | Quick content (emails, social) | High-level guidance only |
| `standard` | Regular content (blog posts) | Section-by-section guidance |
| `detailed` | Complex content (workbooks, courses) | Paragraph-level planning |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `planDepth` | Complexity indicator |
| `purpose` | Quick description |

**Example preview JSON:**

```json
{
  "name": "Lived Experience Story",
  "planDepth": "standard",
  "purpose": "Share real experiences that create 'me too' moments"
}
```

---

## Staging Area

**Path:** `/repos/drafts/content-types/`

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `planDepth` | No | Yes | No | Enum filter |
| `status` | No | Yes | No | Enum filter |
| `platforms` | No | Yes | No | Array contains |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Finite content types |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | no | Small entries |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "contenttype_blog-post",
  "name": "Blog Post",
  "schemaVersion": "1.0.0",
  "status": "active",
  "purpose": "Provide actionable information on a specific topic",
  "planDepth": "standard"
}
```

### Complete (all fields)

```json
{
  "id": "contenttype_personal-story",
  "name": "Lived Experience Story",
  "schemaVersion": "1.0.0",
  "status": "active",
  "purpose": "Share real experiences that create 'me too' moments and show proof of change",
  "planDepth": "standard",
  "structure": [
    "Hook/recognition moment (2-3 sentences)",
    "The difficult bit (what was happening, without graphic detail)",
    "The shift (what changed, what helped)",
    "Where I am now (honest, not 'fixed')",
    "What I'd tell someone in that place (direct address)"
  ],
  "typicalLength": "800-1500 words",
  "toneVariation": "Most personal, warmest, first person singular",
  "requiredSections": ["section_hook", "section_story", "section_reflection"],
  "optionalSections": ["section_cta", "section_resources"],
  "platforms": ["blog", "newsletter", "linkedin"],
  "constraints": {
    "minLength": 800,
    "maxLength": 1500,
    "format": "long-form",
    "readingLevel": "general"
  },
  "bestFor": ["authenticity building", "trust building", "connection"]
}
```

### Additional Content Types

```json
{
  "id": "contenttype_practical-guide",
  "name": "How-to Guide",
  "purpose": "Provide actionable, usable information on a specific topic",
  "structure": [
    "What this is and who it's for",
    "Why this matters (brief)",
    "The core content (steps, options, approaches)",
    "Common challenges and what helps",
    "Where to go next"
  ],
  "typicalLength": "1000-2000 words",
  "toneVariation": "Still warm, but more instructional",
  "planDepth": "standard"
}
```

```json
{
  "id": "contenttype_workbook",
  "name": "Downloadable Resource",
  "purpose": "Provide interactive, usable tools for self-directed work",
  "structure": [
    "Cover page with clear title",
    "Introduction (what it is, how to use it)",
    "Core content sections with activities",
    "Reflection/integration section",
    "What's next / additional resources"
  ],
  "typicalLength": "10-30 pages depending on scope",
  "toneVariation": "Instructional but warm, space for user's own words",
  "planDepth": "detailed"
}
```

```json
{
  "id": "contenttype_psychoeducation",
  "name": "Understanding [Topic]",
  "purpose": "Explain the 'why' behind experiences to reduce shame and increase self-compassion",
  "structure": [
    "Recognition/validation opening",
    "What's actually happening (accessible explanation)",
    "Why this is a normal response (normalising)",
    "What this means for you (practical application)",
    "Self-compassion prompt"
  ],
  "typicalLength": "800-1500 words",
  "toneVariation": "Knowledgeable but warm, second person",
  "planDepth": "standard"
}
```

```json
{
  "id": "contenttype_social",
  "name": "Social Media Post",
  "purpose": "Short-form content for social platforms - the generic structure before platform-specific formatting",
  "planDepth": "minimal",
  "structure": [
    "Hook (attention-grabbing opener)",
    "Core message (1-3 key points)",
    "Call to action or engagement prompt"
  ],
  "typicalLength": "50-300 words depending on platform",
  "toneVariation": "Conversational, direct, platform-appropriate",
  "requiredSections": ["section_hook", "section_cta"],
  "optionalSections": ["section_body"],
  "platforms": ["instagram", "linkedin", "twitter", "facebook", "threads"],
  "constraints": {
    "format": "short-form",
    "readingLevel": "casual"
  },
  "bestFor": ["engagement", "awareness", "community building", "traffic driving"]
}
```

```json
{
  "id": "contenttype_email",
  "name": "Email",
  "purpose": "Direct communication to subscriber list - newsletters, sequences, announcements",
  "planDepth": "minimal",
  "structure": [
    "Subject line",
    "Opening hook/greeting",
    "Main content (value or message)",
    "Call to action",
    "Sign-off"
  ],
  "typicalLength": "200-800 words",
  "toneVariation": "Personal, conversational, varies by email type",
  "requiredSections": ["section_hook", "section_body", "section_cta"],
  "optionalSections": ["section_ps"],
  "platforms": ["email"],
  "bestFor": ["nurturing", "announcements", "sales", "relationship building"]
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `contenttype_{slug}` pattern | `CONTENTTYPE_INVALID_ID` |
| Required fields | All required fields present | `CONTENTTYPE_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `CONTENTTYPE_DUPLICATE_ID` |
| planDepth valid | Must be minimal/standard/detailed | `CONTENTTYPE_INVALID_PLANDEPTH` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | System-defined |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `templates` | Templates reference one content type |
| `outlines` | Outlines specify content type |
| `section-specs` | Content types define required sections |

---

## Migration Notes

**From BrandKit:**
- `content/types/default/example-wwas.json` → content_types array
- Structure array maps to template section ordering

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandKit content/types*
