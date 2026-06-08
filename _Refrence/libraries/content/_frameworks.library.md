# Frameworks Library

> **System:** Content
> **Repository:** Content Repository
> **Path:** `/data/frameworks/`
> **Library Type:** reference
> **Status:** Draft
> **Source:** Evidence-based frameworks + brand proprietary frameworks

---

## Overview

Psychological and educational frameworks that guide content structure. Frameworks provide the theoretical foundation for how content is organized and sequenced. They are **read-only reference data** - no AI writes to them, they're human-curated.

**Four-Layer Framework Architecture:**

```
LAYER 1: EVIDENCE-BASED FRAMEWORKS (credibility layer)
         → THIS LIBRARY (evidence-based/)
         ↓
LAYER 2: PROPRIETARY FRAMEWORKS (brand voice + philosophy layer)
         → THIS LIBRARY (proprietary/)
         ↓
LAYER 3: CONTENT-TYPE FRAMEWORKS (structural delivery layer)
         → See: _content-type-frameworks.library.md
         ↓
LAYER 4: PARAGRAPH STRUCTURE FRAMEWORKS (paragraph-level coherence)
         → See: _paragraph-structures.library.md
```

**This Library Contains:**
- **Evidence-based**: Published frameworks from psychology, education, behavior change research
- **Proprietary**: Brand-specific frameworks developed for particular methodologies
- **Interpretations**: How proprietary frameworks reframe evidence-based frameworks

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `OutlineGenerator` | Read via Librarian | Select appropriate structure for content |
| `TemplateEngine` | Read via Librarian | Apply correct structure to sections |
| `ContentManager` | Read/Write (Owner) | Framework curation (human only) |

---

## Directory Structure

```
/data/frameworks/
├── _index.sqlite                    → Master index across all frameworks
├── evidence-based/
│   ├── skill-building.sqlite
│   ├── behaviour-change.sqlite
│   ├── goal-setting.sqlite
│   ├── emotional-processing.sqlite
│   ├── cognitive-work.sqlite
│   ├── relationships.sqlite
│   ├── recovery.sqlite
│   ├── self-discovery.sqlite
│   └── trauma-informed.sqlite
└── proprietary/
    ├── brand_{brand-id}.sqlite      → Per-brand proprietary frameworks
    └── ...
```

---

## SQLite Schema

### Master Index (`_index.sqlite`)

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  category TEXT,                     -- 'skill_building', 'behaviour_change', etc.
  source_type TEXT,                  -- 'evidence_based' | 'proprietary'
  brand_id TEXT,                     -- NULL for evidence-based, brand ID for proprietary
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,                         -- JSON array
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE INDEX idx_frameworks_category ON _index(category);
CREATE INDEX idx_frameworks_source ON _index(source_type);
CREATE INDEX idx_frameworks_brand ON _index(brand_id);
```

### Framework Tables (per category)

```sql
CREATE TABLE frameworks (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW frameworks_latest AS
SELECT * FROM frameworks t1
WHERE version = (SELECT MAX(version) FROM frameworks t2 WHERE t2.id = t1.id);

CREATE VIRTUAL TABLE frameworks_fts USING fts5(
  id,
  name,
  category,
  description_professional,
  description_peer_led,
  content='frameworks',
  content_rowid='rowid'
);
```

### Framework Interpretations Table (proprietary only)

How proprietary frameworks interpret and reframe evidence-based frameworks:

```sql
CREATE TABLE framework_interpretations (
  id TEXT PRIMARY KEY,
  proprietary_framework_id TEXT NOT NULL,
  evidence_framework_id TEXT NOT NULL,
  interpretation_lens TEXT,              -- How brand views the evidence framework
  language_adjustments JSON,             -- Term replacements (e.g., "failure" → "learning")
  example_rewrite TEXT,                  -- Before/after example of reframing
  applies_to_cognitive_levels INTEGER[], -- Which levels this interpretation covers
  created_at TEXT DEFAULT (datetime('now')),
  FOREIGN KEY (proprietary_framework_id) REFERENCES frameworks(id),
  FOREIGN KEY (evidence_framework_id) REFERENCES frameworks(id)
);

CREATE INDEX idx_interpretations_proprietary ON framework_interpretations(proprietary_framework_id);
CREATE INDEX idx_interpretations_evidence ON framework_interpretations(evidence_framework_id);
```

**Example Interpretation:**
```json
{
  "id": "interp_mtb-five-step_kolb",
  "proprietary_framework_id": "mtb_five_step",
  "evidence_framework_id": "kolb_experiential",
  "interpretation_lens": "Through the lens of limiting beliefs as 'boxes' that can be expanded",
  "language_adjustments": {
    "concrete_experience": "Notice the Box",
    "reflective_observation": "Question the Walls",
    "abstract_conceptualization": "Expand the View"
  },
  "example_rewrite": "Instead of 'What patterns do you see?' → 'What box has been keeping you safe here?'",
  "applies_to_cognitive_levels": [3, 4, 5]
}
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Framework.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "kolb_experiential") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `category` | string | Essential | Framework category (see categories below) |
| `descriptions` | Descriptions | Essential | Professional and peer-led descriptions |
| `structure` | Structure | Essential | Framework stages/components |

