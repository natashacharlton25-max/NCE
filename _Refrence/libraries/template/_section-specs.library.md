# Section Specs Library

> **System:** Template
> **Repository:** Template Repository
> **Path:** `/data/section-specs.sqlite`
> **Library Type:** reference
> **Status:** Draft
> **Source:** Migrated from BrandLib `content/hook-patterns`, `content/cta-patterns`, `content/transitions`

---

## Overview

Section type definitions for templates. Defines structure, requirements, and constraints for each type of content section. Section specs provide the building blocks that templates and outlines use to structure content.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `TemplateEngine` | Read via FileManager | Section structure for generation |
| `OutlineGenerator` | Read via FileManager | Section specs for outline structure |
| `Writer` | Read via FileManager | Writing guidance per section |

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

CREATE TABLE section_specs (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW section_specs_latest AS
SELECT * FROM section_specs t1
WHERE version = (SELECT MAX(version) FROM section_specs t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/SectionSpec.ts`
> Source: BrandLib content patterns

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "section_hook") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `purpose` | string | Essential | What this section accomplishes |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `category` | string | null | Supporting | Section category |
| `strength` | enum | 'medium' | Supporting | 'soft' / 'medium' / 'strong' |
| `patterns` | Pattern[] | [] | Essential | Example patterns/templates |
| `formulas` | Formula[] | [] | Essential | Reusable formulas |
| `fields` | Field[] | [] | Essential | Field definitions |
| `constraints` | Constraint[] | [] | Supporting | Validation rules |
| `position` | string | 'any' | Supporting | 'start' / 'middle' / 'end' / 'any' |
| `requiredFor` | string[] | [] | Supporting | Content types requiring this |
| `toneMatch` | string[] | [] | Supporting | Compatible tones |
| `avoidWhen` | string[] | [] | Supporting | When NOT to use |

### Pattern Object

```json
{
  "id": "curiosity-001",
  "name": "Hidden Truth Reveals",
  "templates": [
    "There is something nobody tells you about [TOPIC].",
    "Most people get [TOPIC] completely wrong.",
    "What if everything you believed about [TOPIC] was backwards?"
  ],
  "useFor": ["blog_posts", "social_media", "email_subject_lines"],
  "toneMatch": ["direct", "authentic", "conversational"],
  "avoidWhen": ["academic_content", "sensitive_topics"]
}
```

### Formula Object

```json
{
  "id": "formula-001",
  "name": "The Possibility Question",
  "formula": "What if [desired outcome] was possible in [timeframe]?",
  "examples": [
    "What if peace with food was possible in 90 days?",
    "What if confident communication was possible in one month?"
  ],
  "worksBestFor": ["transformation_programmes", "coaching_offers"]
}
```

### Field Object

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Field name |
| `type` | string | 'text' / 'rich-text' / 'list' / 'number' |
| `required` | boolean | Whether field is required |
| `maxLength` | number | Maximum length (optional) |
| `guidance` | string | How to fill this field |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `category` | Quick grouping |
| `position` | Where in content |

**Example preview JSON:**

```json
{
  "name": "Hook",
  "category": "opening",
  "position": "start"
}
```

---

## Staging Area

**Path:** `/repos/drafts/section-specs/`

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `category` | No | Yes | No | Filter |
| `position` | No | Yes | No | Filter |
| `status` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Finite section types |
| **Entry Size** | medium | 1-5KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | yes | Load patterns on demand |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "section_hook",
  "name": "Hook",
  "schemaVersion": "1.0.0",
  "status": "active",
  "purpose": "Grab attention and create curiosity to keep reading"
}
```

### Complete (all fields)

```json
{
  "id": "section_hook",
  "name": "Hook",
  "schemaVersion": "1.0.0",
  "status": "active",
  "purpose": "Grab attention and create curiosity to keep reading",
  "category": "opening",
  "strength": "strong",
  "position": "start",
  "requiredFor": ["contenttype_blog-post", "contenttype_email", "contenttype_social"],
  "patterns": [
    {
      "id": "curiosity-001",
      "name": "Hidden Truth Reveals",
      "templates": [
        "There is something nobody tells you about [TOPIC].",
        "Most people get [TOPIC] completely wrong.",
        "What if everything you believed about [TOPIC] was backwards?",
        "The real reason [OUTCOME] happens has nothing to do with [COMMON BELIEF]."
      ],
      "useFor": ["blog_posts", "social_media", "email_subject_lines"],
      "toneMatch": ["direct", "authentic", "conversational"],
      "avoidWhen": ["academic_content", "formal_reports", "sensitive_topics"]
    },
    {
      "id": "validation-001",
      "name": "Validation Starters",
      "templates": [
        "If [COMMON STRUGGLE] sounds familiar, you are not alone.",
        "That feeling when [RELATABLE FRUSTRATION]? There is a reason for it.",
        "Struggling with [CHALLENGE] does not mean something is wrong with you."
      ],
      "useFor": ["therapeutic_content", "support_resources", "community_posts"],
      "toneMatch": ["validating", "warm", "understanding"],
      "avoidWhen": ["celebratory_content", "achievement_focused_posts"]
    }
  ],
  "formulas": [
    {
      "id": "formula-001",
      "name": "The Possibility Question",
      "formula": "What if [desired outcome] was possible in [timeframe]?",
      "examples": [
        "What if peace with food was possible in 90 days?",
        "What if confident communication was possible in one month?"
      ],
      "worksBestFor": ["transformation_programmes", "coaching_offers"]
    },
    {
      "id": "formula-002",
      "name": "The Hidden Truth",
      "formula": "The real reason [common struggle] is not [surface explanation].",
      "examples": [
        "The real reason boundaries feel hard is not about being selfish.",
        "The real reason change is difficult is not about willpower."
      ],
      "worksBestFor": ["myth_busting_content", "differentiation"]
    }
  ],
  "fields": [
    {
      "name": "content",
      "type": "text",
      "required": true,
      "maxLength": 200,
      "guidance": "Write a compelling opening that creates curiosity"
    }
  ],
  "constraints": [
    {
      "rule": "maxLength",
      "value": 200,
      "message": "Hook should be concise - under 200 characters"
    }
  ],
  "toneMatch": ["direct", "warm", "provocative", "validating"],
  "avoidWhen": ["formal_communications", "technical_documentation"]
}
```

### Additional Section Specs

```json
{
  "id": "section_problem",
  "name": "Problem",
  "purpose": "Define the pain point or challenge the audience faces",
  "category": "body",
  "position": "start",
  "patterns": [
    {
      "id": "frustration-001",
      "name": "Frustration Acknowledgers",
      "templates": [
        "[CHALLENGE] can feel impossible. Until you try this approach.",
        "Tired of [FRUSTRATING PATTERN]? There is another way.",
        "When [COMMON ADVICE] keeps failing, here is what actually works."
      ],
      "useFor": ["sales_pages", "lead_magnets", "transformation_content"],
      "toneMatch": ["direct", "confident", "solution-focused"]
    }
  ]
}
```

```json
{
  "id": "section_cta",
  "name": "Call to Action",
  "purpose": "Drive specific action from the reader",
  "category": "closing",
  "position": "end",
  "strength": "strong",
  "patterns": [
    {
      "id": "direct-001",
      "name": "Direct Invitations",
      "templates": [
        "Start [POSITIVE ACTION] today. Here is how.",
        "Ready to [DESIRED OUTCOME]? Let us begin.",
        "Take the first step toward [GOAL] right now."
      ],
      "useFor": ["programme_launches", "course_intros", "challenge_kickoffs"],
      "toneMatch": ["energising", "confident", "action-oriented"]
    },
    {
      "id": "gentle-001",
      "name": "Gentle Invitations",
      "templates": [
        "When you are ready, [RESOURCE/SUPPORT] is here.",
        "Consider exploring [TOPIC] at your own pace.",
        "This might be the support you have been looking for."
      ],
      "useFor": ["therapeutic_content", "resource_sharing", "community_building"],
      "toneMatch": ["patient", "supportive", "non-pressuring"]
    }
  ]
}
```

```json
{
  "id": "section_transition",
  "name": "Transition",
  "purpose": "Bridge between sections maintaining flow",
  "category": "connector",
  "position": "middle",
  "patterns": [
    {
      "id": "transition-001",
      "name": "Story Transitions",
      "templates": [
        "And then everything shifted.",
        "But here's what nobody tells you.",
        "That's when I realised.",
        "Looking back, I can see.",
        "What I didn't know then was."
      ],
      "useFor": ["blog_posts", "personal_stories", "case_studies"]
    }
  ]
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `section_{slug}` pattern | `SECTION_INVALID_ID` |
| Required fields | All required fields present | `SECTION_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `SECTION_DUPLICATE_ID` |
| Position valid | Must be start/middle/end/any | `SECTION_INVALID_POSITION` |

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
| `templates` | Templates define section order |
| `outlines` | Outlines reference section specs |
| `content-types` | Content types require specific sections |

---

## Migration Notes

**From BrandLib:**
- `content/hook-patterns/default.json` → hook section patterns
- `content/cta-patterns/default.json` → CTA section patterns
- `content/transitions/default.json` → transition phrases

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
*Source: BrandLib content patterns*
