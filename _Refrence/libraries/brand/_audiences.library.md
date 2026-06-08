# Audiences Library

> **System:** Brand
> **Repository:** Brand Repository
> **Path:** `/data/audiences.sqlite`
> **Library Type:** core
> **Status:** Draft
> **Source:** Migrated from BrandKit `audience/core`

---

## Overview

Target audience profiles for content personalization and theme targeting. Rich profiles including demographics, psychographics, language patterns, objections, transformation journeys, and accessibility considerations.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `AudienceManager` | Read/Write (Owner) | CRUD operations on audiences |
| `ThemeGenerator` | Read via FileManager | Audience context for theme generation |
| `TemplateEngine` | Read via FileManager | Audience context for content generation |
| `PromptBuilder` | Read via FileManager | Language patterns for prompts |

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

CREATE VIRTUAL TABLE audiences_fts USING fts5(
  id,
  name,
  segment_name,
  content='audiences',
  content_rowid='rowid'
);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Audience.ts`
> Source: BrandKit `05-audience-core`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "audience_entrepreneurs") |
| `segmentName` | string | Essential | Clear internal label for this segment |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |

### Demographics Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `demographics.ageRange` | string | Supporting | Age range (e.g., "25-45") |
| `demographics.genderDistribution` | string | Supporting | Gender breakdown |
| `demographics.location` | string | Supporting | Geographic location |
| `demographics.incomeLevel` | string | Supporting | Income bracket |
| `demographics.education` | string | Supporting | Education level |
| `demographics.employment` | string | Supporting | Employment status/type |
| `demographics.livingSituation` | string | Supporting | Living arrangements |

### Psychographics Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `psychographics.beliefs` | string[] | Essential | Core beliefs they hold |
| `psychographics.fears` | string[] | Essential | What they're afraid of |
| `psychographics.desires` | string[] | Essential | What they want |
| `psychographics.frustrations` | string[] | Essential | What frustrates them |

### Awareness Level Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `awarenessLevel.primaryLevel` | string | Essential | Main awareness stage |
| `awarenessLevel.secondaryLevel` | string | Supporting | Secondary awareness |
| `awarenessLevel.contentImplications` | string[] | Supporting | How to write for this level |

### Language Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `language.theySay` | string[] | Essential | Phrases they use |
| `language.theyDontSay` | string[] | Essential | Phrases they avoid |
| `language.avoidUsing` | string[] | Essential | Words to avoid with them |

### Objections Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `objections.practical` | Objection[] | Essential | Practical concerns (time, money) |
| `objections.emotional` | Objection[] | Essential | Emotional barriers (fear, shame) |
| `objections.brandSpecific` | Objection[] | Supporting | Objections specific to this brand |

### Transformation Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `transformation.from` | string[] | Essential | Current state descriptions |
| `transformation.to` | string[] | Essential | Desired state descriptions |
| `transformation.statement` | string | Essential | Concise transformation summary |

### Additional Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `currentSolutions` | Solution[] | Supporting | Where they currently seek help |
| `accessibility` | Accessibility | Supporting | Accessibility considerations |
| `formatPreferences` | FormatPrefs | Supporting | Content format preferences |
| `platforms` | Platforms | Supporting | Where they spend time online |
| `sensitivities` | Sensitivities | Essential | Topics/approaches to handle carefully |
| `journeyStages` | JourneyStage[] | Supporting | Customer journey stages |
| `engagementTriggers` | Triggers | Supporting | What triggers engagement |

---

## Nested Object Schemas

### Objection Object

```json
{
  "objection": "I don't have time for this",
  "response": "Our approach takes just 15 minutes daily..."
}
```

### Solution Object

```json
{
  "source": "Self-help books",
  "pros": "Affordable, private",
  "cons": "Generic, no accountability"
}
```

### Accessibility Object

```json
{
  "cognitive": ["May have difficulty with long-form content", "Benefits from clear structure"],
  "physical": ["Screen reader users need alt text"],
  "situational": ["Often reading on mobile", "May be in distress"],
  "standardsCommitment": "WCAG 2.1 AA"
}
```

### Sensitivities Object

```json
{
  "contentWarningsRequired": ["trauma", "self-harm", "abuse"],
  "contentWarningFormat": "CW at start, expandable content",
  "languageToAvoid": [
    { "avoid": "crazy", "useInstead": "unexpected" },
    { "avoid": "committed suicide", "useInstead": "died by suicide" }
  ],
  "imageryToAvoid": ["distressed people", "before/after comparisons"],
  "approachSensitivities": ["Avoid toxic positivity", "Don't minimize struggles"]
}
```

### JourneyStage Object

```json
{
  "stage": "Awareness",
  "description": "Just realizing they have a problem",
  "contentNeeds": ["Validation", "Problem naming", "Hope"],
  "triggers": ["Life event", "Recommendation", "Pain threshold"]
}
```

### Triggers Object

```json
{
  "positive": ["Success stories", "Practical tips", "Empathy"],
  "negative": ["Lectures", "Shame", "Overwhelm"],
  "timing": ["Monday mornings", "End of year", "After setbacks"]
}
```

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `segmentName` | Display name in lists |
| `transformation.statement` | Quick summary |

**Example preview JSON:**

```json
{
  "segmentName": "First-time Founders",
  "transformation": "From overwhelmed and uncertain to confident and strategic"
}
```

---

## Staging Area

**Path:** `/repos/drafts/audiences/`

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `brands` | (implicit via brandId) | soft | Audience can exist without brand |

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `segmentName` | Yes | Yes | Yes | Full-text |
| `status` | No | Yes | No | Enum filter |
| `awarenessLevel.primaryLevel` | No | Yes | No | Filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Few audiences per brand |
| **Entry Size** | medium-large | 3-10KB typical |
| **FTS Enabled** | yes | segmentName, language.theySay |
| **Lazy Loading** | yes | Load full profile on demand |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "audience_entrepreneurs",
  "segmentName": "First-time Founders",
  "schemaVersion": "1.0.0",
  "status": "active"
}
```