### Descriptions Object

| Field | Type | Description |
|-------|------|-------------|
| `professional` | string | Formal description for professional context |
| `peerLed` | string | Accessible description for peer-led context |

### Source Object (evidence-based only)

| Field | Type | Description |
|-------|------|-------------|
| `creator` | string | Original creator/researcher |
| `year` | number | Publication year |
| `publication` | string | Original publication/book |
| `field` | string | Academic field |

### Background Object

| Field | Type | Description |
|-------|------|-------------|
| `origin` | string | How/why framework was developed |
| `evidenceBase` | string | Research supporting it |
| `whyItMatters` | string | Relevance to content creation |
| `commonApplications` | string[] | Where it's typically used |
| `limitations` | string | Known limitations or caveats |

### Structure Object

| Field | Type | Description |
|-------|------|-------------|
| `type` | enum | 'sequential' / 'cyclical' / 'branching' / 'matrix' |
| `stages` | Stage[] | Ordered array of stages |
| `transitions` | Transition[] | How to move between stages |

### Stage Object

```json
{
  "id": "stage_concrete_experience",
  "name": "Concrete Experience",
  "order": 1,
  "purpose": "Direct engagement with the learning situation",
  "prompts": [
    "What specific situation did you encounter?",
    "What happened?"
  ],
  "sectionTypes": ["exercise", "reflection"],
  "duration": "short"
}
```

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `source` | Source | null | Supporting | Origin information (evidence-based) |
| `background` | Background | null | Supporting | Context and research |
| `useInSections` | string[] | [] | Essential | Section types this applies to |
| `bestFor` | string[] | [] | Essential | Content categories it excels at |
| `cognitiveLevels` | number[] | [1-5] | Supporting | Which cognitive levels it serves |
| `pairsWellWith` | string[] | [] | Supporting | Other frameworks it complements |
| `brandId` | string | null | Essential | Brand ID (proprietary only) |
| `adaptations` | Adaptation[] | [] | Supporting | Brand-specific adaptations |

---

## Framework Categories

| Category | Description | Example Frameworks |
|----------|-------------|-------------------|
| `skill_building` | Learning and practice | Kolb, Deliberate Practice, Scaffolded Learning |
| `behaviour_change` | Habit and behavior modification | Stages of Change, Habit Loop, COM-B |
| `goal_setting` | Planning and achievement | SMART Goals, Implementation Intentions, WOOP |
| `emotional_processing` | Feeling and processing emotions | RAIN, Emotion Regulation, Window of Tolerance |
| `cognitive_work` | Thinking patterns and beliefs | CBT Triangle, Cognitive Restructuring, Reframing |
| `relationships` | Connection and communication | Gottman, Nonviolent Communication, Attachment |
| `recovery` | Healing and restoration | Stages of Grief, Recovery Wheel, Restoration |
| `self_discovery` | Identity and values | Values Clarification, Johari Window, Life Design |
| `trauma_informed` | Sensitive and safe approaches | Trauma-Informed Care, Grounding, Safety Planning |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `category` | Filter by category |
| `sourceType` | Evidence-based vs proprietary |
| `brandId` | Filter by brand (proprietary) |
| `bestFor` | Quick relevance check |

**Example preview JSON:**

```json
{
  "name": "Kolb's Experiential Learning Cycle",
  "category": "skill_building",
  "sourceType": "evidence_based",
  "brandId": null,
  "bestFor": ["skill_building", "reflection"]
}
```

---

## Staging Area

**Path:** `/repos/drafts/frameworks/`

**Note:** Frameworks are human-curated. AI does not generate or modify frameworks. The staging area is for human review before adding to the library.

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `brands` | `brandId` | soft | Proprietary frameworks orphaned if brand archived |
| `cognitive-types` | `cognitiveLevels` | soft | Reference only |
| `section-specs` | `useInSections` | soft | Reference only |

---

## Reference Integrity

> Cross-library integrity rules enforced by Librarian.

| Field | References | On Archive | On Delete |
|-------|------------|------------|-----------|
| `brandId` | brands.id | warn | mark orphaned |

