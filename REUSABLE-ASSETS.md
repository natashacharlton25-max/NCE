# REUSABLE-ASSETS.md

**Status:** Draft Complete – Awaiting Review
**Generated:** 2026-02-20
**Scope:** Quick scan of `c:\Users\NCE` for portable content, functions, and schemas

---

## 1. Reusable Content

### 1.1 — Therapeutic Framework JSON Library (PRODUCTION-READY)

**Location:** `LibraryJsonFilled/`
**Format:** JSON, Australian English
**Total:** 12 files, 30 frameworks (23 evidence-based + 7 proprietary)

| File | Contents | Entries |
|------|----------|---------|
| `index.json` | Master index linking all categories | 9 categories, 23 frameworks, 7 workbook type recommendations, 5 cognitive levels |
| `behaviour-change.json` | Stages of Change, COM-B, Implementation Intentions, Habit Loop | 4 frameworks, 3 combinations |
| `cognitive-work.json` | Cognitive Triangle (CBT), ABCDE Model | 2 frameworks, 1 combination |
| `emotional-processing.json` | RAIN, Window of Tolerance | 2 frameworks, 2 combinations |
| `goal-setting.json` | SMART Goals, WOOP | 2 frameworks, 3 combinations |
| `relationships.json` | Gottman Four Horsemen, NVC, Attachment Styles | 3 frameworks, 2 combinations |
| `recovery.json` | Relapse Prevention, HALT, WRAP | 3 frameworks, 2 combinations |
| `self-discovery.json` | Values Card Sort (24 values), Wheel of Life | 2 frameworks, 1 combination |
| `skill-building.json` | Kolb's Cycle, Dreyfus Model, Deliberate Practice | 3 frameworks, 2 combinations |
| `trauma-informed.json` | Polyvagal Theory, Herman's 3 Stages, Grounding Techniques | 3 frameworks, 2 combinations |
| `proprietary-frameworks.json` | Wardrobe Framework, Controlled vs Comfort Zone, Addition Not Subtraction, Choice as Safety, No Failure Only Signals, Resilience Before Processing, Small Steps Toward Joy | 7 proprietary frameworks, language substitution table |

**Portability:** Immediately usable. Can be served from Cloudflare KV or R2 with zero transformation.

---

### 1.2 — Brand Configuration Schemas (with worked JSON examples)

**Location:** `DOCS - StructureDefined/libraries/brand/`

| File | Contents | Entries |
|------|----------|---------|
| `_brands.library.md` | Brand identity, purpose, governance, content pillars | 1 schema, 2 examples, 6 validation rules, 1 SQLite DDL |
| `_voices.library.md` | Voice personality, writing style, vocabulary rules | 1 schema, 2 examples, 4 validation rules |
| `_tones.library.md` | 6 named tones (warm-professional, direct-urgent, etc.) | 1 schema, 6 tones, 2 combinations |
| `_audiences.library.md` | Demographics, psychographics, language patterns, sensitivities | 1 schema, 2 examples, 8 nested sub-schemas |

---

### 1.3 — Content Strategy Libraries

**Location:** `DOCS - StructureDefined/libraries/content/`

| File | Contents | Entries |
|------|----------|---------|
| `_frameworks.library.md` | Meta-schema for framework storage, 4-layer architecture | 1 master schema, 2 examples, selection algorithm |
| `_themes.library.md` | Theme schema with truths, transformation, scope system | 1 schema, 3 examples, 4 quality tiers |
| `_outcomes.library.md` | Transformation outcomes, belief shifts, brand perspective | 1 schema, 2 examples, 6 outcome categories |
| `_outlines.library.md` | Outline schema, truth strategy, section outlines | 1 schema, 2 examples, 4 sequence types |
| `_content-type-frameworks.library.md` | 8 workbook types with section/framework/word-count matrices | 8 types, 2 full examples |
| `_paragraph-structures.library.md` | Paragraph structures (PEARL, SAFE, PEEL, CER) | Multiple structures with cognitive-level variants |

---

### 1.4 — Template & Social Libraries

**Location:** `DOCS - StructureDefined/libraries/template/` and `libraries/social/`

| File | Contents | Entries |
|------|----------|---------|
| `_templates.library.md` | Document templates (blog, personal story, welcome email) | 1 schema, 3 templates |
| `_section-specs.library.md` | Section types (hook, body, CTA, story, etc.) | 1 schema, multiple section types |
| `_cognitive-types.library.md` | Narrative structures (problem-solution, origin story) | 1 schema, multiple types |
| `_social-templates.library.md` | Platform templates: IG carousel, LinkedIn, Twitter, Reels | 1 schema, 4 examples, 6 platforms |

---

### 1.5 — Prompt Templates

**Location:** `prompts/`
**Total:** 27 files

