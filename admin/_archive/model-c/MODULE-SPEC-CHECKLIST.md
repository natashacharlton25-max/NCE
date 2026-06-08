# Module Spec Checklist

> ⚠️ **LEGACY — SUPERSEDED (2026-06-07).**
> This monolithic checklist is **no longer the canonical tracker.** The live,
> canonical progress trackers are the **per-domain `Index.md` files** inside
> `NCEMPIRE/<domain>/Index.md` — one source of truth per domain, co-located with
> the specs they track.
>
> This file is retained as a **historical reference only**. It is out of date in
> several ways: it counts 85 modules (the real structure has ~242 across 31
> domains), and its spec paths (`systems/<Module>/…`) are wrong — specs actually
> live at `NCEMPIRE/<domain>/<Module>/`. Do **not** update this file for new
> progress; record progress in the relevant `NCEMPIRE/<domain>/Index.md` instead.

---

> Track progress on detailed specs for all 85 modules.
> Order: By category, simplest → most complex within each.

---

## Progress Summary

| Category | Total | Spec Written | Approved | Schema Ready |
|----------|-------|--------------|----------|--------------|
| Data Management | 5 | 2 | 2 | 0 |
| Template System | 2 | 1 | 1 | 0 |
| Renderers | 9 | 0 | 0 | 0 |
| System Services | 17 | 0 | 0 | 0 |
| Cost & Performance | 3 | 0 | 0 | 0 |
| AI Services | 6 | 1 | 1 | 0 |
| Content Quality | 4 | 0 | 0 | 0 |
| Content Creation | 8 | 1 | 1 | 0 |
| Visual Design | 13 | 0 | 0 | 0 |
| Brand Management | 3 | 0 | 0 | 0 |
| Social Media | 4 | 0 | 0 | 0 |
| Integrations | 5 | 0 | 0 | 0 |
| Core Architecture | 2 | 0 | 0 | 0 |
| Domain Managers | 4 | 0 | 0 | 0 |
| **TOTAL** | **85** | **5** | **5** | **0** |

**Build-Ready:** A module is ready for coding when: `[x] Spec` `[x] Approved` `[x] Schema`

---

## Schemas vs Content Libraries

> Understanding what goes where.
>
> **Analogy:** Library = Room full of books. Schema = Rules for what qualifies as a book (must have cover, author, ISBN).

### Schemas (Data Structure Rules)

Schemas define the **shape** of data and validation rules. Created after spec approval.

| Item | Location | Purpose |
|------|----------|---------|
| **Zod schemas** | `/src/schemas/{Module}.ts` | Runtime validation (source of truth) |
| **JSON schemas** | `/schemas/{module}.json` | Generated from Zod (for docs/external tools) |
| **Test fixtures** | `/fixtures/{module}/` | Example data for testing |

### Content Libraries (Actual Data)

Libraries contain the **actual content** that flows through the system. These are repos, not code.

| Library | Location | Contains |
|---------|----------|----------|
| **Prompts** | `/repos/prompts/` | Actual prompt templates (PromptBuilder uses) |
| **Brands** | `/repos/brands/` | Actual brand definitions (voice, values, truthApprovalMode) |
| **Audiences** | `/repos/audiences/` | Actual audience profiles |
| **Outcomes** | `/repos/outcomes/` | Actual outcomes library |
| **Section Specs** | `/repos/section-specs/` | Actual section type definitions |
| **Content Types** | `/repos/content-types/` | Actual content type configs |
| **Cognitive Types** | `/repos/cognitive-types/` | Actual cognitive frameworks |

### Relationship

```
Schema: "A Brand must have id, name, voice, truthApprovalMode fields"
     ↓ validates
Library: /repos/brands/mind-the-box.json (actual brand data)
```

---

## Database Access Pattern

> Convention for all module specs:

| Access Type | Method | Example |
|-------------|--------|---------|
| **Own database** | Direct via DatabaseHandler | TemplateEngine → templates.db, ThemeGenerator → themes.db |
| **Other repos** | Via FileManager | TemplateEngine → /repos/brands/, /repos/section-specs/ |
| **Discovery** | Via Librarian | Cross-system search, list, recommend, suggest |

---

## Spec Writing Guidelines

> Rules for keeping specs focused and consistent.