**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| `outlines` | (indirect) | warn |
| `templates` | (indirect) | warn |

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `category` | No | Yes | No | Enum filter |
| `sourceType` | No | Yes | No | Enum filter |
| `brandId` | No | Yes | No | Exact match |
| `bestFor` | No | Yes | No | Array contains |
| `cognitiveLevels` | No | Yes | No | Array contains |
| `status` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 200 | ~50 evidence-based + ~150 proprietary across brands |
| **Entry Size** | medium | 2-5KB typical |
| **FTS Enabled** | yes | name, descriptions |
| **Lazy Loading** | yes | Preview in _index, full load on demand |
| **Cache TTL** | 24h | Frameworks rarely change |

---

## Example Entry

### Evidence-Based Framework

```json
{
  "id": "kolb_experiential",
  "name": "Kolb's Experiential Learning Cycle",
  "schemaVersion": "1.0.0",
  "status": "active",
  "category": "skill_building",
  "sourceType": "evidence_based",
  "brandId": null,

  "source": {
    "creator": "David Kolb",
    "year": 1984,
    "publication": "Experiential Learning: Experience as the Source of Learning and Development",
    "field": "Educational psychology"
  },

  "descriptions": {
    "professional": "A cyclical model proposing that effective learning occurs through four stages: concrete experience, reflective observation, abstract conceptualization, and active experimentation.",
    "peerLed": "Learning works best when you actually try something, think about what happened, figure out why, and then try again with what you learned. It's a cycle that keeps building."
  },

  "background": {
    "origin": "Developed from earlier work by Lewin, Dewey, and Piaget on experiential education",
    "evidenceBase": "Widely validated in educational settings; foundational to experiential learning pedagogy",
    "whyItMatters": "Provides structure for moving from passive consumption to active skill development",
    "commonApplications": ["Professional development", "Skills training", "Coaching", "Reflective practice"],
    "limitations": "Can oversimplify learning; not all topics suit cyclical approach; individual learning styles vary"
  },

  "structure": {
    "type": "cyclical",
    "stages": [
      {
        "id": "concrete_experience",
        "name": "Concrete Experience",
        "order": 1,
        "purpose": "Direct engagement with the learning situation",
        "prompts": ["What specific situation did you encounter?", "What happened?"],
        "sectionTypes": ["exercise", "scenario"],
        "duration": "medium"
      },
      {
        "id": "reflective_observation",
        "name": "Reflective Observation",
        "order": 2,
        "purpose": "Reviewing and reflecting on the experience",
        "prompts": ["What did you notice?", "How did it feel?", "What surprised you?"],
        "sectionTypes": ["reflection", "questions"],
        "duration": "medium"
      },
      {
        "id": "abstract_conceptualization",
        "name": "Abstract Conceptualization",
        "order": 3,
        "purpose": "Making sense of the experience through theory",
        "prompts": ["What patterns do you see?", "What principles apply here?"],
        "sectionTypes": ["teaching", "framework"],
        "duration": "long"
      },
      {
        "id": "active_experimentation",
        "name": "Active Experimentation",
        "order": 4,
        "purpose": "Planning how to apply learning",
        "prompts": ["What will you try next?", "How will you apply this?"],
        "sectionTypes": ["action_planning", "commitment"],
        "duration": "short"
      }
    ],
    "transitions": [
      { "from": "concrete_experience", "to": "reflective_observation", "archetype": "zoom-out" },
      { "from": "reflective_observation", "to": "abstract_conceptualization", "archetype": "bridge" },
      { "from": "abstract_conceptualization", "to": "active_experimentation", "archetype": "pivot" },
      { "from": "active_experimentation", "to": "concrete_experience", "archetype": "callback" }
    ]
  },

  "useInSections": ["skill_practice", "reflection", "exercise", "action_planning"],
  "bestFor": ["skill_building", "professional_development", "coaching"],
  "cognitiveLevels": [2, 3, 4, 5],
  "pairsWellWith": ["deliberate_practice", "scaffolded_learning"]
}
```

### Proprietary Framework

