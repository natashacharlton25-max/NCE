# Cognitive Types Library

> **System:** Template
> **Repository:** Template Repository
> **Path:** `/data/cognitive-types.sqlite`
> **Library Type:** reference
> **Status:** Draft
> **Source:** Migrated from BrandLib `content/story-frameworks`

---

## Overview

Cognitive framework definitions for content structure and reasoning patterns. Defines narrative structures and how ideas flow and connect. Each framework provides a proven structure for organizing content effectively.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `OutlineGenerator` | Read via FileManager | Cognitive framework for outline reasoning |
| `ThemeGenerator` | Read via FileManager | Framework suggestions for themes |

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

CREATE TABLE cognitive_types (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW cognitive_types_latest AS
SELECT * FROM cognitive_types t1
WHERE version = (SELECT MAX(version) FROM cognitive_types t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/CognitiveType.ts`
> Source: BrandLib story-frameworks

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "cognitive_problem-solution") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `description` | string | Essential | What this framework does and when to use it |
| `stages` | Stage[] | Essential | Ordered stages of the framework |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `bestFor` | string[] | [] | Essential | Content types this works well with |
| `length` | enum | 'medium' | Supporting | 'short' / 'medium' / 'long' |
| `variations` | Variation[] | [] | Supporting | Alternative versions |
| `flow` | string | 'linear' | Supporting | 'linear' / 'cyclical' / 'branching' |

### Stage Object

```json
{
  "stageNumber": 1,
  "stageName": "The Before",
  "purpose": "Establishes the world before change, creating relatability",
  "prompts": [
    "What was life or work like before this journey began?",
    "What was missing or not working?",
    "What assumptions or norms were you operating under?"
  ],
  "example": "I spent eight years in corporate marketing, helping brands shout louder without saying anything meaningful."
}
```

### Variation Object

```json
{
  "name": "Quick Version",
  "format": "social_media",
  "structure": "Condensed to 3 stages for shorter content",
  "example": "Hook → Shift → Invitation"
}
```

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `description` | Quick summary |
| `length` | Complexity indicator |

**Example preview JSON:**

```json
{
  "name": "Problem-Agitate-Solution",
  "description": "Classic persuasive structure",
  "length": "short"
}
```

---

## Staging Area

**Path:** `/repos/drafts/cognitive-types/`

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `length` | No | Yes | No | Filter |
| `status` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 50 | Finite cognitive frameworks |
| **Entry Size** | medium | 2-5KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | yes | Load stages on demand |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "cognitive_problem-solution",
  "name": "Problem-Agitate-Solution",
  "schemaVersion": "1.0.0",
  "status": "active",
  "description": "Classic persuasive structure that names the problem, intensifies awareness of its impact, then presents the solution",
  "stages": [
    {
      "stageNumber": 1,
      "stageName": "Problem",
      "purpose": "Names the specific problem your audience faces",
      "prompts": ["What is the core problem your audience struggles with?"]
    },
    {
      "stageNumber": 2,
      "stageName": "Agitate",
      "purpose": "Deepens awareness of the problem's real impact",
      "prompts": ["What happens if they don't solve this problem?"]
    },
    {
      "stageNumber": 3,
      "stageName": "Solution",
      "purpose": "Presents your offering as the answer",
      "prompts": ["How does your solution specifically address this problem?"]
    }
  ]
}
```

### Complete (all fields)

```json
{
  "id": "cognitive_origin-story",
  "name": "The Origin Story",
  "schemaVersion": "1.0.0",
  "status": "active",
  "description": "Tells the founding story of a brand, person, or offering. Creates emotional connection through shared human experience and establishes authenticity.",
  "length": "medium",
  "flow": "linear",
  "stages": [
    {
      "stageNumber": 1,
      "stageName": "The Before",
      "purpose": "Establishes the world before change, creating relatability",
      "prompts": [
        "What was life or work like before this journey began?",
        "What was missing or not working?",
        "What assumptions or norms were you operating under?",
        "What did your daily reality look like?"
      ],
      "example": "I spent eight years in corporate marketing, helping brands shout louder without saying anything meaningful. My calendar was full, my clients were paying, and I was slowly dying inside."
    },
    {
      "stageNumber": 2,
      "stageName": "The Catalyst",
      "purpose": "Introduces the moment of realisation or disruption that changed everything",
      "prompts": [
        "What specific moment or event triggered change?",
        "What did you suddenly see or understand?",
        "What became impossible to ignore?",
        "Who or what challenged your existing path?"
      ],
      "example": "Then came the meeting where a client asked me to make their product sound 'more authentic' while we both knew the claims were exaggerated. Something snapped."
    },
    {
      "stageNumber": 3,
      "stageName": "The Struggle",
      "purpose": "Shows the difficulty of change, building credibility through honesty",
      "prompts": [
        "What was hard about making the change?",
        "What did you have to give up or risk?",
        "What doubts or fears came up?",
        "What obstacles did you face?"
      ],
      "example": "Walking away from a six-figure salary with no clear plan felt terrifying. Friends thought I was having a breakdown."
    },
    {
      "stageNumber": 4,
      "stageName": "The Discovery",
      "purpose": "Reveals the insight or approach that made the difference",
      "prompts": [
        "What did you figure out or create?",
        "What approach or method emerged?",
        "What unexpected truth did you uncover?",
        "What worked when you finally found it?"
      ],
      "example": "I discovered that the best brand work starts with listening, not advising. When I stopped trying to make people sound impressive and started helping them sound like themselves, everything changed."
    },
    {
      "stageNumber": 5,
      "stageName": "The New Reality",
      "purpose": "Shows the transformation and invites others into it",
      "prompts": [
        "What does life or work look like now?",
        "What became possible that wasn't before?",
        "What do you now offer or provide?",
        "How does this story connect to the audience's journey?"
      ],
      "example": "Now I help purpose-led businesses find words that feel true. Every client tells me the same thing: 'This finally sounds like me.'"
    }
  ],
  "bestFor": ["about pages", "founder stories", "brand history", "keynote openings", "podcast introductions"],
  "variations": [
    {
      "name": "Quick Social Version",
      "format": "social_media",
      "structure": "Before → Catalyst → Now (3 stages)",
      "example": "Used to [BEFORE]. Then [CATALYST]. Now [NEW REALITY]."
    }
  ]
}
```

### Additional Cognitive Types

```json
{
  "id": "cognitive_heros-journey",
  "name": "The Hero's Journey (Adapted)",
  "description": "Classic narrative structure adapted for business storytelling. Positions the customer as hero and the brand as guide.",
  "length": "long",
  "stages": [
    {
      "stageNumber": 1,
      "stageName": "The Ordinary World",
      "purpose": "Establishes the customer's current reality and struggles"
    },
    {
      "stageNumber": 2,
      "stageName": "The Call to Change",
      "purpose": "Introduces the possibility of something different"
    },
    {
      "stageNumber": 3,
      "stageName": "Meeting the Guide",
      "purpose": "Positions the brand as the trusted mentor with expertise and empathy"
    },
    {
      "stageNumber": 4,
      "stageName": "The Plan",
      "purpose": "Provides a clear, simple path forward"
    },
    {
      "stageNumber": 5,
      "stageName": "The Transformation",
      "purpose": "Paints the picture of success and invites action"
    }
  ],
  "bestFor": ["sales pages", "service descriptions", "marketing campaigns", "launch content"]
}
```

```json
{
  "id": "cognitive_before-after-bridge",
  "name": "Before-After-Bridge",
  "description": "Shows transformation by contrasting current and future states, then providing the bridge. Simple and effective for quick impact.",
  "length": "short",
  "stages": [
    {
      "stageNumber": 1,
      "stageName": "Before",
      "purpose": "Paints a vivid picture of current struggle",
      "prompts": ["What does a typical frustrating day look like for your audience?"]
    },
    {
      "stageNumber": 2,
      "stageName": "After",
      "purpose": "Shows the desired future state in vivid detail",
      "prompts": ["What does a great day look like after the transformation?"]
    },
    {
      "stageNumber": 3,
      "stageName": "Bridge",
      "purpose": "Presents your solution as the path between states",
      "prompts": ["What specifically gets them from before to after?"]
    }
  ],
  "bestFor": ["email marketing", "social media content", "course sales pages", "product descriptions"]
}
```

```json
{
  "id": "cognitive_contrarian-take",
  "name": "The Contrarian Take",
  "description": "Challenges conventional wisdom and presents an alternative perspective. Creates thought leadership through differentiated thinking.",
  "length": "medium",
  "stages": [
    {
      "stageNumber": 1,
      "stageName": "The Common Belief",
      "purpose": "Articulates what most people think or do"
    },
    {
      "stageNumber": 2,
      "stageName": "The Challenge",
      "purpose": "Raises doubt about the conventional wisdom"
    },
    {
      "stageNumber": 3,
      "stageName": "The Alternative",
      "purpose": "Presents your different perspective"
    },
    {
      "stageNumber": 4,
      "stageName": "The Proof",
      "purpose": "Provides evidence for your alternative view"
    }
  ],
  "bestFor": ["thought leadership", "LinkedIn posts", "keynote talks", "podcast pitches"]
}
```

```json
{
  "id": "cognitive_lesson-learned",
  "name": "The Lesson Learned",
  "description": "Shares a mistake or failure and the wisdom gained. Builds connection through vulnerability and provides genuine value.",
  "length": "medium",
  "stages": [
    {
      "stageNumber": 1,
      "stageName": "The Mistake",
      "purpose": "Honestly shares what went wrong"
    },
    {
      "stageNumber": 2,
      "stageName": "The Impact",
      "purpose": "Shows the real consequences without dramatising"
    },
    {
      "stageNumber": 3,
      "stageName": "The Lesson",
      "purpose": "Extracts the actionable insight"
    },
    {
      "stageNumber": 4,
      "stageName": "The Application",
      "purpose": "Helps the audience apply this wisdom"
    }
  ],
  "bestFor": ["blog posts", "newsletter content", "social media", "podcast episodes"]
}
```

---

## Framework Selection Guide

### By Goal

| Goal | Recommended Frameworks |
|------|------------------------|
| Establish credibility | origin-story, lesson-learned, credibility-story |
| Build emotional connection | lesson-learned, values-story, day-in-life |
| Drive sales | heros-journey, problem-solution, before-after-bridge |
| Differentiate from competitors | contrarian-take, myth-busting, pivot-story |
| Create community | future-vision, community-story, values-story |
| Educate and add value | lesson-learned, unexpected-teacher, expertise-showcase |

### By Platform

| Platform | Best Frameworks |
|----------|-----------------|
| LinkedIn | problem-solution, lesson-learned, contrarian-take |
| Instagram | before-after-bridge, unexpected-teacher |
| Sales Page | heros-journey, problem-solution, client-journey |
| About Page | origin-story, values-story, pivot-story |
| Newsletter | lesson-learned, unexpected-teacher, expertise-showcase |

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `cognitive_{slug}` pattern | `COGNITIVE_INVALID_ID` |
| Required fields | All required fields present | `COGNITIVE_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `COGNITIVE_DUPLICATE_ID` |
| Stages count | Must have 3-10 stages | `COGNITIVE_INVALID_STAGES` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | System-defined |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

## Reference Integrity

> Cross-library integrity rules enforced by Librarian.

| Field | References | On Archive | On Delete |
|-------|------------|------------|-----------|
| (none - cognitive-types is reference data) | - | - | - |

**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| `templates` | defaultCognitiveType | set null |
| `outlines` | cognitiveTypeId | warn |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `outlines` | Outlines specify cognitive type |
| `content-types` | Content types suggest frameworks |
| `section-specs` | Stages map to section specs |

---

## Transition Archetypes

Narrative transition patterns for connecting sections and ideas within content.

| Archetype | Purpose | Example |
|-----------|---------|---------|
| `bridge` | Smooth connection between related ideas | "This leads naturally to..." |
| `pivot` | Shift direction while maintaining thread | "But here's where it gets interesting..." |
| `zoom-in` | Move from general to specific | "Let's look at this more closely..." |
| `zoom-out` | Move from specific to broader context | "Stepping back, we can see..." |
| `contrast` | Highlight differences between concepts | "On the other hand..." |
| `callback` | Reference earlier point for cohesion | "Remember when we mentioned..." |
| `escalate` | Build intensity or stakes | "And it doesn't stop there..." |
| `resolve` | Bring threads together | "So what does this all mean?" |

### Usage in Outlines

Transitions are suggested between sections based on:
- Cognitive type flow (linear vs branching)
- Section relationship (supporting vs contrasting)
- Audience preferences (direct vs narrative)

### Integration Notes (Dev Team Gap - 2026-01-20)
- OutlineGenerator should suggest transitions between sections
- TemplateEngine may include transition placeholders
- Transitions adapt to brand voice (formal vs conversational)

---

## Migration Notes

**From BrandLib:**
- `content/story-frameworks/default.json` → cognitive types
- 16 story frameworks with full stage definitions
- Includes micro_story_templates → variations

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandLib story-frameworks*
