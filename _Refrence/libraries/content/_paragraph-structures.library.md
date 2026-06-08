# Paragraph Structures Library

> **System:** Content
> **Repository:** Content Repository
> **Path:** `/data/paragraph-structures.sqlite`
> **Library Type:** reference
> **Status:** Draft
> **Source:** Academic structures + therapeutic adaptations (human-curated)

---

## Overview

Paragraph structure frameworks define **how individual paragraphs are built** within sections. This is **Layer 4** in the four-layer framework architecture.

**Four-Layer Framework Architecture:**

```
LAYER 1: EVIDENCE-BASED FRAMEWORKS (credibility layer)
         → See: _frameworks.library.md
         ↓
LAYER 2: PROPRIETARY FRAMEWORKS (brand voice + philosophy layer)
         → See: _frameworks.library.md
         ↓
LAYER 3: CONTENT-TYPE FRAMEWORKS (structural delivery layer)
         → See: _content-type-frameworks.library.md
         ↓
LAYER 4: PARAGRAPH STRUCTURE FRAMEWORKS (paragraph-level coherence)
         → THIS LIBRARY
         → Defines how to build individual paragraphs
         → Components follow specific structures (PEARL, SAFE, etc.)
         → Cognitive level variants adjust complexity
```

**Purpose:**
- Define how paragraphs are structured within sections
- Provide component-by-component guidance for paragraph generation
- Support cognitive level variants for each structure
- Align paragraph patterns with therapeutic philosophy

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `OutlineGenerator` | Read via Librarian | Assign paragraph structures to sections |
| `TemplateEngine` | Read via Librarian | Render paragraphs following structure |
| `ContentManager` | Read/Write (Owner) | Structure curation (human only) |

---

## SQLite Schema

### Main Tables

```sql
CREATE TABLE paragraph_structures (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,                    -- PEEL, PEARL, SAFE, etc.
  category TEXT NOT NULL,                -- Academic, Therapeutic, Narrative, etc.
  description TEXT,                      -- How this structure works
  purpose TEXT,                          -- What this structure is best for
  best_for_sections TEXT,                -- JSON array of section types
  cognitive_levels TEXT,                 -- JSON array of compatible levels [1,2,3,4,5]
  therapeutic_alignment TEXT,            -- How it aligns with brand philosophy
  status TEXT DEFAULT 'active',
  created_at TEXT DEFAULT (datetime('now')),
  updated_at TEXT
);

CREATE TABLE paragraph_components (
  id TEXT PRIMARY KEY,
  paragraph_structure_id TEXT NOT NULL,
  component_order INTEGER NOT NULL,
  component_name TEXT NOT NULL,          -- e.g., "Point", "Evidence", "Explain"
  component_label TEXT,                  -- User-friendly name
  purpose TEXT,                          -- What this component delivers
  guidance TEXT,                         -- How to write it
  examples TEXT,                         -- JSON array of sample component text
  word_count_range TEXT,                 -- Typical length (e.g., "15-30 words")
  tone_notes TEXT,                       -- Voice guidance for this component
  FOREIGN KEY (paragraph_structure_id) REFERENCES paragraph_structures(id)
);

CREATE TABLE paragraph_cognitive_variants (
  id TEXT PRIMARY KEY,
  paragraph_structure_id TEXT NOT NULL,
  cognitive_level INTEGER NOT NULL,      -- 1-5
  component_variants TEXT,               -- JSON object: how each component changes
  sentence_length_max INTEGER,
  sentence_count_per_component INTEGER,
  jargon_allowed BOOLEAN DEFAULT false,
  complexity_notes TEXT,
  FOREIGN KEY (paragraph_structure_id) REFERENCES paragraph_structures(id),
  UNIQUE(paragraph_structure_id, cognitive_level)
);

-- Indexes
CREATE INDEX idx_ps_category ON paragraph_structures(category);
CREATE INDEX idx_ps_status ON paragraph_structures(status);
CREATE INDEX idx_pc_structure ON paragraph_components(paragraph_structure_id);
CREATE INDEX idx_pcv_structure ON paragraph_cognitive_variants(paragraph_structure_id);
CREATE INDEX idx_pcv_level ON paragraph_cognitive_variants(cognitive_level);
```