| File | Purpose |
|------|---------|
| `PASS0PROMPT.md` | Coverage/sufficiency assessment |
| `PASS1PROMPT.MD` | System grounding (SYSTEM.md + ADMIN.md) |
| `PASS2PROMPT.MD` | Subsystem pre-specs |
| `PASS3PROMPT.MD` | Full specification |
| `SPECPROMPT.MD` | Spec writing |
| `SUBSYSTEMPROMPT.MD` | Subsystem documentation |
| `SYSTEMDOCPROMPT.MD` | System-level documentation |
| `SYSTEMHARDENINGPROMPT.MD` | Hardening pass |
| `ADMINDOCPROMPT.MD` | Admin documentation |
| `INDEXPROMPT.MD` | Index generation |
| `PRE-SPEC-NOTES-TEMPLATE.md` | Pre-spec template |
| `PRE-SPEC-NOTES-CHECKLIST.md` | Pre-spec checklist |
| `PASSLIST.md` | Pass workflow summary |
| `SYS_folder/*.md` | 5 system-level sub-prompts |
| `SUBSYS_folder/*.md` | 8 subsystem-level sub-prompts |

---

## 2. Reusable Functions

> **Note:** This project is pre-implementation (documentation-first). No source code exists yet. The following are **documented function contracts** ready to implement.

### 2.1 — PromptBuilder (most detailed contract)

**Spec:** `NCEMPIRE/services/PromptBuilder/PromptBuilder.md`
**Planned runtime:** Node.js 20+, TypeScript 5.x

| Function | What it does | Dependencies |
|----------|-------------|--------------|
| `build(templateId, context, model)` | Full pipeline: load → inject → validate → condense → return `ValidatedPrompt` | PromptCondenser, FileManager, TokenManager |
| `buildThemePrompt(context)` | Assembles theme generation prompt | Brand, Audience, Outcome data |
| `buildOutlinePrompt(context)` | Assembles outline generation prompt | Theme, Template data |
| `buildSectionPrompt(context)` | Per-section content generation prompt | Outline, Framework data |
| `buildParagraphPrompt(context)` | Paragraph-level prompt with structure | ParagraphStructure data |
| `validate(prompt, model)` | Validates prompt fits token limits, condenses if needed | TokenManager, PromptCondenser |
| `willFit(prompt, model)` | Quick boolean token limit check | TokenManager |
| `countTokens(text, model?)` | Token count for text | Model-specific tokenizer |
| `measurePrompt(prompt)` | `TokenBreakdown` by section and priority | None |

**Workers note:** Compatible if using fetch-based AI SDKs. Token counting needs a Workers-compatible tokenizer.

---

### 2.2 — AICaller

**Spec:** `NCEMPIRE/ai/AICaller/AICaller.md`
**Providers:** Anthropic (primary), OpenAI (fallback), Google AI, Groq, OpenRouter, Ollama (future)

| Function | What it does | Dependencies |
|----------|-------------|--------------|
| `call(model, prompt, options)` | Route to provider, handle retry/backoff | APIKeyManager, RateLimiter, FailureHandler |
| `classifyError(error)` | Categorise as transient vs permanent | None |

**Workers note:** Anthropic and OpenAI SDKs both have Workers-compatible versions. Queue management would need Durable Objects or Queues.

---

### 2.3 — Other Documented Modules (contracts only, no implementation)

| Module | Location | Purpose |
|--------|----------|---------|
| Librarian | `NCEMPIRE/services/Librarian/` | SQLite query abstraction, cross-library integrity, caching |
| PromptCondenser | `NCEMPIRE/services/PromptCondenser/` | Priority-based prompt truncation |
| TokenManager | `NCEMPIRE/ai/TokenManager/` | Token counting and budget tracking |
| ContentValidator | `NCEMPIRE/content/ContentValidator/` | Validate content against brand/audience/framework |
| ThemeGenerator | `NCEMPIRE/template/ThemeGenerator/` | AI-driven theme generation from outcomes |
| OutlineGenerator | `NCEMPIRE/template/OutlineGenerator/` | AI-driven outline generation from theme + template |
| TemplateEngine | `NCEMPIRE/template/TemplateEngine/` | Section-by-section content generation |
| FailureClassifier | `NCEMPIRE/resilience/FailureClassifier/` | Error classification (levels 1–4) |
| RetryPolicyEngine | `NCEMPIRE/resilience/RetryPolicyEngine/` | Exponential backoff retry strategy |
| BrandManager | `NCEMPIRE/brand/` | CRUD on brands, voices, tones, audiences |
| FileManager | `NCEMPIRE/services/FileManager/` | File read/write with path validation |

---

## 3. Reusable Schemas

> All schemas are documented as JSON examples + SQLite DDL within markdown. No Zod or TypeScript files exist yet. Planned: Zod schemas at `/src/schemas/`.

### 3.1 — Brand System

