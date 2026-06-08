# Outcomes Library

> **System:** Content
> **Repository:** Content Repository
> **Path:** `/data/outcomes.sqlite`
> **Library Type:** core
> **Status:** Draft
> **Source:** Migrated from BrandLib `outcomes/client-outcomes`

---

## Overview

Transformation outcomes that content aims to produce in audiences. Includes core outcome definitions, belief shifts, desired outcomes, and brand perspective on each outcome. Used by ThemeGenerator for `targetOutcomes[]` field to drive transformation-focused content.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `ThemeGenerator` | Read via FileManager | targetOutcomes, transformation context, angle generation |
| `OutlineGenerator` | Read via FileManager | Outcome alignment for content structure |

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

CREATE VIRTUAL TABLE outcomes_fts USING fts5(
  id,
  name,
  theme,
  content='outcomes',
  content_rowid='rowid'
);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Outcome.ts`
> Source: BrandLib `outcomes/client-outcomes` + ThemeGenerator spec alignment

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "outcome_self-worth") |
| `name` | string | Essential | Outcome name (e.g., "Self-Worth") |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `theme` | string | Essential | Theme category (e.g., "Worth & Value") |
| `definition` | string | Essential | Clear definition of this outcome |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `indicators` | string[] | [] | Supporting | Observable indicators of achievement |
| `everydayExamples` | string[] | [] | Supporting | Relatable everyday examples |
| `beliefShifts` | BeliefShift[] | [] | Essential | Limiting beliefs and their shifts (for ThemeGenerator) |
| `desiredOutcomes` | DesiredOutcome | null | Essential | What clients gain/experience (for ThemeGenerator) |
| `brandPerspective` | BrandPerspective | null | Essential | Brand's unique take on this outcome (enables Tier 1 generation) |

---

## Nested Object Schemas

### BeliefShift Object

Used by ThemeGenerator to generate truths that facilitate belief transformation.

```json
{
  "from": "I'm only valuable when I'm productive",
  "to": "My worth is inherent—productivity is what I do, not who I am"
}
```

### DesiredOutcome Object

Used by ThemeGenerator for `audienceTransformation` in theme output.

```json
{
  "theyGain": [
    "Recognition of inherent worth",
    "Tools to challenge productivity-based self-evaluation"
  ],
  "theyExperience": [
    "Moments of self-acceptance without achievement",
    "Reduced anxiety about 'doing enough'"
  ],
  "theyWalkAwayWith": [
    "Language for self-worth (I am vs I do)",
    "Permission to rest without guilt"
  ]
}
```

### BrandPerspective Object

**Critical for Tier 1 theme generation.** Contains the brand's unique voice and angle on this outcome. Without this, ThemeGenerator falls back to Tier 2/3 (generic) generation.

```json
{
  "coreBelief": "Rest is infrastructure, not indulgence. Your worth isn't earned through exhaustion.",
  "howITalkAboutThis": "I approach self-worth through the lens of anti-hustle culture. Worth isn't something you achieve—it's something you already have that gets obscured by productivity metrics.",
  "whatImRejecting": [
    "Hustle culture narratives",
    "Worth tied to output",
    "Rest as reward for productivity",
    "Self-improvement as fixing brokenness"
  ],
  "signaturePhrases": [
    "You are not your productivity",
    "Rest as radical resistance",
    "Worth beyond metrics",
    "Already enough, always"
  ],
  "contentAngles": [
    "Rest as radical resistance to hustle culture",
    "Worth beyond productivity metrics",
    "The myth of earning your value",
    "Separating identity from achievement"
  ]
}
```

---

## ThemeGenerator Integration

### Generation Quality Tiers

The presence of `brandPerspective` determines theme generation quality:

| Tier | Data Available | Result |
|------|----------------|--------|
| **Tier 1: Full** | outcome + brandPerspective + beliefShifts + desiredOutcomes | Authentic voice, strategic alignment |
| **Tier 2: Good** | outcome + beliefShifts + desiredOutcomes (no perspective) | Strategic alignment, generic voice |
| **Tier 3: Basic** | outcome only | Topic-focused, minimal transformation |
| **Tier 4: Legacy** | No targetOutcomes | Traditional generation |

### How ThemeGenerator Uses Outcomes

```
1. Input: targetOutcomes: ["outcome_self-worth"]
2. Load: outcome definition, beliefShifts, desiredOutcomes, brandPerspective
3. Generate angles from brandPerspective.contentAngles (or auto-generate if missing)
4. Generate truths that facilitate beliefShifts
5. Output theme with transformationFramework populated
```

---

## Theme Categories

| Theme | Description |
|-------|-------------|
| Agency & Autonomy | Taking ownership, self-direction |
| Worth & Value | Self-worth, inherent value, enough-ness |
| Emotional Intelligence | Understanding and managing emotions |
| Relationships | Connection, boundaries, communication |
| Growth Mindset | Learning, resilience, adaptation |
| Purpose & Meaning | Direction, fulfillment, values |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `theme` | Category grouping |
| `hasPerspective` | Quick indicator for Tier 1 availability |

**Example preview JSON:**

```json
{
  "name": "Self-Worth",
  "theme": "Worth & Value",
  "hasPerspective": true
}
```

---

## Staging Area

**Path:** `/repos/drafts/outcomes/`

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `theme` | Yes | Yes | Yes | Category filter |
| `status` | No | Yes | No | Enum filter |
| `hasPerspective` | No | Yes | No | Boolean filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Curated outcome library |
| **Entry Size** | medium | 2-5KB with perspective |
| **FTS Enabled** | yes | name, theme |
| **Lazy Loading** | yes | Load perspective on demand |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "outcome_self-worth",
  "name": "Self-Worth",
  "schemaVersion": "1.0.0",
  "status": "active",
  "theme": "Worth & Value",
  "definition": "Recognising inherent value independent of achievements, productivity, or external validation"
}
```