### Complete (key fields)

```json
{
  "id": "audience_entrepreneurs",
  "segmentName": "First-time Founders",
  "schemaVersion": "1.0.0",
  "status": "active",
  "demographics": {
    "ageRange": "28-45",
    "location": "UK, US, Australia",
    "employment": "Self-employed or transitioning"
  },
  "psychographics": {
    "beliefs": ["Hard work leads to success", "I should be further along by now"],
    "fears": ["Failure", "Being found out", "Running out of money"],
    "desires": ["Freedom", "Impact", "Recognition"],
    "frustrations": ["Too much conflicting advice", "Isolation", "Constant pivoting"]
  },
  "awarenessLevel": {
    "primaryLevel": "problem-aware",
    "contentImplications": ["Name the problem clearly", "Show you understand"]
  },
  "language": {
    "theySay": ["I'm wearing too many hats", "I don't know what I don't know"],
    "theyDontSay": ["I need business coaching"],
    "avoidUsing": ["Guru", "Hustle", "Grind"]
  },
  "objections": {
    "practical": [
      { "objection": "I can't afford coaching right now", "response": "What's the cost of staying stuck for another year?" }
    ],
    "emotional": [
      { "objection": "I should be able to figure this out myself", "response": "The best founders have coaches and mentors" }
    ]
  },
  "transformation": {
    "from": ["Overwhelmed", "Reactive", "Second-guessing every decision"],
    "to": ["Focused", "Strategic", "Confident in my direction"],
    "statement": "From overwhelmed and uncertain to confident and strategic"
  },
  "sensitivities": {
    "contentWarningsRequired": ["burnout", "financial stress"],
    "languageToAvoid": [
      { "avoid": "failed business", "useInstead": "business that didn't work out" }
    ],
    "approachSensitivities": ["Avoid implying they're lazy", "Acknowledge systemic barriers"]
  }
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `audience_{slug}` pattern | `AUDIENCE_INVALID_ID` |
| Required fields | All required fields present | `AUDIENCE_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `AUDIENCE_DUPLICATE_ID` |
| Schema version | Must be valid semver | `AUDIENCE_INVALID_SCHEMA_VERSION` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `AudienceManager` | Via staging → finalize |
| Update | `AudienceManager` | Creates new version |
| Archive | `AudienceManager` | Soft delete, cascades to themes |
| Delete | Never | Archive instead |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `brands` | Audiences may belong to brands |
| `themes` | Themes target one audience (hard dependency) |

---

## Migration Notes

**From BrandKit:**
- `05-audience-core.data.json` → Full audience profile
- Multiple audience types (core, professional, supporter) map to separate entries with type tag

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandKit audience/core schema*
