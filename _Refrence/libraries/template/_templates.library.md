# Templates Library

> **System:** Template
> **Repository:** Template Repository
> **Path:** `/data/templates.sqlite`
> **Library Type:** derived
> **Status:** Draft

---

## Overview

Document templates for content generation. Templates define section structure, ordering, and configuration for specific content outputs. Each template combines a content type with specific section specifications to create a reusable content structure.

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `TemplateFactory` | Read/Write (Owner) | CRUD operations on templates |
| `TemplateEngine` | Read (Direct) | Template for content generation |
| `LegalDocCreator` | Read via Librarian | Template for document generation |
| `OutlineGenerator` | Read via Librarian | Template structure for outlines |

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

CREATE TABLE templates (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW templates_latest AS
SELECT * FROM templates t1
WHERE version = (SELECT MAX(version) FROM templates t2 WHERE t2.id = t1.id);

CREATE VIRTUAL TABLE templates_fts USING fts5(
  id,
  name,
  content='templates',
  content_rowid='rowid'
);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/Template.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "template_blog-standard") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `contentType` | string | Essential | Content type reference |
| `sections` | Section[] | Essential | Ordered section definitions |

### Section Definition Object

```json
{
  "id": "section_1",
  "type": "section_hook",
  "order": 1,
  "required": true,
  "config": {
    "strength": "strong",
    "maxLength": 200,
    "patterns": ["curiosity-001", "validation-001"]
  }
}
```

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Section ID within template |
| `type` | string | Essential | Section spec reference |
| `order` | integer | Essential | Position in template |
| `required` | boolean | Essential | Whether section is required |
| `config` | object | Supporting | Section-specific configuration |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `description` | string | null | Supporting | Template description |
| `category` | string | null | Supporting | Template category |
| `defaultBrandId` | string | null | Supporting | Default brand for this template |
| `defaultCognitiveType` | string | null | Supporting | Default cognitive framework |
| `estimatedLength` | string | null | Supporting | Expected output length |
| `platforms` | string[] | [] | Supporting | Where this template is used |
| `metadata` | object | {} | Truncatable | Additional metadata |

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `contentType` | Filter by content type |
| `category` | Filter by category |
| `sectionCount` | Quick indicator |

**Example preview JSON:**

```json
{
  "name": "Standard Blog Post",
  "contentType": "contenttype_blog-post",
  "category": "blog",
  "sectionCount": 7
}
```

---

## Staging Area

**Path:** `/repos/drafts/templates/`

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `content-types` | `contentType` | soft | Reference only |
| `section-specs` | `sections[].type` | soft | Reference only |
| `brands` | `defaultBrandId` | soft | Optional reference |
| `cognitive-types` | `defaultCognitiveType` | soft | Optional reference |

---

## Reference Integrity

> Cross-library integrity rules enforced by Librarian.

| Field | References | On Archive | On Delete |
|-------|------------|------------|-----------|
| `contentType` | content-types.id | warn | warn |
| `sections[].type` | section-specs.id | warn | warn |
| `defaultBrandId` | brands.id | set null | set null |
| `defaultCognitiveType` | cognitive-types.id | set null | set null |