### Technology Separation

| Specs Describe | TECH-STACK.md Describes |
|----------------|-------------------------|
| **What** the module does | **How** to build it |
| Function signatures | Language (TypeScript 5.x) |
| Data schemas | Libraries (better-sqlite3, Zod) |
| Process flows | Runtime (Node.js 20+) |
| Error codes | Database engine (SQLite) |
| Boundaries | External APIs (Anthropic, Google) |

**Rule:** Specs stay technology-agnostic. Implementation details live in [TECH-STACK.md](TECH-STACK.md).

### Concern Ownership

Some concerns span multiple modules. Don't duplicate—reference the owning spec:

| Concern | Owner | Notes |
|---------|-------|-------|
| **Validation schemas** | Validator | Runtime checks, input/output validation |
| **Retry/backoff logic** | FailureHandler | Uses ERROR-CODES.md to decide recovery |
| **Logging patterns** | Logger | Format, levels, output destinations |
| **Prompt templates** | PromptBuilder | Stored in `/repos/prompts/` |
| **Error codes** | ERROR-CODES.md | Centralized library, modules reference codes |
| **Test fixtures** | Per-module build plan | Added after spec complete, before coding |

### What To Include in Specs

| Include | Don't Include |
|---------|---------------|
| Function signatures with params/returns | Library names (put in TECH-STACK) |
| SQLite DDL schemas | Retry counts/timing (put in FailureHandler) |
| Process flow (numbered steps) | Logging statements (put in Logger) |
| Error codes to emit | Validation implementation (put in Validator) |
| Dependencies (which modules needed) | Test cases (put in build plan) |
| Boundaries (what module cannot do) | Prompt text (put in /repos/prompts/) |
| Design rationale | Configuration values (put in config files) |

---

## 1. Data Management (Start Here - Foundation)

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 1 | Writer | [x] | [x] | [ ] | 0 | Spec at systems/Writer/Writer.md |
| 2 | FileManager | [x] | [x] | [ ] | 0 | Spec at systems/FileManager/FileManager.md |
| 3 | Archivist | [ ] | [ ] | [ ] | 0 | |
| 4 | CacheHandler | [ ] | [ ] | [ ] | 0 | |
| 5 | Librarian | [ ] | [ ] | [ ] | 0 | Discovery layer: search, list, recommend, suggest across repos and databases |

---

## 2. Template System (Orchestration)

> See [TEMPLATE-ARCHITECTURE.md](TEMPLATE-ARCHITECTURE.md) for design rationale.
> Templates = section assembly instructions (DATA in /repos/, not modules).
> These modules orchestrate the flow and create new templates.

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 6 | TemplateEngine | [x] | [x] | [ ] | 3 | v1.1.1 at systems/TemplateEngine/TemplateEngine.md |
| 7 | TemplateFactory | [ ] | [ ] | [ ] | 0 | Handles template writes, version archiving for drift handling |

---

## 3. Renderers (Output Generation)

> Each renderer maps section types to page layouts for its output format.
> Receives filled sections from TemplateEngine, produces final documents.

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 8 | CanvaRenderer | [ ] | [ ] | [ ] | 0 | Outputs via CanvaIntegration |
| 9 | GoogleDocsRenderer | [ ] | [ ] | [ ] | 0 | Outputs via GoogleIntegration |
| 10 | GoogleSheetsRenderer | [ ] | [ ] | [ ] | 0 | Outputs via GoogleIntegration |
| 11 | GoogleSlidesRenderer | [ ] | [ ] | [ ] | 0 | Outputs via GoogleIntegration |
| 12 | ImageRenderer | [ ] | [ ] | [ ] | 0 | PNG/JPG via Sharp/Canvas |
| 13 | PDFRenderer | [ ] | [ ] | [ ] | 0 | PDF via PDFKit |
| 14 | DOCXRenderer | [ ] | [ ] | [ ] | 0 | Word docs via docx |
| 15 | MarkdownRenderer | [ ] | [ ] | [ ] | 0 | Markdown files |
| 16 | HTMLRenderer | [ ] | [ ] | [ ] | 0 | HTML files |

---