---

## Paragraph Structure Categories

| Category | Description | Example Structures |
|----------|-------------|-------------------|
| `academic` | Formal analytical structures from academic writing | PEEL, TEEL, MEAL, CER, TIQA |
| `therapeutic` | Adapted for therapeutic/self-help content | PEARL, SAFE |
| `narrative` | Problem-solution and story-driven | SCQA, PAS, AIDA |
| `learning` | Process and experiential learning | Kolb's Cycle, ADDIE |
| `emotional` | Communication and feeling-focused | NVC, Cognitive Triangle |

---

## Structure Catalog

### Academic/Analytical Structures

| ID | Name | Components | Best For |
|----|------|------------|----------|
| `peel` | PEEL | Point, Evidence, Explain, Link | Academic explanations |
| `teel` | TEEL | Topic, Explain, Evidence, Link | Topic introductions |
| `meal` | MEAL | Main idea, Evidence, Analysis, Link | Research-backed content |
| `cer` | CER | Claim, Evidence, Reasoning | Persuasive paragraphs |
| `tiqa` | TIQA | Topic, Introduce, Quote, Analysis | Citation-based content |

### Therapeutic/Action-Oriented Structures

| ID | Name | Components | Best For |
|----|------|------------|----------|
| `pearl` | PEARL | Point, Example, Application, Reflection, Link | Therapeutic explanations (primary) |
| `safe` | SAFE | State, Anchor, Frame, Extend | Joy/Safety/Values philosophy |

### Narrative/Problem-Solution Structures

| ID | Name | Components | Best For |
|----|------|------------|----------|
| `scqa` | SCQA | Situation, Complication, Question, Answer | Problem framing |
| `pas` | PAS | Problem, Agitation, Solution | Marketing/persuasion |
| `aida` | AIDA | Attention, Interest, Desire, Action | Call-to-action content |

### Learning/Process Structures

| ID | Name | Components | Best For |
|----|------|------------|----------|
| `kolb_paragraph` | Kolb's Cycle | Experience, Reflect, Conceptualise, Experiment | Experiential learning |
| `addie_paragraph` | ADDIE | Analyse, Design, Develop, Implement, Evaluate | Process explanations |

### Emotional/Communication Structures

| ID | Name | Components | Best For |
|----|------|------------|----------|
| `nvc_paragraph` | NVC | Observation, Feeling, Need, Request | Communication guidance |
| `cog_triangle` | Cognitive Triangle | Thoughts, Feelings, Behaviours | CBT content |

---

## Entry Schema

### Paragraph Structure Entry

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique identifier (e.g., "pearl") |
| `name` | string | Yes | Human-readable name (e.g., "PEARL") |
| `category` | string | Yes | Category (academic, therapeutic, etc.) |
| `description` | string | Yes | How this structure works |
| `purpose` | string | Yes | What this structure is best for |
| `bestForSections` | string[] | Yes | Section types this works well with |
| `cognitiveLevels` | number[] | Yes | Compatible cognitive levels [1-5] |
| `therapeuticAlignment` | string | No | How it aligns with brand philosophy |
| `components` | Component[] | Yes | Ordered array of components |

### Component Entry

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `order` | number | Yes | Position in structure (1-indexed) |
| `name` | string | Yes | Component name (e.g., "Point") |
| `label` | string | Yes | User-friendly label |
| `purpose` | string | Yes | What this component delivers |
| `guidance` | string | Yes | How to write it |
| `examples` | string[] | No | Sample component text |
| `wordCountRange` | string | No | Typical length (e.g., "15-30 words") |
| `toneNotes` | string | No | Voice guidance for this component |

### Cognitive Variant Entry

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `cognitiveLevel` | number | Yes | Level 1-5 |
| `componentVariants` | object | Yes | How each component changes at this level |
| `sentenceLengthMax` | number | No | Max sentence length (words) |
| `sentenceCountPerComponent` | number | No | Typical sentences per component |
| `jargonAllowed` | boolean | No | Whether technical terms allowed |
| `complexityNotes` | string | No | Level-specific guidance |