| Schema | Spec Location | Key Fields |
|--------|---------------|------------|
| `Brand` | `_brands.library.md` | id, name, tagline, positioningStatement, mission, vision, voiceId, defaultToneId, values[], truthApprovalMode, contentPillars[], avoidedTopics[] |
| `Voice` | `_voices.library.md` | id, name, personality[3-7], writingStyle{sentenceLength, paragraphLength, punctuation, contractions, person}, vocabulary{preferred[], avoided[], clinical[]} |
| `Tone` | `_tones.library.md` | id, name, description, whenToUse[], characteristics[], examplePhrases[], avoid[] |
| `Audience` | `_audiences.library.md` | id, segmentName, demographics, psychographics{beliefs[], fears[], desires[], frustrations[]}, awarenessLevel, language{theySay[], theyDontSay[]}, objections, transformation{from[], to[]}, sensitivities |

**ID patterns:** `brand_{slug}`, `voice_{slug}`, `tone_{slug}`, `audience_{slug}`

---

### 3.2 — Content System

| Schema | Spec Location | Key Fields |
|--------|---------------|------------|
| `Theme` | `_themes.library.md` | id, name, scope{brand/audience/contentType/platform}, concept, message, angle, keywords[], truths[3-7], transformationFramework, temporal, quality |
| `Truth` | within themes | index, statement, application, dependsOn, status, reviewRequired |
| `Outcome` | `_outcomes.library.md` | id, name, definition, indicators[], beliefShifts[], desiredOutcomes, brandPerspective |
| `Outline` | `_outlines.library.md` | id, themeId, templateId, truthStrategy{sequenceType, selectedTruths[]}, sections[], frameworkMapping |
| `SectionOutline` | within outlines | index, type, plan, keyPoints[], truthsReferenced[], transformationStage, wordCountTarget |

---

### 3.3 — Template System

| Schema | Spec Location | Key Fields |
|--------|---------------|------------|
| `Template` | `_templates.library.md` | id, name, contentType, sections[]{type, order, required, config}, estimatedLength |
| `SectionSpec` | `_section-specs.library.md` | id, name, purpose, category, strength, constraints |
| `CognitiveType` | `_cognitive-types.library.md` | id, name, stages[], bestFor[], length |
| `SocialTemplate` | `_social-templates.library.md` | id, platform, postType, constraints, structure[], mediaRequirements |
| `Framework` | `_frameworks.library.md` | id, name, category, sourceType, structure{stages[], transitions[]}, cognitiveLevels[] |
| `ContentTypeFramework` | `_content-type-frameworks.library.md` | id, name, coreQuestion, sections[]{sectionSpecId, wordCountByLevel{1-5}} |

---

### 3.4 — System-Level Constants & Enums

**Location:** `DOCS - StructureDefined/reference/`

**Error codes** (`ERROR-CODES.md`): PATH_, FILE_, ENTRY_, API_, LOCK_, THEME_, VOICE_, BRAND_, TONE_, AUDIENCE_, OUTLINE_, TEMPLATE_, FRAMEWORK_, OUTCOME_, SOCIAL_ — each with level (1–4), description, recovery action, responsible module.

**Token limits** (`constants.md`):
- Claude 3.5 Sonnet: 200K in / 8K out
- Claude 3 Opus: 200K in / 4K out
- GPT-4 Turbo: 128K in / 4K out

**Token budgets by context type:** brand: 2000, audience: 1500, theme: 3000, framework: 1500, template: 1000, section: 500, paragraph: 500

**Shared enums:**
- Entry lifecycle: `draft | active | archived | flagged`
- Review status: `pending | approved | rejected`
- Truth approval: `strict | guided | trusted`
- Temporal: `evergreen | seasonal | date-specific | trending`
- Scope: `specific | universal`
- Sequence: `progressive | complementary | hierarchical | standalone`
- Transformation stage: `current_belief_validation | destabilization | insight | new_belief_installation | action`

---

## Cloudflare Workers Portability Notes

| Asset | Portable Now? | Adaptation Needed |
|-------|--------------|-------------------|
| JSON framework library (`LibraryJsonFilled/`) | Yes | None — serve from KV or R2 |
| Prompt templates (`prompts/`) | Yes | None — use as system prompts |
| Brand/content schemas | Needs implementation | Convert markdown specs → Zod schemas |
| PromptBuilder functions | Needs implementation | Write from contract; use fetch-based AI SDKs |
| SQLite (`better-sqlite3`) | Not compatible | Replace with D1 (Cloudflare SQLite) |
| File system paths (`/data/`, `/repos/`) | Not compatible | Map to KV, R2, or D1 |
| Token counting (tiktoken) | Not compatible | Use `@anthropic-ai/tokenizer` or approximation |

---

### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Are there any corrections or refinements required?
- May I proceed, or should I revise this first?