## 4. System Services (Infrastructure)

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 17 | Logger | [ ] | [ ] | [ ] | 0 | |
| 18 | APIKeyManager | [ ] | [ ] | [ ] | 0 | |
| 19 | FeatureFlags | [ ] | [ ] | [ ] | 0 | |
| 20 | AuthHandler | [ ] | [ ] | [ ] | 0 | |
| 21 | Notification | [ ] | [ ] | [ ] | 0 | |
| 22 | RateLimiter | [ ] | [ ] | [ ] | 0 | |
| 23 | RequestHandler | [ ] | [ ] | [ ] | 0 | |
| 24 | StatusHandler | [ ] | [ ] | [ ] | 0 | |
| 25 | QueueHandler | [ ] | [ ] | [ ] | 0 | |
| 26 | Scheduler | [ ] | [ ] | [ ] | 0 | |
| 27 | BatchProcessor | [ ] | [ ] | [ ] | 0 | |
| 28 | TestHandler | [ ] | [ ] | [ ] | 0 | |
| 29 | MigrationHandler | [ ] | [ ] | [ ] | 0 | |
| 30 | PythonRunner | [ ] | [ ] | [ ] | 0 | |
| 31 | ResearchTools | [ ] | [ ] | [ ] | 0 | |
| 32 | PolicyEngine | [ ] | [ ] | [ ] | 0 | |
| 33 | FailureHandler | [ ] | [ ] | [ ] | 0 | Handles template drift recovery |

---

## 5. Cost & Performance

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 34 | CostTracker | [ ] | [ ] | [ ] | 0 | |
| 35 | AnalyticsHandler | [ ] | [ ] | [ ] | 0 | |
| 36 | CostBudgetManager | [ ] | [ ] | [ ] | 0 | |

---

## 6. AI Services

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 37 | TokenManager | [ ] | [ ] | [ ] | 0 | |
| 38 | ModelManager | [ ] | [ ] | [ ] | 0 | |
| 39 | PromptBuilder | [ ] | [ ] | [ ] | 0 | Uses /repos/prompts/ |
| 40 | AICaller | [ ] | [ ] | [ ] | 0 | |
| 41 | AISystemManager | [ ] | [ ] | [ ] | 0 | |
| 42 | ThemeGenerator | [x] | [x] | [ ] | 7 | v1.7.0 at systems/ThemeGenerator/ThemeGenerator.md |

---

## 7. Content Quality

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 43 | Parsers | [ ] | [ ] | [ ] | 0 | |
| 44 | LanguageCheck | [ ] | [ ] | [ ] | 0 | |
| 45 | ContentTextAssessor | [ ] | [ ] | [ ] | 0 | |
| 46 | ContentAlignment | [ ] | [ ] | [ ] | 0 | |

---

## 8. Content Creation

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 47 | OutlineGenerator | [x] | [x] | [ ] | 4 | v1.3.1 at systems/OutlineGenerator/OutlineGenerator.md |
| 48 | Builders | [ ] | [ ] | [ ] | 0 | Document/Email/Social builders |
| 49 | Content | [ ] | [ ] | [ ] | 0 | |
| 50 | WebsiteContent | [ ] | [ ] | [ ] | 0 | |
| 51 | Email | [ ] | [ ] | [ ] | 0 | Uses /repos/email-templates/ |
| 52 | EmailLit | [ ] | [ ] | [ ] | 0 | |
| 53 | LegalDocCreator | [ ] | [ ] | [ ] | 0 | Uses /repos/templates/ |
| 54 | PDFMaker | [ ] | [ ] | [ ] | 0 | |

---

## 9. Visual Design

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 55 | ColourMix | [ ] | [ ] | [ ] | 0 | Pure functions |
| 56 | ColourMaker | [ ] | [ ] | [ ] | 0 | |
| 57 | ColourToken | [ ] | [ ] | [ ] | 0 | |
| 58 | FontManager | [ ] | [ ] | [ ] | 0 | |
| 59 | Typography | [ ] | [ ] | [ ] | 0 | |
| 60 | SVGDrawer | [ ] | [ ] | [ ] | 0 | |
| 61 | ImageBrowser | [ ] | [ ] | [ ] | 0 | |
| 62 | ImageTemplate | [ ] | [ ] | [ ] | 0 | |
| 63 | ImageCollections | [ ] | [ ] | [ ] | 0 | |
| 64 | AIImageCreation | [ ] | [ ] | [ ] | 0 | |
| 65 | LogoMaker | [ ] | [ ] | [ ] | 0 | |
| 66 | VisualValueAlignment | [ ] | [ ] | [ ] | 0 | |
| 67 | VisualBrand | [ ] | [ ] | [ ] | 0 | |