---

## Example Entries

### PEARL Structure (Primary Therapeutic)

```json
{
  "id": "pearl",
  "name": "PEARL",
  "category": "therapeutic",
  "description": "Point, Example, Application, Reflection, Link. A therapeutic adaptation of academic PEEL that uses everyday examples instead of citations and includes reflection.",
  "purpose": "Explaining concepts while maintaining engagement and self-reflection",

  "bestForSections": ["mechanism", "definition", "recognition", "psychoeducation"],
  "cognitiveLevels": [1, 2, 3, 4, 5],

  "therapeuticAlignment": "Supports invitation-based learning. Uses everyday examples so readers see themselves. Reflection builds self-knowledge without prescription.",

  "components": [
    {
      "order": 1,
      "name": "Point",
      "label": "Introduce the concept",
      "purpose": "State clearly what you're explaining",
      "guidance": "One sentence. What is the main idea?",
      "wordCountRange": "15-30 words",
      "toneNotes": "Clear, authoritative, invitation-based"
    },
    {
      "order": 2,
      "name": "Example",
      "label": "Everyday example",
      "purpose": "Make it concrete with a relatable scenario",
      "guidance": "2-3 sentences. Use reader context (parent, partner, worker, etc.). Make it specific but universal.",
      "wordCountRange": "40-80 words",
      "toneNotes": "Warm, matter-of-fact, non-judgmental",
      "examples": [
        "You're in a meeting and someone raises their voice unexpectedly. Your heart starts racing before you've even processed what's happening.",
        "Picture your morning routine when everything goes wrong - the coffee spills, the kids are late, traffic is worse than usual."
      ]
    },
    {
      "order": 3,
      "name": "Application",
      "label": "How this works in the concept",
      "purpose": "Explain what's happening in the example through the lens of the concept",
      "guidance": "2-3 sentences. What does this example show about the concept?",
      "wordCountRange": "40-80 words",
      "toneNotes": "Analytical but warm"
    },
    {
      "order": 4,
      "name": "Reflection",
      "label": "Reflect on the pattern",
      "purpose": "Invite reader to see themselves in this",
      "guidance": "1 sentence that invites reflection without demand",
      "wordCountRange": "15-30 words",
      "toneNotes": "Curious, permission-giving",
      "examples": [
        "Notice: this response made sense at the time.",
        "You might recognise this pattern in your own experience.",
        "This is worth pausing on for a moment."
      ]
    },
    {
      "order": 5,
      "name": "Link",
      "label": "Bridge to next",
      "purpose": "Connect to what comes next or deepen the concept",
      "guidance": "1 sentence bridging concept or inviting further exploration",
      "wordCountRange": "10-20 words",
      "toneNotes": "Warm, forward-moving"
    }
  ],

  "cognitiveVariants": [
    {
      "cognitiveLevel": 1,
      "componentVariants": {
        "point": "Can be 2 sentences, include theoretical background",
        "example": "Can be research study or detailed scenario",
        "application": "Can reference multiple mechanisms",
        "reflection": "Optional, can be skipped"
      },
      "sentenceLengthMax": 25,
      "jargonAllowed": true
    },
    {
      "cognitiveLevel": 3,
      "componentVariants": {
        "point": "1 clear sentence",
        "example": "Everyday scenario, specific but universal",
        "application": "Clear explanation without jargon",
        "reflection": "Always include, brief"
      },
      "sentenceLengthMax": 20,
      "jargonAllowed": false
    },
    {
      "cognitiveLevel": 4,
      "componentVariants": {
        "point": "1 very simple sentence",
        "example": "Very specific scenario, shorter version",
        "application": "Number the steps in the example",
        "reflection": "Very brief, structured prompt"
      },
      "sentenceLengthMax": 15,
      "jargonAllowed": false
    },
    {
      "cognitiveLevel": 5,
      "componentVariants": {
        "point": "Essential statement only",
        "example": "Minimal scenario or skip",
        "application": "1 sentence max",
        "reflection": "Single grounding question"
      },
      "sentenceLengthMax": 10,
      "jargonAllowed": false,
      "complexityNotes": "Crisis mode - bare essentials only"
    }
  ]
}
```

