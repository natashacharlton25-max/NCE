# Content-Type Frameworks Library

> **System:** Content
> **Repository:** Content Repository
> **Path:** `/data/content-type-frameworks.sqlite`
> **Library Type:** reference
> **Status:** Draft
> **Source:** Human-curated structural specifications

---

## Overview

Content-type frameworks define **how workbook types are structured** - the required sections, their sequence, element requirements, and word count targets. This is **Layer 3** in the three-layer framework architecture.

**Three-Layer Architecture:**

```
LAYER 1: EVIDENCE-BASED FRAMEWORKS (credibility layer)
         → See: _frameworks.library.md
         ↓
LAYER 2: PROPRIETARY FRAMEWORKS (brand voice + philosophy layer)
         → See: _frameworks.library.md
         ↓
LAYER 3: CONTENT-TYPE FRAMEWORKS (structural delivery layer)
         → THIS LIBRARY
         → Maps workbook types to section sequences
         → Defines required elements per section
         → Sets word count targets by cognitive level
```

**Purpose:**
- Define what sections each workbook type needs
- Specify required vs optional sections
- Map evidence-based frameworks to section types
- Set word count targets by cognitive level

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `OutlineGenerator` | Read via Librarian | Get section sequence for workbook type |
| `TemplateEngine` | Read via Librarian | Validate section completeness |
| `ContentManager` | Read/Write (Owner) | Content-type curation (human only) |

---

## Directory Structure

```
/data/content-type-frameworks.sqlite
├── _index                    → Master index
├── workbook_types           → Workbook type definitions
├── section_mappings         → Workbook → section relationships
└── element_requirements     → Required elements per section type
```

---

## SQLite Schema

