# Schema Authority Map

> **Purpose:** Declare ownership of schemas and canonical fields
> **Type:** Governance Document
> **Status:** Active
> **Version:** 1.0.0

---

## Overview

This document declares which module owns which schema and has authority to define its fields. When adding new fields, check this map to ensure you're modifying through the correct owner.

**Rules:**
1. Only the owner module can add fields to a schema
2. Other modules may READ fields but not define new ones
3. Cross-module field requests go through the owner's spec
4. Library schemas follow their write owner (from `_libraries-index.md`)

---

## Generated Content Schemas

### Theme
**Owner:** ThemeGenerator
**Spec:** `systems/ThemeGenerator/ThemeGenerator.md`
**Library:** `libraries/content/_themes.library.md`

| Field | Description | Added |
|-------|-------------|-------|
| `id`, `name`, `topic` | Core identity | v1.0 |
| `concept`, `message` | Central idea | v1.0 |
| `truths[]` | Master truth statements | v1.0 |
| `truthRelationshipType` | How truths relate | v1.5 |
| `beliefShifts[]` | From/to transformations | v1.0 |
| `transformationFramework` | Belief journey structure | v1.6 |
| `tone`, `keywords[]` | Voice and SEO | v1.0 |
| `angleMetadata` | Angle selection context | v1.6 |
| `frameworkIds[]` | Selected frameworks (L1+L2) | v1.7 |
| `primaryFrameworkId` | Main framework | v1.7 |
| `frameworkSelection` | Selection metadata | v1.7 |

---

### Outline
**Owner:** OutlineGenerator
**Spec:** `systems/OutlineGenerator/OutlineGenerator.md`
**Library:** `libraries/content/_outlines.library.md`

| Field | Description | Added |
|-------|-------------|-------|
| `id`, `name` | Core identity | v1.0 |
| `themeId`, `templateId` | Source references | v1.0 |
| `templateSnapshot` | Template drift protection | v1.2 |
| `contentTypeSnapshot` | Content type drift protection | v1.4 |
| `themeSnapshot` | Theme/truth drift protection | v1.4 |
| `truthStrategy` | Truth selection + sequence | v1.0 |
| `truthStrategy.*Ids` | Stable IDs parallel to indices | v1.4 |
| `sections[]` | Section outlines | v1.0 |
| `cognitiveTypeId` | Cognitive approach | v1.0 |
| `planDepth` | Detail level | v1.0 |
| `globalImageStyle`, `globalImageMood` | Visual defaults | v1.3 |
| `frameworkMapping` | Framework stage mapping | v1.4 |

**SectionOutline Fields:**

*Note: SectionOutline is a **composite planning artifact** spanning Layer 1+2 (frameworks), Layer 3 (content-type structure), and Layer 4 (paragraph structure). This is intentional - it bridges abstract frameworks to concrete generation.*

| Field | Description | Added |
|-------|-------------|-------|
| `type`, `plan`, `keyPoints[]` | Core planning | v1.0 |
| `truthsReferenced[]` | Truth indices | v1.0 |
| `truthsReferencedIds[]` | Stable IDs for validation | v1.4 |
| `truthPriority` | Driver vs support | v1.3 |
| `truthPriority.*Id` | Stable IDs for driver/support | v1.4 |
| `transitionOut` | Narrative bridging | v1.3 |
| `transformationStage` | Belief journey stage | v1.3 |
| `toneNotes` | Section-specific tone | v1.0 |
| `imageKeywords[]` | Visual search | v1.0 |
| `imageComposition` | Composition hints | v1.2 |
| `planLocked` | Protect manual edits | v1.3 |
| `frameworkStageId` | Framework stage | v1.4 |
| `frameworkPrompts[]` | Stage prompts | v1.4 |
| `frameworkLanguage` | Proprietary terms | v1.4 |
| `frameworkElements` | Required elements | v1.4 |
| `paragraphStructure` | PEARL/SAFE/CER | v1.4 |
| `expectedParagraphs` | Paragraph count | v1.4 |
| `paragraphBreakdown[]` | Per-paragraph plan | v1.4 |

---

### Template
**Owner:** TemplateFactory
**Spec:** `systems/TemplateFactory/TemplateFactory.md` (placeholder)
**Library:** `libraries/template/_templates.library.md`

| Field | Description | Added |
|-------|-------------|-------|
| `id`, `name` | Core identity | v1.0 |
| `contentType` | Content classification | v1.0 |
| `sections[]` | Section structure | v1.0 |
| `version` | Template version | v1.0 |
| `defaultCognitiveType` | Default cognitive approach | v1.0 |

---

## Brand System Schemas

### Brand
**Owner:** BrandManager (placeholder)
**Spec:** `systems/_BrandManager/BrandManager.md`
**Library:** `libraries/brand/_brands.library.md`

| Field | Description | Added |
|-------|-------------|-------|
| `id`, `name` | Core identity | v1.0 |
| `voiceId` | Reference to voice | v1.0 |
| `values[]` | Brand values | v1.0 |
| `philosophy` | Brand philosophy | v1.0 |
| `defaultToneId` | Default tone | v1.0 |

---

### Audience
**Owner:** BrandManager
**Spec:** `systems/_BrandManager/BrandManager.md`
**Library:** `libraries/brand/_audiences.library.md`