### SAFE Structure (Joy/Safety/Values Philosophy)

```json
{
  "id": "safe",
  "name": "SAFE",
  "category": "therapeutic",
  "description": "State, Anchor, Frame, Extend. Directly aligned with Joy + Safety + Values philosophy. Grounds concepts in reader experience before extending to possibility.",
  "purpose": "Building safety while introducing new concepts",

  "bestForSections": ["introduction", "opening", "insight", "reframe"],
  "cognitiveLevels": [2, 3, 4, 5],

  "therapeuticAlignment": "Core brand structure. State creates clarity. Anchor grounds in body/experience. Frame offers the insight. Extend invites possibility without prescription.",

  "components": [
    {
      "order": 1,
      "name": "State",
      "label": "State the concept",
      "purpose": "Clear statement of what you're introducing",
      "guidance": "1 sentence. Direct, clear, no hedging.",
      "wordCountRange": "10-25 words",
      "toneNotes": "Clear, warm, authoritative"
    },
    {
      "order": 2,
      "name": "Anchor",
      "label": "Anchor in experience",
      "purpose": "Ground in reader's body or lived experience",
      "guidance": "1-2 sentences. 'You know that feeling when...' or 'Notice what happens in your body when...'",
      "wordCountRange": "20-50 words",
      "toneNotes": "Inviting, grounded, body-aware",
      "examples": [
        "You know that feeling when you suddenly feel on high alert?",
        "Notice what happens in your chest when you read those words.",
        "Think of the last time you felt truly safe with someone."
      ]
    },
    {
      "order": 3,
      "name": "Frame",
      "label": "Frame the insight",
      "purpose": "Offer the reframe or insight about safety/joy/values",
      "guidance": "1-2 sentences. The 'aha' - what this means.",
      "wordCountRange": "20-50 words",
      "toneNotes": "Warm, revelatory without dramatic",
      "examples": [
        "That's not a malfunction. It's your system detecting threat.",
        "This response kept you safe once. It made sense then."
      ]
    },
    {
      "order": 4,
      "name": "Extend",
      "label": "Extend to possibility",
      "purpose": "Open to possibility or action without prescription",
      "guidance": "1 sentence. What becomes possible now? Invitation, not instruction.",
      "wordCountRange": "15-30 words",
      "toneNotes": "Hopeful, agency-focused, permission-giving",
      "examples": [
        "Understanding this pattern is the first step to working with it.",
        "From here, you get to choose what comes next.",
        "This awareness opens new possibilities."
      ]
    }
  ],

  "cognitiveVariants": [
    {
      "cognitiveLevel": 3,
      "componentVariants": {
        "state": "Clear, accessible statement",
        "anchor": "Everyday body/emotional experience",
        "frame": "Insight in plain language",
        "extend": "Gentle invitation"
      },
      "sentenceLengthMax": 20,
      "jargonAllowed": false
    },
    {
      "cognitiveLevel": 5,
      "componentVariants": {
        "state": "Shortest possible statement",
        "anchor": "Single grounding cue",
        "frame": "Core insight only",
        "extend": "Optional - may skip"
      },
      "sentenceLengthMax": 10,
      "jargonAllowed": false,
      "complexityNotes": "Crisis/overwhelm mode - safety-focused, minimal"
    }
  ]
}
```

### CER Structure (Evidence-Based)

