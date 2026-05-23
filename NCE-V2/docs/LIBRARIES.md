# NCE-V2 Libraries — Canonical Index

**Status:** v2 canonical index (supersedes `C:/Users/NCE/DOCS - Outlines/_LIBRARIES.md` v1.0.0 — archived)
**Storage model:** D1 (text + JSON only, per `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0). Binary assets in R2 via the `assets/` system.
**Last updated:** 2026-05-23

---

## Purpose

This is the canonical index of D1 content libraries in NCE-V2. Each library is owned by one system (its write-owner); read access is mediated by `library/Librarian` from all other systems.

A library lives at `NCE-V2/specs/<owning-system>/libraries/<library-name>.library.md` (per Stage 2 output convention) and follows the shape defined in `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 (D1 schema: `_index` table, versioned `{items}` table, `{items}_latest` view, optional FTS5 + drafts).

If a library isn't listed here, it doesn't exist in NCE-V2. Adding one is a Stage 2 architecture decision; log in `NCE-V2/admin/PASS-DECISION-NOTES.md` and add to this index.

---

## Libraries by Owner System

### `brand/`

| Library | D1 Binding (planned) | Type | Write Owner | Used By | Notes |
|---|---|---|---|---|---|
| `brands` | `BRANDS_DB` | core | `brand/core/BrandManager` | most systems (read via Librarian) | Brand profiles, voice, values, truthApprovalMode |
| `audiences` | `AUDIENCES_DB` | core | `brand/audience/AudienceManager` | content/, template/ | Audience profiles for personalisation + targeting |
| `voices` | `VOICES_DB` | core | `brand/voice/BrandVoiceManager` | ai/, content/, social/, email/ | Brand voice definitions per brand |
| `tones` | `TONES_DB` | core | `brand/voice/BrandVoiceManager` | ai/, content/, social/, email/ | Tone modifiers (formal/casual/urgent etc.) |

### `ai/`

| Library | D1 Binding | Type | Write Owner | Used By | Notes |
|---|---|---|---|---|---|
| `prompts` | `PROMPTS_DB` | reference | `ai/AISystemManager` (or human-curated) | ai/, content/, template/ | Prompt templates for AI calls |
| `model-configs` | `MODEL_CONFIGS_DB` | reference | `ai/ModelManager` | ai/AICaller | Model parameters (temp, max tokens, etc.) per use case |

### `content/`

| Library | D1 Binding | Type | Write Owner | Used By | Notes |
|---|---|---|---|---|---|
| `themes` | `THEMES_DB` | generated | `content/ContentManager` (via `template/ThemeGenerator`) | template/, content/, social/, email/ | AI-generated content topics; reviewStatus required |
| `outlines` | `OUTLINES_DB` | generated | `content/ContentManager` (via `template/OutlineGenerator`) | template/, content/, social/, email/ | AI-generated content outlines from themes; reviewStatus required |
| **`frameworks`** | **`FRAMEWORKS_DB`** | **core** | **`content/ContentManager`** | **content/, template/, ai/, social/, email/** | **30 evidence-based + proprietary therapeutic frameworks (CBT, polyvagal, attachment theory, stages of change, COM-B, habit loop, etc.). Populated from `C:/Users/NCE/LibraryJsonFilled/` (12 JSON files, ~140KB). Each entry has professional + peer-led descriptions, background, structure (stages/components/steps), usage guidance, content application rules. Settled 2026-05-23 — owned by content/ because frameworks are construction inputs for the generation content/ does.** |

### `template/`

| Library | D1 Binding | Type | Write Owner | Used By | Notes |
|---|---|---|---|---|---|
| `templates` | `TEMPLATES_DB` | derived | `template/TemplateFactory` | template/TemplateEngine, content/LegalDocCreator, document-templates/ | Document templates |
| `section-specs` | `SECTION_SPECS_DB` | reference | human-curated | template/TemplateEngine, template/OutlineGenerator | Section type definitions |
| `content-types` | `CONTENT_TYPES_DB` | reference | human-curated | template/TemplateEngine, template/OutlineGenerator | Content type configs (workbook, blog-post, email, etc.) |
| `cognitive-types` | `COGNITIVE_TYPES_DB` | reference | human-curated | template/OutlineGenerator | Cognitive framework definitions (distinct from therapeutic frameworks in content/, which are methodology content; cognitive-types are structural patterns for reasoning) |

### `social/`

| Library | D1 Binding | Type | Write Owner | Used By | Notes |
|---|---|---|---|---|---|
| `social-templates` | `SOCIAL_TEMPLATES_DB` | reference | human-curated | social/SocialMediaManager | Per-platform per-format post templates |

### `email/`

| Library | D1 Binding | Type | Write Owner | Used By | Notes |
|---|---|---|---|---|---|
| `email-templates` | `EMAIL_TEMPLATES_DB` | reference | human-curated | email/EmailComposer | Email template definitions |

### `assets/`

| Library | D1 Binding | Type | Write Owner | Used By | Notes |
|---|---|---|---|---|---|
| `asset-catalog` | `ASSET_CATALOG_DB` | core | `assets/AssetCatalog` | most systems | Metadata for every asset (acquired, generated, uploaded). Asset *binaries* live in R2; this library indexes them by ID + holds metadata (source, license, attribution, dimensions, format). |

### (no libraries owned)

`access/`, `audit/`, `checks/`, `document-templates/`, `documents/`, `integrations/`, `library/` (it's the mediator, not an owner), `marks/`, `observability/`, `orchestration/`, `publishing/`, `renderers/`, `resilience/`, `review/`, `services/`, `state/`, `system/`, `verification/`, `versioning/`, `website/`

(These systems use libraries but don't write to any of their own.)

---

## Library Types (recap from LIBRARY-TEMPLATE.md v2.0.0)

| Type | Description | Can rebuild? | Staging area? |
|---|---|---|---|
| `core` | Human-curated source content | No | Yes |
| `derived` | Computed from other libraries | Yes | Optional |
| `generated` | AI-generated content | Yes | Yes (for review) |
| `reference` | System configuration | No | Yes |

---

## Rules

- **Single write-owner:** exactly one module writes to each library. All other systems read via `library/Librarian`.
- **No direct D1 access from outside `library/`** — every read goes through `library/Librarian`; every write goes through the owner module which uses `library/Writer`.
- **Storage substrate:** D1 only. Binary data goes to R2 via `assets/`; library entries that reference binaries hold the asset ID, not the bytes.
- **Schema:** every library follows the standard shape (`_index` + versioned `{items}` + `{items}_latest` view + optional `{items}_fts` + optional `{items}_drafts`) per LIBRARY-TEMPLATE v2.0.0.
- **Adding a new library:** architecture decision. Log in `PASS-DECISION-NOTES.md`, add to this index, then write the per-library `.library.md` spec under `NCE-V2/specs/<owner-system>/libraries/`.

---

## Notes

- Library names and D1 binding names listed here are **planned** — confirmed at Stage 2 when each library's `.library.md` spec is written. If a name shifts during spec writing (e.g. `frameworks` → `therapeutic-frameworks`), update this index in the same commit.
- v1 `_LIBRARIES.md` at `C:/Users/NCE/DOCS - Outlines/_LIBRARIES.md` is archived and bannered as superseded by this index + LIBRARY-TEMPLATE v2.0.0.
- `visual-brands` library from v1 `_LIBRARIES.md` is not listed here because the v1 `visual/` system was absorbed into `brand/` in v2 (per FileTree-v2.md). Brand visual data is part of brand library entries, not a separate library.
