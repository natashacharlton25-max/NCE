# Brands Library

> **System:** Brand
> **Repository:** Brand Repository
> **Path:** `/data/brands.sqlite`
> **Library Type:** core
> **Status:** Draft
> **Source:** Migrated from BrandKit `core/identity` + `core/purpose`

---

## Overview

Brand definitions including identity, purpose, voice configuration, values, and content governance settings. Core source of truth for brand identity across all content generation.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `BrandManager` | Read/Write (Owner) | CRUD operations on brands |
| `ThemeGenerator` | Read via FileManager | Brand context for theme generation |
| `TemplateEngine` | Read via FileManager | Brand context for content generation |
| `PromptBuilder` | Read via FileManager | Voice/tone injection into prompts |

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
  tags TEXT,                     -- JSON array
  preview JSON,                  -- Key fields for UI
  ref_status TEXT DEFAULT 'valid'
);

-- Full versioned data
CREATE TABLE brands (
  id TEXT,
  version INTEGER,
  data JSON,                     -- Full brand as JSON
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

-- Latest version view
CREATE VIEW brands_latest AS
SELECT * FROM brands t1
WHERE version = (SELECT MAX(version) FROM brands t2 WHERE t2.id = t1.id);

-- Full-text search
CREATE VIRTUAL TABLE brands_fts USING fts5(
  id,
  name,
  tagline,
  content='brands',
  content_rowid='rowid'
);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Brand.ts`
> Source: BrandKit `01-brand-identity` + `02-brand-purpose`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "brand_mind-the-box") |
| `name` | string | Essential | Official brand name |
| `schemaVersion` | string | Essential | Schema version (e.g., "1.0.0") |
| `status` | EntryStatus | Essential | Lifecycle state |

### Identity Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `name` | string | Essential | Official brand name exactly as it should appear |
| `nameVariations` | NameVariations | Supporting | Allowed and disallowed name variations |
| `tagline` | string | Essential | Memorable phrase capturing brand essence (max 10 words) |
| `positioningStatement` | string | Essential | "For [audience], [brand] is the [category] that [benefit] because [reason]" |

### Purpose Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `missionStatement` | string | Essential | What the brand does day-to-day |
| `visionStatement` | string | Supporting | Future state the brand is working toward |
| `brandPurpose` | string | Essential | The deeper "why" behind the brand's existence |
| `brandStory` | string | Supporting | Narrative of how/why the brand came to exist (150-300 words) |
| `founderBackground` | string | Truncatable | Credentials, experience, personal journey |

### Voice & Values Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `voiceId` | string | Essential | Reference to voice entry in voices library |
| `defaultToneId` | string | Essential | Default tone for this brand |
| `values` | string[] | Essential | Core brand values (3-5 recommended) |
| `valueDescriptions` | ValueDescription[] | Supporting | Detailed value definitions |

### Governance Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `truthApprovalMode` | enum | Essential | 'strict' / 'guided' / 'trusted' |
| `contentPillars` | string[] | Supporting | Core content themes |
| `avoidedTopics` | string[] | Supporting | Topics to never cover |

---

## Nested Object Schemas

### NameVariations Object

```json
{
  "allowed": [
    { "variation": "MTB", "context": "Internal communications only" },
    { "variation": "Mind the Box Coaching", "context": "Full formal name" }
  ],
  "notAllowed": [
    { "variation": "mindthebox", "reason": "No spaces reduces readability" },
    { "variation": "MtB", "reason": "Incorrect capitalization" }
  ]
}
```

### ValueDescription Object

```json
{
  "value": "Authenticity",
  "definition": "We speak truth even when uncomfortable",
  "inPractice": "We cite sources, acknowledge limitations, avoid hype",
  "violation": "Making unsupported claims, using manipulative language"
}
```

---

## Entry Status Enum

| Value | Description | AI Visibility |
|-------|-------------|---------------|
| `draft` | Incomplete, not ready | Hidden |
| `active` | Production-ready | Visible |
| `archived` | Deprecated, kept for history | Hidden |
| `flagged` | Needs human review | Hidden |

### truthApprovalMode Enum

| Value | Description | Editing Behavior |
|-------|-------------|------------------|
| `strict` | All truths locked | Cannot edit truth claims without human approval |
| `guided` | Edits require confirmation | AI prompts for verification on changes |
| `trusted` | Free editing | AI can adjust claims within brand values |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `tagline` | Quick brand summary |
| `truthApprovalMode` | Governance indicator |

**Example preview JSON:**

```json
{
  "name": "Mind the Box",
  "tagline": "Think differently about thinking",
  "truthApprovalMode": "guided"
}
```

---

## Staging Area

**Path:** `/repos/drafts/brands/`

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `voices` | `voiceId` | soft | Warning if voice archived |
| `tones` | `defaultToneId` | soft | Warning if tone archived |

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `tagline` | Yes | No | No | Full-text |
| `status` | No | Yes | No | Enum filter |
| `truthApprovalMode` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Few brands per business |
| **Entry Size** | medium | 2-5KB typical |
| **FTS Enabled** | yes | name, tagline |
| **Lazy Loading** | no | Small entries |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "brand_mind-the-box",
  "name": "Mind the Box",
  "schemaVersion": "1.0.0",
  "status": "active",
  "tagline": "Think differently about thinking",
  "positioningStatement": "For entrepreneurs stuck in old patterns, Mind the Box is the coaching practice that unlocks new perspectives because we challenge assumptions others accept.",
  "missionStatement": "To help people recognize and transcend their mental boxes.",
  "values": ["authenticity", "curiosity", "growth"],
  "truthApprovalMode": "guided"
}
```

### Complete (all fields)

```json
{
  "id": "brand_mind-the-box",
  "name": "Mind the Box",
  "schemaVersion": "1.0.0",
  "status": "active",
  "nameVariations": {
    "allowed": [
      { "variation": "MTB", "context": "Internal only" }
    ],
    "notAllowed": [
      { "variation": "mindthebox", "reason": "Reduces readability" }
    ]
  },
  "tagline": "Think differently about thinking",
  "positioningStatement": "For entrepreneurs stuck in old patterns, Mind the Box is the coaching practice that unlocks new perspectives because we challenge assumptions others accept.",
  "missionStatement": "To help people recognize and transcend their mental boxes.",
  "visionStatement": "A world where curiosity defeats fear.",
  "brandPurpose": "We exist because too many capable people stay trapped in thinking patterns that no longer serve them.",
  "brandStory": "Founded in 2020 after...",
  "founderBackground": "10 years in corporate consulting...",
  "voiceId": "voice_mtb-primary",
  "defaultToneId": "tone_warm-professional",
  "values": ["authenticity", "curiosity", "growth"],
  "valueDescriptions": [
    {
      "value": "authenticity",
      "definition": "We speak truth even when uncomfortable",
      "inPractice": "Cite sources, acknowledge limitations",
      "violation": "Unsupported claims, manipulative language"
    }
  ],
  "truthApprovalMode": "guided",
  "contentPillars": ["mindset shifts", "practical tools", "client stories"],
  "avoidedTopics": ["political commentary", "competitor criticism"]
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `brand_{slug}` pattern | `BRAND_INVALID_ID` |
| Required fields | All required fields present | `BRAND_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `BRAND_DUPLICATE_ID` |
| Values array | Must have 1-7 values | `BRAND_INVALID_VALUES_COUNT` |
| Voice exists | voiceId must reference active voice (if provided) | `BRAND_INVALID_VOICE` |
| Tone exists | defaultToneId must reference active tone (if provided) | `BRAND_INVALID_TONE` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `BrandManager` | Via staging → finalize |
| Update | `BrandManager` | Creates new version |
| Archive | `BrandManager` | Soft delete, cascades ref_status to themes |
| Delete | Never | Archive instead |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `voices` | Brand references one voice |
| `tones` | Brand has default tone |
| `audiences` | Audiences belong to brands |
| `themes` | Themes reference brands (hard dependency) |

---

## Migration Notes

**From BrandKit:**
- `01-brand-identity.data.json` → `name`, `nameVariations`, `tagline`, `positioningStatement`
- `02-brand-purpose.data.json` → `missionStatement`, `visionStatement`, `brandPurpose`, `brandStory`, `founderBackground`
- Voice/values extracted from `09-personality-traits`, `10-voice-characteristics`, `14-values`

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandKit core/identity + core/purpose schemas*