```json
{
  "id": "cer",
  "name": "CER",
  "category": "academic",
  "description": "Claim, Evidence, Reasoning. Research-backed structure for presenting factual claims with supporting evidence.",
  "purpose": "Making evidence-backed claims with clear reasoning",

  "bestForSections": ["mechanism", "research", "explanation"],
  "cognitiveLevels": [1, 2, 3],

  "therapeuticAlignment": "Useful when credibility matters. Adapted for therapeutic context by using accessible evidence and warm reasoning.",

  "components": [
    {
      "order": 1,
      "name": "Claim",
      "label": "State the claim",
      "purpose": "Clear statement of what you're asserting",
      "guidance": "1 sentence. What is the factual claim?",
      "wordCountRange": "15-30 words",
      "toneNotes": "Clear, confident, not dogmatic"
    },
    {
      "order": 2,
      "name": "Evidence",
      "label": "Provide evidence",
      "purpose": "Support the claim with evidence",
      "guidance": "1-3 sentences. Research finding, statistic, or observable pattern.",
      "wordCountRange": "30-80 words",
      "toneNotes": "Matter-of-fact, accessible",
      "examples": [
        "Research shows: elevated heart rate, rapid breathing, muscle tension are common responses.",
        "Studies consistently find that this pattern appears across cultures and age groups."
      ]
    },
    {
      "order": 3,
      "name": "Reasoning",
      "label": "Explain the connection",
      "purpose": "Connect evidence to claim - why does this matter?",
      "guidance": "1-2 sentences. What does this evidence mean for the reader?",
      "wordCountRange": "25-60 words",
      "toneNotes": "Analytical but warm, connecting to lived experience"
    }
  ]
}
```

---

## Section-to-Structure Mapping

Default paragraph structure recommendations by section type:

| Section Type | Primary Structure | Alternative |
|--------------|-------------------|-------------|
| `definition` | PEARL | SAFE |
| `mechanism` | PEARL | CER |
| `recognition` | PEARL | SAFE |
| `self_mapping` | SAFE | PEARL |
| `reflection_before` | SAFE | — |
| `reflection_after` | SAFE | — |
| `experiential_exercise` | SAFE | — |
| `response_repertoire` | PEARL | — |
| `practice_protocol` | PEARL | — |
| `summary` | PEARL | — |
| `introduction` | SAFE | SCQA |
| `closing` | SAFE | — |

---

## Integration with Generation Stack

### Query Flow

```
OutlineGenerator assigns paragraph structures to sections
    ↓
TemplateEngine loads structure for each paragraph
    ↓
For each paragraph:
    1. Load structure components
    2. Load cognitive level variant
    3. Generate each component following guidance
    4. Apply brand voice
    5. Assemble into paragraph
    6. Validate all components present
```

### Example Paragraph Generation

```javascript
// Input: Generate mechanism paragraph about stress response
{
  sectionType: "mechanism",
  paragraphStructure: "pearl",
  cognitiveLevel: 3,
  content: "Explaining how the alert state works"
}

// Process:
// 1. Load PEARL structure
// 2. Load Level 3 variants
// 3. Generate components:
//    Point: "Here's what happens in the alert state..."
//    Example: "You're in a meeting and someone raises their voice..."
//    Application: "Your nervous system registers this as potential danger..."
//    Reflection: "Notice: this response kept you safe in the past."
//    Link: "Now we'll explore what happens in each state..."
// 4. Apply brand voice to each
// 5. Assemble into paragraph
```

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `section-specs` | `bestForSections` | soft | Reference only |
| `cognitive-types` | `cognitiveLevels` | soft | Reference only |

---

## Reference Integrity

| Field | References | On Archive | On Delete |
|-------|------------|------------|-----------|
| `bestForSections` | section-specs.id | warn | warn |

**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| `outlines` | section.paragraphStructure | warn |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (ContentManager) | Structures are human-curated |
| Update | Human (ContentManager) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

**Note:** AI does NOT generate or modify paragraph structures. They are reference data curated by humans.

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 20 | ~15 core structures |
| **Entry Size** | medium | 2-5KB typical |
| **Cache TTL** | 24h | Structures rarely change |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `frameworks` | Paragraph structures complement framework stages |
| `content-type-frameworks` | Content types suggest paragraph structures |
| `section-specs` | Sections specify compatible structures |
| `cognitive-types` | Variants adjust for cognitive level |
| `outlines` | Outlines assign structures to sections |

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Status: Draft*
