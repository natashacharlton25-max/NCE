# Tones Library

> **System:** Brand
> **Repository:** Brand Repository
> **Path:** `/data/tones.sqlite`
> **Library Type:** core
> **Status:** Draft
> **Source:** Migrated from BrandLib `voice/tone`

---

## Overview

Reusable library of brand tones with usage contexts, characteristics, and examples. Tones modify the voice for different contexts (e.g., warm-professional for welcome emails, direct-urgent for crisis comms).

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `BrandManager` | Read/Write (Owner) | Tone management |
| `PromptBuilder` | Read via FileManager | Tone injection into prompts |
| `ThemeGenerator` | Read via FileManager | Tone for theme angles |

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

CREATE TABLE tones (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW tones_latest AS
SELECT * FROM tones t1
WHERE version = (SELECT MAX(version) FROM tones t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Tone.ts`
> Source: BrandLib `voice/tone`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "tone_warm-professional") |
| `name` | string | Essential | Display name (e.g., "Warm Professional") |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `description` | string | Essential | What this tone sounds and feels like |
| `whenToUse` | string[] | Essential | Contexts where this tone is appropriate |
| `characteristics` | string[] | Essential | Key characteristics of this tone |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `examplePhrases` | string[] | [] | Supporting | Example phrases demonstrating tone |
| `avoid` | string[] | [] | Supporting | What to avoid when using this tone |

---

## Example Tones

| ID | Name | When to Use |
|----|------|-------------|
| `tone_warm-professional` | Warm Professional | Welcome emails, client comms, support |
| `tone_direct-urgent` | Direct Urgent | Crisis comms, deadlines, important updates |
| `tone_casual-friendly` | Casual Friendly | Social media, community posts |
| `tone_authoritative` | Authoritative | Expert content, thought leadership |
| `tone_empathetic` | Empathetic | Sensitive topics, support content |
| `tone_celebratory` | Celebratory | Wins, milestones, announcements |

---

## Tone Combinations

Pre-defined combinations for specific contexts:

```json
{
  "toneCombinations": [
    {
      "name": "Supportive Guide",
      "primary": "tone_warm-professional",
      "secondary": "tone_empathetic",
      "useFor": "Onboarding sequences, coaching content",
      "example": "I hear you - starting something new feels overwhelming. Let's break this down together."
    },
    {
      "name": "Friendly Expert",
      "primary": "tone_authoritative",
      "secondary": "tone_casual-friendly",
      "useFor": "Educational content, how-to guides",
      "example": "Here's the thing most people miss about productivity..."
    }
  ]
}
```

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `whenToUse[0]` | Primary use context |

**Example preview JSON:**

```json
{
  "name": "Warm Professional",
  "primaryContext": "Client communications"
}
```

---

## Staging Area

**Path:** `/repos/drafts/tones/`

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `status` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Curated tone library |
| **Entry Size** | small | < 1KB typical |
| **FTS Enabled** | no | ID lookup sufficient |
| **Lazy Loading** | no | Small entries |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "tone_warm-professional",
  "name": "Warm Professional",
  "schemaVersion": "1.0.0",
  "status": "active",
  "description": "Friendly yet competent. Approachable but not casual. Shows care while maintaining credibility.",
  "whenToUse": [
    "Client communications",
    "Welcome emails",
    "Support responses"
  ],
  "characteristics": [
    "Uses 'you' and 'we' frequently",
    "Acknowledges feelings before solutions",
    "Balances warmth with professionalism"
  ]
}
```

### Complete (all fields)

```json
{
  "id": "tone_warm-professional",
  "name": "Warm Professional",
  "schemaVersion": "1.0.0",
  "status": "active",
  "description": "Friendly yet competent. Approachable but not casual. Shows care while maintaining credibility.",
  "whenToUse": [
    "Client communications",
    "Welcome emails",
    "Support responses",
    "Onboarding sequences"
  ],
  "characteristics": [
    "Uses 'you' and 'we' frequently",
    "Acknowledges feelings before solutions",
    "Balances warmth with professionalism",
    "Conversational but not slangy"
  ],
  "examplePhrases": [
    "I'm glad you reached out.",
    "Let's work through this together.",
    "That's a great question - here's what I'd suggest.",
    "I understand this feels overwhelming right now."
  ],
  "avoid": [
    "Overly formal language (pursuant to, as per)",
    "Dismissive phrases (just, simply, obviously)",
    "Corporate jargon",
    "Being so warm it feels unprofessional"
  ]
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `tone_{slug}` pattern | `TONE_INVALID_ID` |
| Required fields | All required fields present | `TONE_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `TONE_DUPLICATE_ID` |
| Characteristics count | Must have 2-10 characteristics | `TONE_INVALID_CHARACTERISTICS` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `BrandManager` | Via staging → finalize |
| Update | `BrandManager` | Creates new version |
| Archive | `BrandManager` | Soft delete |
| Delete | Never | Archive instead |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `brands` | Brands reference default tone |
| `voices` | Tones modify voice output |

---

## Tone Combinations (Dev Team Gap - 2026-01-20)

> Some contexts benefit from blending tones. This needs formalization.

### Current Gap
- `tone_combinations` mentioned in Migration Notes but not fully specified
- No schema for valid combinations
- No guidance on which tones can combine

### Proposed Solution
Add `compatibleTones` field to tone schema:

```json
{
  "id": "tone_warm-professional",
  "compatibleTones": ["tone_encouraging", "tone_direct"],
  "incompatibleTones": ["tone_casual", "tone_urgent"]
}
```

### Alternative: Separate Combinations Library
Create `tone-combinations.library.md` with:
- Combination ID (e.g., "combo_warm-encouraging")
- Primary and secondary tone references
- Contexts where combination applies
- Blending rules (50/50, 70/30, etc.)

### Integration Points
- BrandManager validates tone selection
- PromptBuilder applies combination rules
- TemplateEngine respects context-specific tone variants

---

## Migration Notes

**From BrandLib:**
- `voice/tone/default.json` → tones array
- `tone_combinations` stored as separate reference data

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandLib voice/tone schema*