**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| `outlines` | templateId | mark `ref_status='stale'` |

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `contentType` | No | Yes | No | Exact match |
| `category` | No | Yes | No | Exact match |
| `status` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Finite template types |
| **Entry Size** | medium | 1-10KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | yes | Load sections on demand |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "template_blog-simple",
  "name": "Simple Blog Post",
  "schemaVersion": "1.0.0",
  "status": "active",
  "contentType": "contenttype_blog-post",
  "sections": [
    {
      "id": "section_1",
      "type": "section_hook",
      "order": 1,
      "required": true
    },
    {
      "id": "section_2",
      "type": "section_body",
      "order": 2,
      "required": true
    },
    {
      "id": "section_3",
      "type": "section_cta",
      "order": 3,
      "required": false
    }
  ]
}
```

### Complete (all fields)

```json
{
  "id": "template_blog-problem-solution",
  "name": "Problem-Solution Blog Post",
  "schemaVersion": "1.0.0",
  "status": "active",
  "description": "Standard blog template using problem-agitate-solution framework. Ideal for educational content that drives action.",
  "category": "blog",
  "contentType": "contenttype_blog-post",
  "defaultCognitiveType": "cognitive_problem-solution",
  "estimatedLength": "1000-1500 words",
  "platforms": ["blog", "linkedin", "newsletter"],
  "sections": [
    {
      "id": "section_1",
      "type": "section_hook",
      "order": 1,
      "required": true,
      "config": {
        "strength": "strong",
        "maxLength": 200,
        "preferredPatterns": ["curiosity-001", "validation-001"],
        "guidance": "Open with a relatable struggle or counterintuitive claim"
      }
    },
    {
      "id": "section_2",
      "type": "section_problem",
      "order": 2,
      "required": true,
      "config": {
        "minLength": 150,
        "maxLength": 300,
        "guidance": "Define the specific pain point the reader faces"
      }
    },
    {
      "id": "section_3",
      "type": "section_agitate",
      "order": 3,
      "required": true,
      "config": {
        "minLength": 150,
        "maxLength": 300,
        "guidance": "Deepen awareness of the problem's real impact without being manipulative"
      }
    },
    {
      "id": "section_4",
      "type": "section_solution",
      "order": 4,
      "required": true,
      "config": {
        "minLength": 400,
        "maxLength": 800,
        "guidance": "Present your approach as the answer, with practical steps"
      }
    },
    {
      "id": "section_5",
      "type": "section_proof",
      "order": 5,
      "required": false,
      "config": {
        "minLength": 100,
        "maxLength": 200,
        "guidance": "Include evidence, examples, or social proof"
      }
    },
    {
      "id": "section_6",
      "type": "section_transition",
      "order": 6,
      "required": false,
      "config": {
        "maxLength": 100,
        "guidance": "Bridge from solution to call-to-action"
      }
    },
    {
      "id": "section_7",
      "type": "section_cta",
      "order": 7,
      "required": true,
      "config": {
        "strength": "medium",
        "maxLength": 150,
        "preferredPatterns": ["gentle-001"],
        "guidance": "Invite action without being pushy"
      }
    }
  ],
  "metadata": {
    "targetWordCount": 1200,
    "readingLevel": "general",
    "seoOptimized": true
  }
}
```

### Additional Templates

```json
{
  "id": "template_personal-story",
  "name": "Personal Story Post",
  "description": "Template for sharing lived experience stories. Creates connection through vulnerability.",
  "category": "story",
  "contentType": "contenttype_personal-story",
  "defaultCognitiveType": "cognitive_origin-story",
  "sections": [
    {
      "id": "section_1",
      "type": "section_hook",
      "order": 1,
      "required": true,
      "config": {
        "strength": "soft",
        "preferredPatterns": ["story-001", "story-003"]
      }
    },
    {
      "id": "section_2",
      "type": "section_story_before",
      "order": 2,
      "required": true,
      "config": {
        "guidance": "Describe the situation before the change - relatable, specific"
      }
    },
    {
      "id": "section_3",
      "type": "section_story_catalyst",
      "order": 3,
      "required": true,
      "config": {
        "guidance": "The moment or event that triggered change"
      }
    },
    {
      "id": "section_4",
      "type": "section_story_struggle",
      "order": 4,
      "required": false,
      "config": {
        "guidance": "The difficulty of change - honest but not graphic"
      }
    },
    {
      "id": "section_5",
      "type": "section_story_discovery",
      "order": 5,
      "required": true,
      "config": {
        "guidance": "What you learned or discovered"
      }
    },
    {
      "id": "section_6",
      "type": "section_story_now",
      "order": 6,
      "required": true,
      "config": {
        "guidance": "Where you are now - honest, not 'fixed'"
      }
    },
    {
      "id": "section_7",
      "type": "section_reflection",
      "order": 7,
      "required": true,
      "config": {
        "guidance": "What you would tell someone in that situation"
      }
    }
  ]
}
```

```json
{
  "id": "template_email-welcome",
  "name": "Welcome Email",
  "description": "First email in a sequence. Warm, sets expectations.",
  "category": "email",
  "contentType": "contenttype_email",
  "estimatedLength": "300-500 words",
  "sections": [
    {
      "id": "section_1",
      "type": "section_greeting",
      "order": 1,
      "required": true,
      "config": {
        "maxLength": 50,
        "guidance": "Warm, personal greeting"
      }
    },
    {
      "id": "section_2",
      "type": "section_welcome",
      "order": 2,
      "required": true,
      "config": {
        "guidance": "Welcome them, acknowledge their decision"
      }
    },
    {
      "id": "section_3",
      "type": "section_expectations",
      "order": 3,
      "required": true,
      "config": {
        "guidance": "What they can expect from this list"
      }
    },
    {
      "id": "section_4",
      "type": "section_quick_win",
      "order": 4,
      "required": false,
      "config": {
        "guidance": "Provide immediate value"
      }
    },
    {
      "id": "section_5",
      "type": "section_cta",
      "order": 5,
      "required": false,
      "config": {
        "strength": "soft",
        "guidance": "Gentle invitation to reply or engage"
      }
    },
    {
      "id": "section_6",
      "type": "section_signoff",
      "order": 6,
      "required": true,
      "config": {
        "guidance": "Warm sign-off with name"
      }
    }
  ]
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `template_{slug}` pattern | `TEMPLATE_INVALID_ID` |
| Required fields | All required fields present | `TEMPLATE_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `TEMPLATE_DUPLICATE_ID` |
| Content type valid | contentType must exist | `TEMPLATE_INVALID_CONTENT_TYPE` |
| Sections ordered | Section order must be sequential | `TEMPLATE_INVALID_SECTION_ORDER` |
| Section types valid | Each section type must exist in section-specs | `TEMPLATE_INVALID_SECTION_TYPE` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `TemplateFactory` | Module-managed |
| Update | `TemplateFactory` | Creates new version |
| Archive | `TemplateFactory` | Soft delete |
| Delete | Never | Archive instead |

**Note:** TemplateEngine reads templates directly (own database), but TemplateFactory handles all writes.

---

## Related

| Related To | Relationship |
|------------|--------------|
| `content-types` | Template implements one content type |
| `section-specs` | Template uses multiple section specs |
| `cognitive-types` | Template may suggest cognitive framework |
| `outlines` | Outlines follow template structure |
| `brands` | Templates may have default brand |

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