```json
{
  "id": "mtb_five_step",
  "name": "Mind the Box Five-Step Framework",
  "schemaVersion": "1.0.0",
  "status": "active",
  "category": "cognitive_work",
  "sourceType": "proprietary",
  "brandId": "brand_mind-the-box",

  "descriptions": {
    "professional": "A proprietary cognitive reframing methodology developed for identifying and challenging limiting beliefs in professional contexts.",
    "peerLed": "Five simple steps to notice when you're stuck in old thinking patterns and find new ways forward."
  },

  "structure": {
    "type": "sequential",
    "stages": [
      {
        "id": "notice",
        "name": "Notice the Box",
        "order": 1,
        "purpose": "Identify when you're operating from limiting beliefs",
        "prompts": ["What assumption am I making?", "Where did this belief come from?"],
        "sectionTypes": ["awareness", "reflection"]
      },
      {
        "id": "question",
        "name": "Question the Walls",
        "order": 2,
        "purpose": "Challenge the validity of the limiting belief",
        "prompts": ["Is this always true?", "What evidence contradicts this?"],
        "sectionTypes": ["inquiry", "reframe"]
      },
      {
        "id": "expand",
        "name": "Expand the View",
        "order": 3,
        "purpose": "Consider alternative perspectives",
        "prompts": ["What else could be true?", "How might someone else see this?"],
        "sectionTypes": ["perspective", "exploration"]
      },
      {
        "id": "choose",
        "name": "Choose Your Response",
        "order": 4,
        "purpose": "Select a more empowering belief or action",
        "prompts": ["What belief would serve me better?", "What action aligns with who I want to be?"],
        "sectionTypes": ["decision", "commitment"]
      },
      {
        "id": "practice",
        "name": "Practice the New",
        "order": 5,
        "purpose": "Reinforce the new pattern through action",
        "prompts": ["What small step can I take today?", "How will I remember this?"],
        "sectionTypes": ["action_planning", "habit"]
      }
    ]
  },

  "useInSections": ["cognitive_reframe", "belief_work", "mindset"],
  "bestFor": ["cognitive_work", "professional_development", "leadership"],
  "cognitiveLevels": [3, 4, 5],
  "pairsWellWith": ["cbt_triangle", "reframing_techniques"]
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `{source}_{slug}` pattern | `FRAMEWORK_INVALID_ID` |
| Required fields | All required fields present | `FRAMEWORK_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `FRAMEWORK_DUPLICATE_ID` |
| Category valid | Category must be in allowed list | `FRAMEWORK_INVALID_CATEGORY` |
| Brand valid | If proprietary, brandId must exist | `FRAMEWORK_INVALID_BRAND` |
| Stages ordered | Stage order must be sequential | `FRAMEWORK_INVALID_STAGE_ORDER` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (ContentManager) | Frameworks are human-curated |
| Update | Human (ContentManager) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

**Note:** AI does NOT generate or modify frameworks. They are reference data curated by humans.

---

## Pipeline Integration

How frameworks flow through the generation stack:

```
┌─────────────────────────────────────────────────────────────────┐
│                    FRAMEWORK INTEGRATION FLOW                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. ThemeGenerator                                              │
│     ├─ Query: frameworks by category + brand                    │
│     ├─ Select: 1-3 frameworks matching theme goal               │
│     └─ Store: frameworkIds[] in theme                           │
│                          ↓                                       │
│  2. OutlineGenerator                                            │
│     ├─ Load: selected frameworks from theme                     │
│     ├─ Map: framework stages → section sequence                 │
│     ├─ Apply: proprietary interpretations (if brand has them)   │
│     └─ Store: frameworkStageId per section                      │
│                          ↓                                       │
│  3. TemplateEngine                                              │
│     ├─ Load: content-type framework for workbook type           │
│     ├─ Merge: evidence structure + proprietary lens             │
│     ├─ Inject: stage prompts into section generation            │
│     └─ Validate: all required elements present                  │
│                          ↓                                       │
│  4. Paragraph Rendering (Layer 4)                               │
│     ├─ Load: paragraph structure for section (PEARL, SAFE, etc.)│
│     ├─ Apply: cognitive level variants                          │
│     ├─ Generate: each component following structure guidance    │
│     └─ Assemble: components into coherent paragraph             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Framework Query by Module

| Module | Query Type | Fields Used |
|--------|------------|-------------|
| ThemeGenerator | Search by category | `category`, `bestFor`, `brandId` |
| OutlineGenerator | Fetch by ID + interpretations | `id`, `structure.stages`, `interpretations` |
| TemplateEngine | Fetch content-type framework | Content-type framework library |

---

## Selection Logic for OutlineGenerator

When selecting frameworks for content generation:

```
1. Identify content category (skill_building, emotional_processing, etc.)
2. Filter frameworks by category
3. If brand has proprietary frameworks for category, include those
4. Load interpretations for evidence→proprietary mappings
5. Score frameworks by:
   - bestFor match with content goal
   - cognitiveLevels match with audience
   - pairsWellWith match with other selected frameworks
   - interpretation coverage (if proprietary exists)
6. Return top 1-3 frameworks with their interpretations
```

---

## Related

| Related To | Relationship |
|------------|--------------|
| `brands` | Proprietary frameworks belong to brands |
| `cognitive-types` | Frameworks serve specific cognitive levels |
| `section-specs` | Framework stages map to section types |
| `content-type-frameworks` | Layer 3 - sequences frameworks across sections |
| `paragraph-structures` | Layer 4 - structures paragraphs within sections |
| `themes` | Themes store selected frameworkIds |
| `outlines` | Outlines map framework stages to sections |
| `templates` | Templates may suggest default frameworks |

---

## Migration Notes

**New library** - no migration from BrandLib required.

Evidence-based frameworks to be curated from published sources.
Proprietary frameworks to be documented from brand methodologies.

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Status: Draft*