---

## 10. Brand Management

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 68 | AudienceManager | [ ] | [ ] | [ ] | 0 | |
| 69 | BrandManager | [ ] | [ ] | [ ] | 0 | Needs truthApprovalMode (strict/guided/trusted) |
| 70 | BrandTracker | [ ] | [ ] | [ ] | 0 | |

---

## 11. Social Media

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 71 | SocialMediaCollator | [ ] | [ ] | [ ] | 0 | |
| 72 | SocialMediaPlanner | [ ] | [ ] | [ ] | 0 | |
| 73 | SocialMediaManager | [ ] | [ ] | [ ] | 0 | Uses /repos/social-templates/ |
| 74 | SocialMediaAnalytics | [ ] | [ ] | [ ] | 0 | |

---

## 12. Integrations

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 75 | WebScraper | [ ] | [ ] | [ ] | 0 | |
| 76 | AIWebSearch | [ ] | [ ] | [ ] | 0 | |
| 77 | CanvaIntegration | [ ] | [ ] | [ ] | 0 | API wrapper for Canva Connect |
| 78 | GoogleIntegration | [ ] | [ ] | [ ] | 0 | API wrapper for Google Workspace |
| 79 | YouTubeAPI | [ ] | [ ] | [ ] | 0 | |

---

## 13. Core Architecture (Last - Depends on Everything)

| # | Module | Spec | Approved | Schema | Revisions | Notes |
|---|--------|------|----------|--------|-----------|-------|
| 80 | GlobalRules | [ ] | [ ] | [ ] | 0 | |
| 81 | Orchestrator | [ ] | [ ] | [ ] | 0 | Uses activeOn filter for trending themes |

---

## 14. Domain Managers (After Module Specs)

| # | Manager | Spec | Approved | Schema | Revisions | Notes |
|---|---------|------|----------|--------|-----------|-------|
| 82 | TypographyManager | [ ] | [ ] | [ ] | 0 | |
| 83 | VisualManager | [ ] | [ ] | [ ] | 0 | |
| 84 | ContentManager | [ ] | [ ] | [ ] | 0 | Shows "Verified" badge for humanReviewed themes |
| 85 | ExportManager | [ ] | [ ] | [ ] | 0 | |

---

## Pending Revisions

| Module | Revision Needed | Notes |
|--------|-----------------|-------|
| *None* | — | — |

---

## Change Log

