# Libraries Index

> **Purpose:** Central index of all libraries in the system
> **Type:** Reference Document
> **Status:** Active
> **Version:** 1.0.0

---

## Overview

Libraries store structured data that modules read and write. Each library has a spec defining its schema, access patterns, and relationships.

**Library Types:**
- **generated**: AI-generated content (themes, outlines)
- **reference**: Human-curated reference data (frameworks, cognitive-types)
- **core**: Foundational brand/config data (brands, voices, tones)
- **derived**: Built from other libraries (templates from section-specs)

---

## Libraries by System

### Brand System

| Library | Path | Type | Write Owner | Status |
|---------|------|------|-------------|--------|
| [brands](../libraries/brand/_brands.library.md) | `/data/brands.sqlite` | core | BrandManager | Draft |
| [audiences](../libraries/brand/_audiences.library.md) | `/data/audiences.sqlite` | core | BrandManager | Draft |
| [voices](../libraries/brand/_voices.library.md) | `/data/voices.sqlite` | core | BrandManager | Draft |
| [tones](../libraries/brand/_tones.library.md) | `/data/tones.sqlite` | core | BrandManager | Draft |

### Content System - Generated

| Library | Path | Type | Write Owner | Status |
|---------|------|------|-------------|--------|
| [themes](../libraries/content/_themes.library.md) | `/data/themes.sqlite` | generated | ThemeGenerator | Draft |
| [outlines](../libraries/content/_outlines.library.md) | `/data/outlines.sqlite` | generated | OutlineGenerator | Draft |
| [outcomes](../libraries/content/_outcomes.library.md) | `/data/outcomes.sqlite` | reference | ContentManager | Draft |

### Content System - Reference

| Library | Path | Type | Write Owner | Status |
|---------|------|------|-------------|--------|
| [frameworks](../libraries/content/_frameworks.library.md) | `/data/frameworks/` | reference | ContentManager | Draft |
| [content-type-frameworks](../libraries/content/_content-type-frameworks.library.md) | `/data/content-type-frameworks.sqlite` | reference | ContentManager | Draft |
| [paragraph-structures](../libraries/content/_paragraph-structures.library.md) | `/data/paragraph-structures.sqlite` | reference | ContentManager | Draft |

### Template System

| Library | Path | Type | Write Owner | Status |
|---------|------|------|-------------|--------|
| [templates](../libraries/template/_templates.library.md) | `/data/templates.sqlite` | derived | TemplateFactory | Draft |
| [section-specs](../libraries/template/_section-specs.library.md) | `/data/section-specs.sqlite` | reference | ContentManager | Draft |
| [content-types](../libraries/template/_content-types.library.md) | `/data/content-types.sqlite` | reference | ContentManager | Draft |
| [cognitive-types](../libraries/template/_cognitive-types.library.md) | `/data/cognitive-types.sqlite` | reference | ContentManager | Draft |

### Social System

| Library | Path | Type | Write Owner | Status |
|---------|------|------|-------------|--------|
| [social-templates](../libraries/social/_social-templates.library.md) | `/data/social-templates.sqlite` | derived | TemplateFactory | Draft |

---

## Library Dependency Graph

```
brands
├── audiences (brandId)
├── themes (brandId)
├── frameworks/proprietary (brandId)
└── voices/tones (referenced by brand)

outcomes
└── themes (targetOutcomes[])

cognitive-types
├── templates (defaultCognitiveType)
└── outlines (cognitiveTypeId)

section-specs
├── templates (sections[].type)
└── outlines (sections[].type)

content-types
└── templates (contentType)

frameworks (Layer 1 + 2: evidence-based + proprietary)
├── content-type-frameworks (recommendedFrameworks[])
├── themes (frameworkIds[])
└── outlines (frameworkStageId per section)

content-type-frameworks (Layer 3: structural delivery)
├── section-specs (sectionSpecId references)
└── outlines (section sequence + word counts)

paragraph-structures (Layer 4: paragraph-level coherence)
├── section-specs (bestForSections[])
├── cognitive-types (cognitiveVariants)
└── outlines (section.paragraphStructure)

themes
└── outlines (themeId) [HARD]

templates
└── outlines (templateId) [HARD]
```

---

## Access Patterns

### Direct Fetch (via FileManager)
When you know the ID:
```javascript
FileManager.get('themes', 'theme_rest-productivity')
```

### Discovery (via Librarian)
When searching/filtering:
```javascript
Librarian.search('themes', { brandId: 'brand_bylw', status: 'active' })
Librarian.recommend('frameworks', { category: 'skill_building', cognitive: 3 })
```

---

## Common Queries by Module

| Module | Libraries Accessed | Query Type |
|--------|-------------------|------------|
| ThemeGenerator | outcomes, brands, audiences | Direct fetch |
| OutlineGenerator | themes, templates, frameworks, cognitive-types | Direct + search |
| TemplateEngine | outlines, templates, section-specs, brands | Direct fetch |
| Librarian | All | Search, list, recommend |

---

## Schema Versioning

All libraries use versioned entries:
```sql
CREATE TABLE {library} (
  id TEXT,
  version INTEGER,
  data JSON,
  PRIMARY KEY (id, version)
);

CREATE VIEW {library}_latest AS
SELECT * FROM {library} t1
WHERE version = (SELECT MAX(version) FROM {library} t2 WHERE t2.id = t1.id);
```

**Version Policy:**
- Reads default to latest version
- Explicit version reads supported for history
- Writes always create new version
- Deletes are soft (archive status)

---

## Full-Text Search (FTS)

Libraries with FTS enabled:

| Library | FTS Fields |
|---------|------------|
| themes | name, topic, concept, message, keywords |
| outlines | name, themeId, templateId, contentType |
| templates | name |
| frameworks | name, descriptions |
| content-type-frameworks | name, coreQuestion |
| paragraph-structures | name, description |
| cognitive-types | name |
| section-specs | name |
| brands | name |
| audiences | name |

---

## Reference Integrity

All libraries include:
- `_index` table with `ref_status` field
- Cascade rules defined in Librarian
- Dependencies documented in each library spec

See [Librarian](../systems/_Librarian/Librarian.md) for cascade rules.

---

## Adding New Libraries

When adding a new library:

1. Create spec file: `libraries/{system}/_libraryname.library.md`
2. Follow [library template](../libraries/template/_section-specs.library.md) structure
3. Add to this index
4. Add cascade rules to Librarian
5. Document in consuming module specs

---

*Created: 2026-01-20*
*Status: Active*
