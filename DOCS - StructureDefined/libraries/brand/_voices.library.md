# Voices Library

> **System:** Brand
> **Repository:** Brand Repository
> **Path:** `/data/voices.sqlite`
> **Library Type:** core
> **Status:** Draft
> **Source:** Migrated from BrandLib `voice/vocabulary` + BrandKit `voice-characteristics`

---

## Overview

Brand voice definitions including vocabulary preferences, writing style characteristics, and language guidance. Voice is the consistent personality; tone modifies it for context.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `BrandManager` | Read/Write (Owner) | Voice management |
| `PromptBuilder` | Read via FileManager | Voice injection into prompts |
| `ThemeGenerator` | Read via FileManager | Voice alignment for themes |

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

CREATE TABLE voices (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW voices_latest AS
SELECT * FROM voices t1
WHERE version = (SELECT MAX(version) FROM voices t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Voice.ts`
> Source: BrandKit voice-characteristics + BrandLib vocabulary

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "voice_mtb-primary") |
| `name` | string | Essential | Display name (e.g., "Mind the Box Primary Voice") |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `personality` | string[] | Essential | Personality traits (3-5) |
| `writingStyle` | WritingStyle | Essential | How the brand writes |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `vocabulary` | Vocabulary | {} | Essential | Preferred and avoided terms |
| `sentencePatterns` | string[] | [] | Supporting | Characteristic sentence structures |
| `signaturePhrases` | string[] | [] | Supporting | Phrases unique to this brand |
| `domain` | string | 'universal' | Supporting | Domain specialization |

---

## Nested Object Schemas

### WritingStyle Object

```json
{
  "sentenceLength": "varied, trending short",
  "paragraphLength": "2-4 sentences",
  "punctuation": {
    "ellipses": "sparingly for pause effect",
    "exclamation": "rare, for genuine excitement only",
    "questions": "frequently, to engage reader"
  },
  "contractions": "yes, for warmth",
  "firstPerson": "we (brand), I (founder content)",
  "secondPerson": "yes, frequently address reader directly"
}
```

### Vocabulary Object

```json
{
  "preferredTerms": [
    {
      "preferred": "challenge",
      "insteadOf": ["problem", "issue"],
      "why": "Implies growth opportunity",
      "context": "When discussing difficulties"
    }
  ],
  "avoidedTerms": [
    {
      "term": "just",
      "whyProblematic": "Minimizes effort or importance",
      "useInstead": "omit entirely",
      "exception": "When truly minimal"
    }
  ],
  "clinicalTerms": [
    {
      "clinical": "cognitive distortion",
      "plainLanguage": "unhelpful thinking pattern",
      "whenToUseClinical": "Professional audience, educational context"
    }
  ]
}
```

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `personality[0-2]` | Quick traits |

**Example preview JSON:**

```json
{
  "name": "Mind the Box Primary",
  "traits": ["warm", "direct", "thoughtful"]
}
```

---

## Staging Area

**Path:** `/repos/drafts/voices/`

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `status` | No | Yes | No | Enum filter |
| `domain` | No | Yes | No | Filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Few voices per brand |
| **Entry Size** | medium | 2-5KB typical |
| **FTS Enabled** | no | ID lookup sufficient |
| **Lazy Loading** | yes | Load vocabulary on demand |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "voice_mtb-primary",
  "name": "Mind the Box Primary Voice",
  "schemaVersion": "1.0.0",
  "status": "active",
  "personality": ["warm", "direct", "thoughtful", "occasionally playful"],
  "writingStyle": {
    "sentenceLength": "varied, trending short",
    "contractions": "yes",
    "secondPerson": "yes"
  }
}
```

### Complete (all fields)

```json
{
  "id": "voice_mtb-primary",
  "name": "Mind the Box Primary Voice",
  "schemaVersion": "1.0.0",
  "status": "active",
  "personality": [
    "warm",
    "direct",
    "thoughtful",
    "occasionally playful"
  ],
  "writingStyle": {
    "sentenceLength": "varied, trending short",
    "paragraphLength": "2-4 sentences",
    "punctuation": {
      "ellipses": "sparingly",
      "exclamation": "rare",
      "questions": "frequent"
    },
    "contractions": "yes",
    "firstPerson": "we",
    "secondPerson": "yes"
  },
  "vocabulary": {
    "preferredTerms": [
      {
        "preferred": "pattern",
        "insteadOf": ["habit", "behavior"],
        "why": "More neutral, less judgmental",
        "context": "Discussing recurring actions"
      },
      {
        "preferred": "notice",
        "insteadOf": ["realize", "see"],
        "why": "Implies gentle awareness",
        "context": "Self-reflection prompts"
      }
    ],
    "avoidedTerms": [
      {
        "term": "should",
        "whyProblematic": "Creates shame and obligation",
        "useInstead": "could, might consider",
        "exception": "None"
      },
      {
        "term": "toxic",
        "whyProblematic": "Overused, creates drama",
        "useInstead": "unhealthy, harmful",
        "exception": "When describing genuinely dangerous situations"
      }
    ],
    "clinicalTerms": [
      {
        "clinical": "metacognition",
        "plainLanguage": "thinking about your thinking",
        "whenToUseClinical": "Professional development contexts"
      }
    ]
  },
  "sentencePatterns": [
    "What if [assumption] isn't true?",
    "Here's the thing about [topic]...",
    "Notice how [observation]."
  ],
  "signaturePhrases": [
    "mind the box",
    "thinking about thinking",
    "what's the box here?"
  ],
  "domain": "coaching"
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `voice_{slug}` pattern | `VOICE_INVALID_ID` |
| Required fields | All required fields present | `VOICE_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `VOICE_DUPLICATE_ID` |
| Personality count | Must have 3-7 traits | `VOICE_INVALID_PERSONALITY` |

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
| `brands` | Brands reference one voice |
| `tones` | Tones modify voice output |

---

## Migration Notes

**From BrandKit:**
- `10-voice-characteristics.data.json` → personality, writingStyle
- `09-personality-traits.data.json` → personality traits

**From BrandLib:**
- `voice/vocabulary/default.json` → vocabulary object

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandKit voice-characteristics + BrandLib vocabulary*