| Field | Description | Added |
|-------|-------------|-------|
| `id`, `name` | Core identity | v1.0 |
| `brandId` | Parent brand | v1.0 |
| `psychographics` | Psychological profile | v1.0 |
| `painPoints[]` | Challenges faced | v1.0 |
| `desiredOutcome` | What they want | v1.0 |
| `cognitiveLevel` | Complexity tolerance | v1.0 |
| `engagementTriggers` | Positive/negative triggers | v1.0 |

---

## Reference Data Schemas

### Framework (Layer 1 + 2)
**Owner:** ContentManager (human-curated)
**Spec:** `libraries/content/_frameworks.library.md`
**Type:** Reference data (read-only by AI)

| Field | Description |
|-------|-------------|
| `id`, `name` | Core identity |
| `type` | evidence-based or proprietary |
| `category` | skill_building, emotional_processing, etc. |
| `structure.stages[]` | Framework stages |
| `bestFor[]` | Content goals |
| `cognitiveLevels[]` | Audience fit |
| `pairsWellWith[]` | Complementary frameworks |

---

### Content-Type Framework (Layer 3)
**Owner:** ContentManager (human-curated)
**Spec:** `libraries/content/_content-type-frameworks.library.md`
**Type:** Reference data (read-only by AI)

| Field | Description |
|-------|-------------|
| `id`, `name` | Core identity |
| `contentType` | Which content type |
| `sectionSequence[]` | Ordered section types |
| `wordCountBySection` | Target word counts |
| `recommendedFrameworks[]` | Framework suggestions |

---

### Paragraph Structure (Layer 4)
**Owner:** ContentManager (human-curated)
**Spec:** `libraries/content/_paragraph-structures.library.md`
**Type:** Reference data (read-only by AI)

| Field | Description |
|-------|-------------|
| `id`, `name` | Core identity |
| `components[]` | Structure components |
| `bestForSections[]` | Section type fit |
| `cognitiveVariants[]` | Level-specific versions |

---

### Section Spec
**Owner:** ContentManager (human-curated)
**Spec:** `libraries/template/_section-specs.library.md`
**Type:** Reference data (read-only by AI)

| Field | Description |
|-------|-------------|
| `id`, `name` | Core identity |
| `purpose` | What section accomplishes |
| `requiredElements[]` | Must-have content |
| `optionalElements[]` | Nice-to-have content |
| `defaultParagraphStructure` | Recommended structure |

---

### Outcome
**Owner:** ContentManager (human-curated)
**Spec:** `libraries/content/_outcomes.library.md`
**Type:** Reference data (read-only by AI)

| Field | Description |
|-------|-------------|
| `id`, `name`, `category` | Core identity |
| `description` | What outcome means |
| `natashaPerspective` | Brand voice interpretation |
| `audienceLanguage` | How audience describes it |
| `measurementIndicators[]` | Success signals |

---

## AI Services Schemas

### ValidatedPrompt
**Owner:** PromptBuilder
**Spec:** `systems/_PromptBuilder/PromptBuilder.md`

| Field | Description |
|-------|-------------|
| `prompt` | Final prompt text |
| `tokens` | Token breakdown |
| `condensed` | Whether condensing applied |
| `condensingReport` | What was removed/summarized |

---

### CondensedResult
**Owner:** PromptCondenser
**Spec:** `systems/_PromptCondenser/PromptCondenser.md`

| Field | Description |
|-------|-------------|
| `prompt` | Condensed prompt text |
| `tokens` | Original/condensed counts |
| `report` | Removed/truncated/summarized |
| `success` | Whether prompt now fits |

---

### AlignmentReport
**Owner:** ContentAlignment
**Spec:** `systems/_ContentAlignment/ContentAlignment.md`

| Field | Description |
|-------|-------------|
| `overallScore` | 0-100 alignment score |
| `grade` | A-F letter grade |
| `categoryScores` | Score per category |
| `misalignments[]` | Specific issues |
| `suggestions[]` | How to fix |

---

## Cross-Schema Field Requests

When a module needs a new field on a schema it doesn't own:

1. **Document the need** in your module's Build Notes
2. **Open request** to schema owner (add to their Notes section)
3. **Owner reviews** and adds to their spec if approved
4. **Update this map** with new field and version

**Example:**
```
TemplateEngine needs `paragraphStructure` on SectionOutline
→ Request to OutlineGenerator (owns SectionOutline)
→ OutlineGenerator adds field in v1.4
→ Update this map
```

---

## Library Write Authority

From `_libraries-index.md`:

| Library | Write Owner | AI Writes? |
|---------|-------------|------------|
| themes | ThemeGenerator | Yes |
| outlines | OutlineGenerator | Yes |
| templates | TemplateFactory | Yes |
| brands | BrandManager | No (human) |
| audiences | BrandManager | No (human) |
| outcomes | ContentManager | No (human) |
| frameworks | ContentManager | No (human) |
| content-type-frameworks | ContentManager | No (human) |
| paragraph-structures | ContentManager | No (human) |
| section-specs | ContentManager | No (human) |
| cognitive-types | ContentManager | No (human) |
| voices | BrandManager | No (human) |
| tones | BrandManager | No (human) |

---

*Created: 2026-01-20*
*Status: Active*