| Date | Module | Change | By |
|------|--------|--------|-----|
| 2026-01-18 | Writer | Spec created, awaiting approval | Claude |
| 2026-01-18 | Writer | Approved v1.2 - added dryRun to options, locking to copy() | User |
| 2026-01-18 | FileManager | Spec v1.1 created, awaiting approval | Claude |
| 2026-01-18 | FileManager | Approved v1.1 - exists options, validate output clarified | User |
| 2026-01-18 | Templates | Split into 8 specialized modules (6 local + 2 external orchestration) - awaiting approval | Claude |
| 2026-01-19 | Templates | Restructured: Templates are DATA (repos), not modules. Added TemplateEngine, TemplateFactory, 9 Renderers | User/Claude |
| 2026-01-19 | TemplateEngine | Spec v1.0.2 created, awaiting approval | Claude |
| 2026-01-19 | ThemeGenerator | Added as new module #42 in AI Services. Spec v1.0.0 created | Claude |
| 2026-01-19 | TemplateEngine | Approved v1.0.2 | User |
| 2026-01-19 | ThemeGenerator | v1.2.0 - SQLite storage, 4-dimension scope, rating, temporal, partial themes | Claude |
| 2026-01-19 | OutlineGenerator | Added as new module #47 in Content Creation. Pre-generates section outlines from theme + template. | User/Claude |
| 2026-01-19 | ThemeGenerator | Approved v1.2.1 | User |
| 2026-01-19 | TemplateEngine | v1.1.0 - SQLite storage, outlineId support - awaiting approval | Claude |
| 2026-01-19 | TemplateEngine | v1.1.1 - Added Template type definition, clarified TemplateFactory responsibility | Claude |
| 2026-01-19 | Checklist | Added Database Access Pattern convention | User/Claude |
| 2026-01-19 | TemplateEngine | Approved v1.1.1 | User |
| 2026-01-19 | OutlineGenerator | Spec v1.0.0 created - SQLite, planDepth scaling, truthsReferenced | Claude |
| 2026-01-19 | ThemeGenerator | v1.3.0 - Added Five Truths (continuity anchors for multi-content campaigns) | Claude |
| 2026-01-19 | ThemeGenerator | v1.4.0 - Truths 3-7 range (min 3, target 5, max 7), cache TTL 48h+, hybrid truth model | Claude |
| 2026-01-19 | OutlineGenerator | v1.1.0 - Added sequenceType, hybrid truth model (selectedTruths, localTruths, primaryTruth) | Claude |
| 2026-01-19 | ThemeGenerator | v1.5.0 - Added target_outcomes, angle governance, truth governance, Outcomes Library integration | Claude |
| 2026-01-19 | ERROR-CODES | v1.4.0 - Added GOVERNANCE_ and OUTCOME_ error categories | Claude |
| 2026-01-19 | BrandManager | Added note: needs truthApprovalMode (strict/guided/trusted) | Claude |
| 2026-01-19 | ThemeGenerator | Approved v1.5.0 | User |
| 2026-01-19 | OutlineGenerator | Approved v1.1.0 | User |
| 2026-01-19 | ThemeGenerator | v1.6.0 - Full transformationFramework, expanded angleMetadata, quality tiers, processing flow, Outcomes Library integration | Claude |
| 2026-01-19 | ThemeGenerator | v1.7.0 - Added truth dependsOn, visual keywords, outcome cohesion, stale themes reporting | Claude |
| 2026-01-19 | ThemeGenerator | Approved v1.7.0 | User |
| 2026-01-19 | ERROR-CODES | v1.5.0 - Added OUTCOME_DIVERGENT, THEME_VISUAL_KEYWORDS_INSUFFICIENT, OUTLINE_TRUTH_DEPENDENCY_VIOLATION | Claude |
| 2026-01-19 | OutlineGenerator | v1.2.0 - regenerateSection(), templateSnapshot, imageComposition, needsRefinement, extensionMode, tone merging, cognitive type compat, truth dependency enforcement, transformationFramework pass-through | Claude |
| 2026-01-19 | ERROR-CODES | v1.6.0 - Expanded OUTLINE_ category with drift detection, section regeneration, and validation error codes | Claude |
| 2026-01-19 | OutlineGenerator | v1.3.0 - transitionOut, transformationStage, truthPriority, planLocked, globalImageStyle/globalImageMood, template drift handling strategy | Claude |
| 2026-01-19 | OutlineGenerator | v1.3.1 - Template drift architecture: Validator detects, FailureHandler recovers (use_archived_template/migrate_outline/generate_fresh) | Claude |
| 2026-01-19 | FailureHandler | Added note: handles OUTLINE_TEMPLATE_DRIFT_DETECTED recovery based on severity | Claude |
| 2026-01-19 | TemplateFactory | Added note: must archive previous version when template structure changes | Claude |
| 2026-01-19 | OutlineGenerator | Approved v1.3.1 | User |
| 2026-01-19 | ERROR-CODES | v1.7.0 - Added TRANSFORMATION_ category, OUTCOME_LIMIT_EXCEEDED, theme validation errors, GOVERNANCE_ANGLE_CONTRADICTS_VALUES | Claude |
| 2026-01-19 | Checklist | Added Spec Writing Guidelines section - technology separation, concern ownership | Claude |
| 2026-01-19 | TECH-STACK | Created TECH-STACK.md - centralized technology choices | Claude |
| 2026-01-19 | All Modules | Created 80 placeholder spec folders with _ prefix | Claude |
| 2026-01-19 | Checklist | Added Schema column, Schemas vs Content Libraries section | Claude |

---

*Created: 2026-01-18*
*Updated: 2026-01-19 - Added Schema tracking, Schemas vs Content Libraries*