### Complete (all fields - Tier 1 ready)

```json
{
  "id": "outcome_self-worth",
  "name": "Self-Worth",
  "schemaVersion": "1.0.0",
  "status": "active",
  "theme": "Worth & Value",
  "definition": "Recognising inherent value independent of achievements, productivity, or external validation",
  "indicators": [
    "Speaks about self without qualifying achievements",
    "Sets boundaries without excessive justification",
    "Rests without guilt or 'earning' it first",
    "Accepts compliments without deflection"
  ],
  "everydayExamples": [
    "Taking a day off without being sick",
    "Saying 'thank you' instead of deflecting praise",
    "Choosing rest over 'catching up' on weekends"
  ],
  "beliefShifts": [
    {
      "from": "I'm only valuable when I'm productive",
      "to": "My worth is inherent—productivity is what I do, not who I am"
    },
    {
      "from": "I need to earn rest",
      "to": "Rest is a right, not a reward"
    },
    {
      "from": "If I'm not improving, I'm failing",
      "to": "I can be whole and still growing"
    }
  ],
  "desiredOutcomes": {
    "theyGain": [
      "Recognition of inherent worth",
      "Tools to challenge productivity-based self-evaluation",
      "Language for self-compassion"
    ],
    "theyExperience": [
      "Moments of self-acceptance without achievement",
      "Reduced anxiety about 'doing enough'",
      "Freedom from comparison"
    ],
    "theyWalkAwayWith": [
      "Language shifts (I am vs I do)",
      "Permission to rest without guilt",
      "Framework for evaluating self-worth sources"
    ]
  },
  "brandPerspective": {
    "coreBelief": "Rest is infrastructure, not indulgence. Your worth isn't earned through exhaustion.",
    "howITalkAboutThis": "I approach self-worth through the lens of anti-hustle culture. Worth isn't something you achieve—it's something you already have that gets obscured by productivity metrics.",
    "whatImRejecting": [
      "Hustle culture narratives",
      "Worth tied to output",
      "Rest as reward for productivity",
      "Self-improvement as fixing brokenness"
    ],
    "signaturePhrases": [
      "You are not your productivity",
      "Rest as radical resistance",
      "Worth beyond metrics",
      "Already enough, always"
    ],
    "contentAngles": [
      "Rest as radical resistance to hustle culture",
      "Worth beyond productivity metrics",
      "The myth of earning your value",
      "Separating identity from achievement"
    ]
  }
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `outcome_{slug}` pattern | `OUTCOME_INVALID_ID` |
| Required fields | All required fields present | `OUTCOME_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `OUTCOME_DUPLICATE_ID` |
| Theme valid | Theme must be from allowed list | `OUTCOME_INVALID_THEME` |
| BeliefShifts format | Each must have `from` and `to` strings | `OUTCOME_INVALID_BELIEF` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | Curated content |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `themes` | Themes target multiple outcomes via `targetOutcomes[]` |
| `brands` | Brand perspective comes from brand's voice/values |

---

## Rollout Strategy

Since `brandPerspective` enables Tier 1 generation but isn't required:

1. **Start:** Add 5 most-used outcomes with full `brandPerspective`
2. **Test:** Verify ThemeGenerator produces higher quality output
3. **Expand:** Gradually add perspective to remaining outcomes
4. **Fallback:** System works at Tier 2/3/4 for outcomes without perspective

---

## Migration Notes

**From BrandLib:**
- `outcomes/client-outcomes/default.json` → outcomes array
- `beliefs` → `beliefShifts` (camelCase)
- `desired_outcomes` → `desiredOutcomes` (camelCase)
- `natasha_perspective` → `brandPerspective` (generalized + camelCase)

**Field Naming Convention:** All JSON fields use camelCase. SQL columns use snake_case.

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandLib outcomes/client-outcomes + ThemeGenerator spec alignment*