### Master Index

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  type TEXT,                           -- 'workbook_type' | 'section_mapping'
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE INDEX idx_ctf_type ON _index(type);
CREATE INDEX idx_ctf_status ON _index(status);
```

### Workbook Types Table

```sql
CREATE TABLE workbook_types (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW workbook_types_latest AS
SELECT * FROM workbook_types t1
WHERE version = (SELECT MAX(version) FROM workbook_types t2 WHERE t2.id = t1.id);
```

### Section Mappings Table

```sql
CREATE TABLE section_mappings (
  id TEXT PRIMARY KEY,
  workbook_type_id TEXT NOT NULL,
  section_spec_id TEXT NOT NULL,       -- References section-specs library
  order_position INTEGER NOT NULL,
  required BOOLEAN DEFAULT true,
  recommended_frameworks TEXT,         -- JSON array of framework IDs
  word_count_by_level JSON,           -- Word counts per cognitive level
  FOREIGN KEY (workbook_type_id) REFERENCES workbook_types(id)
);

CREATE INDEX idx_mappings_workbook ON section_mappings(workbook_type_id);
CREATE INDEX idx_mappings_section ON section_mappings(section_spec_id);
```

---

## Entry Schema

### Workbook Type Entry

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique identifier (e.g., "concept_explainer") |
| `name` | string | Yes | Human-readable name |
| `schemaVersion` | string | Yes | Schema version |
| `status` | EntryStatus | Yes | Lifecycle state |
| `coreQuestion` | string | Yes | The question this type answers |
| `purpose` | string | Yes | What this workbook type achieves |
| `balance` | Balance | Yes | Understanding vs action ratio |
| `sections` | SectionRef[] | Yes | Ordered section references |
| `defaultFrameworks` | string[] | No | Default frameworks for this type |

### Balance Object

| Field | Type | Description |
|-------|------|-------------|
| `understand` | number | Percentage for understanding (0-100) |
| `action` | number | Percentage for action (0-100) |
| `description` | string | Human-readable balance description |

### Section Reference Object

| Field | Type | Description |
|-------|------|-------------|
| `sectionSpecId` | string | References section-specs library |
| `order` | number | Position in workbook |
| `required` | boolean | Must be included |
| `recommendedFrameworks` | string[] | Framework IDs that work well here |
| `wordCountByLevel` | object | Word count targets per cognitive level |

---

## Workbook Types Catalog

| Type | Core Question | Balance | Primary Sections |
|------|---------------|---------|------------------|
| `concept_explainer` | What is this and how does it work? | 60% explain / 40% apply | Definition, Mechanism, Recognition, Reflection |
| `skill_builder` | How do I do this? | 30% understand / 70% practice | Foundation, Practice, Response Repertoire, Protocol |
| `self_discovery` | Who am I? What's true for me? | 20% framework / 80% explore | Framing, Self-Mapping, Reflection, Synthesis |
| `processing` | How do I digest what happened? | 30% understand / 70% process | Context, Recognition, Processing, Integration |
| `planning_action` | What will I do? | 20% understand / 80% plan | Assessment, Goal Setting, Implementation, Tracking |
| `relationship_focused` | How do I navigate this with others? | 40% understand / 60% skills | Patterns, Communication, Practice, Repair |
| `recovery_maintenance` | How do I keep going? | 20% understand / 80% maintain | Foundations, Warning Signs, Response Plan, Support |
| `assessment` | Where am I with this? | 80% assess / 20% interpret | Introduction, Assessment, Scoring, Next Steps |

---

## Example Entry

### Concept Explainer Workbook Type

```json
{
  "id": "concept_explainer",
  "name": "Concept Explainer",
  "schemaVersion": "1.0.0",
  "status": "active",

  "coreQuestion": "What is this and how does it work?",
  "purpose": "Help readers understand a psychological concept deeply enough to recognize it in their own experience",

  "balance": {
    "understand": 60,
    "action": 40,
    "description": "60% explain, 40% apply"
  },

  "sections": [
    {
      "sectionSpecId": "definition_foundations",
      "order": 1,
      "required": true,
      "recommendedFrameworks": [],
      "wordCountByLevel": {
        "1": 800,
        "2": 700,
        "3": 600,
        "4": 500,
        "5": 300
      }
    },
    {
      "sectionSpecId": "mechanism_how_it_works",
      "order": 2,
      "required": true,
      "recommendedFrameworks": ["cognitive_triangle", "polyvagal_ladder"],
      "wordCountByLevel": {
        "1": 1200,
        "2": 1000,
        "3": 900,
        "4": 700,
        "5": 400
      }
    },
    {
      "sectionSpecId": "recognition_what_it_looks_like",
      "order": 3,
      "required": true,
      "recommendedFrameworks": [],
      "wordCountByLevel": {
        "1": 1000,
        "2": 900,
        "3": 800,
        "4": 600,
        "5": 350
      }
    },
    {
      "sectionSpecId": "self_mapping_exercise",
      "order": 4,
      "required": true,
      "recommendedFrameworks": ["kolb_experiential"],
      "wordCountByLevel": {
        "1": 600,
        "2": 550,
        "3": 500,
        "4": 450,
        "5": 250
      }
    },
    {
      "sectionSpecId": "reflection_after",
      "order": 5,
      "required": true,
      "recommendedFrameworks": ["rain_mindfulness"],
      "wordCountByLevel": {
        "1": 400,
        "2": 350,
        "3": 300,
        "4": 250,
        "5": 150
      }
    },
    {
      "sectionSpecId": "response_repertoire",
      "order": 6,
      "required": false,
      "recommendedFrameworks": ["com_b", "implementation_intentions"],
      "wordCountByLevel": {
        "1": 1000,
        "2": 900,
        "3": 800,
        "4": 700,
        "5": 400
      }
    },
    {
      "sectionSpecId": "summary",
      "order": 7,
      "required": true,
      "recommendedFrameworks": [],
      "wordCountByLevel": {
        "1": 400,
        "2": 350,
        "3": 300,
        "4": 300,
        "5": 200
      }
    }
  ],

  "defaultFrameworks": ["cognitive_triangle", "kolb_experiential"]
}
```

### Skill Builder Workbook Type

```json
{
  "id": "skill_builder",
  "name": "Skill Builder",
  "schemaVersion": "1.0.0",
  "status": "active",

  "coreQuestion": "How do I do this?",
  "purpose": "Build practical capability through structured practice and skill development",

  "balance": {
    "understand": 30,
    "action": 70,
    "description": "30% understand, 70% practice"
  },

  "sections": [
    {
      "sectionSpecId": "definition_foundations",
      "order": 1,
      "required": true,
      "recommendedFrameworks": [],
      "wordCountByLevel": {
        "1": 600,
        "2": 500,
        "3": 450,
        "4": 400,
        "5": 250
      }
    },
    {
      "sectionSpecId": "mechanism_how_it_works",
      "order": 2,
      "required": false,
      "recommendedFrameworks": [],
      "wordCountByLevel": {
        "1": 800,
        "2": 700,
        "3": 600,
        "4": 500,
        "5": 300
      }
    },
    {
      "sectionSpecId": "experiential_exercise",
      "order": 3,
      "required": true,
      "recommendedFrameworks": ["kolb_experiential", "deliberate_practice"],
      "wordCountByLevel": {
        "1": 800,
        "2": 750,
        "3": 700,
        "4": 600,
        "5": 350
      }
    },
    {
      "sectionSpecId": "reflection_before",
      "order": 4,
      "required": true,
      "recommendedFrameworks": [],
      "wordCountByLevel": {
        "1": 300,
        "2": 275,
        "3": 250,
        "4": 225,
        "5": 150
      }
    },
    {
      "sectionSpecId": "response_repertoire",
      "order": 5,
      "required": true,
      "recommendedFrameworks": ["dreyfus_skill_acquisition"],
      "wordCountByLevel": {
        "1": 1200,
        "2": 1100,
        "3": 1000,
        "4": 850,
        "5": 500
      }
    },
    {
      "sectionSpecId": "practice_protocol",
      "order": 6,
      "required": true,
      "recommendedFrameworks": ["implementation_intentions", "habit_loop"],
      "wordCountByLevel": {
        "1": 800,
        "2": 750,
        "3": 700,
        "4": 600,
        "5": 350
      }
    },
    {
      "sectionSpecId": "reflection_after",
      "order": 7,
      "required": true,
      "recommendedFrameworks": ["kolb_experiential"],
      "wordCountByLevel": {
        "1": 400,
        "2": 350,
        "3": 300,
        "4": 275,
        "5": 175
      }
    },
    {
      "sectionSpecId": "summary",
      "order": 8,
      "required": true,
      "recommendedFrameworks": [],
      "wordCountByLevel": {
        "1": 350,
        "2": 300,
        "3": 275,
        "4": 250,
        "5": 175
      }
    }
  ],

  "defaultFrameworks": ["kolb_experiential", "deliberate_practice", "implementation_intentions"]
}
```

---

## Integration with Other Layers

### Layer 1 + 2 → Layer 3 Flow

```
Evidence-Based Framework (Layer 1)
    e.g., Kolb's Experiential Learning
              ↓
Proprietary Interpretation (Layer 2)
    e.g., "Mind the Box" lens on Kolb
              ↓
Content-Type Framework (Layer 3)
    e.g., Skill Builder workbook type
    - Uses Kolb stages to sequence sections
    - Applies MTB language from interpretation
    - Validates required elements present
```

### Selection Logic

When OutlineGenerator builds an outline:

```
1. Get workbook type from content-type-frameworks
2. Get ordered sections with requirements
3. For each section:
   a. Get recommended frameworks (Layer 1)
   b. Check for proprietary interpretations (Layer 2)
   c. Apply word count targets for cognitive level
   d. Map framework stages to section elements
```

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `section-specs` | `sectionSpecId` | hard | Block archive if active mappings |
| `frameworks` | `recommendedFrameworks` | soft | Reference only |
| `cognitive-types` | `wordCountByLevel` | soft | Reference only |

---

## Reference Integrity

| Field | References | On Archive | On Delete |
|-------|------------|------------|-----------|
| `sectionSpecId` | section-specs.id | block | block |
| `recommendedFrameworks` | frameworks.id | warn | mark stale |

**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| `outlines` | (indirect via template) | warn |
| `templates` | workbookTypeId | warn |

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `coreQuestion` | Yes | No | No | Full-text |
| `balance.understand` | No | Yes | Yes | Range filter |
| `status` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 20 | 8 core types + variations |
| **Entry Size** | medium | 3-8KB typical |
| **FTS Enabled** | yes | name, coreQuestion |
| **Lazy Loading** | yes | Preview in _index |
| **Cache TTL** | 24h | Content types rarely change |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (ContentManager) | Content types are human-curated |
| Update | Human (ContentManager) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

**Note:** AI does NOT generate or modify content-type frameworks. They are reference data curated by humans.

---

## Related

| Related To | Relationship |
|------------|--------------|
| `frameworks` | Recommended frameworks per section |
| `section-specs` | Section specifications referenced |
| `cognitive-types` | Word counts vary by level |
| `templates` | Templates implement content types |
| `outlines` | Outlines follow content type structure |

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Status: Draft*
