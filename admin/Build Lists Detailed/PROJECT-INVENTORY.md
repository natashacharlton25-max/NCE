# BrandKit Ecosystem - Project Inventory

> All projects and folders in `C:\Users\Business\` related to the BrandKit system.
> Updated: 2026-01-16 (Phase 8 Complete - Export System)
>
> **Quick Links:** [DOC-INDEX.md](../DOC-INDEX.md) | [BUILD-CHECKLIST.md](BUILD-CHECKLIST.md) | [INTEGRATION-TESTS.md](INTEGRATION-TESTS.md)

---

## Core Projects (5)

| Folder | Purpose | Status |
|--------|---------|--------|
| `BrandKit/` | Schema definitions - what a brand contains. Houses `_system/` with all section schemas. | Active |
| `BrandKit AIGen/` | AI configuration - how to generate content. Section configs, generation order, domain detection. | Active |
| `BrandLib/` | Shared libraries - reusable content patterns (tone, vocabulary, CTAs, etc.) by domain. | Active |
| `BrandKit Workflow/` | Orchestrator app - the main SvelteKit application that coordinates everything. | In Development |
| `BrandData/` | Output folder - where individual brand folders are created and stored. | Active (empty) |

---

## Utility Apps (6 Current + 2 Planned)

| Folder | Purpose | Status |
|--------|---------|--------|
| `FolderCreator/` | Creates brand folder structures in BrandData. CLI + library. | Complete |
| `APIKeyManager/` | Secure storage for API keys (Canva, Unsplash, etc.). CLI + library. | Complete |
| `ColourToken/` or `ColorTokenGenerator/` | Interactive colour palette builder for visual sections. | Exists (separate) |
| `TypographyBuilder/` | Font selection tool with Google Fonts integration. | Planned |
| `LogoMaker/` | Generate logo variants and specifications. | Planned |
| `ImageBrowser/` | Search Unsplash/Pexels for brand photography. | Planned |

### Future Utility Apps (Planned)

| App | Purpose | Status |
|-----|---------|--------|
| `AI Legal App/` | AI-assisted creation of legal templates with human review | Planned |
| `Librarian/` | Smart library management with learning, suggestions, entry promotion | Planned |

---

## Existing Code for Phase 7 (Visual Pathway)

These existing projects contain code to port/reuse for Phase 7:

| Project | Location | Reusable Code |
|---------|----------|---------------|
| **ColorTokenGenerator** | `C:\Users\Business\ColorTokenGenerator\` | OKLCH color scales, color theory harmonies, theme generation |
| **Empire v3.1** | `C:\Users\Business\Empire v3.1\` | CSS token parser, brand file system utilities |
| **Empire v4.1** | `C:\Users\Business\Empire v4.1\` | Design tokens, brand schema, 1,512 Phosphor icons |
| **Website v2.36** | `C:\Users\Business\Website v2.36\` | Same ThemeTokenGen scripts as ColorTokenGenerator |

### Key Files to Port

| Source File | Purpose | Destination |
|-------------|---------|-------------|
| `Empire v3.1/src/utils/colorTokenParser.js` | CSS color/font extraction | `lib/server/css-token-parser.ts` |
| `ColorTokenGenerator/ThemeTokenGen/simple-theme-gen.js` | OKLCH color scale generation | ColourToken App |
| `Empire v4.1/shared/Icons/` | 1,512 Phosphor SVG icons | Copy to shared location |

### External APIs for Phase 7

| API | Key Location | Purpose |
|-----|--------------|---------|
| Colour Pizza API | `.env` | Get colour names, palette suggestions |
| Background Remover API | `.env` | Auto-generate transparent logo variants |
| Google Fonts API | `.env` | Font metadata for TypographyBuilder |
| Unsplash/Pexels API | `.env` | Stock photo search for ImageBrowser |

---

## Folder Structure Detail

### BrandKit (`C:\Users\Business\BrandKit\`)
```
BrandKit/
├── _system/                    # Section schema definitions
│   ├── core/                   # Core brand sections
│   │   ├── identity/default/   # Brand name, tagline
│   │   ├── purpose/default/    # Mission, vision
│   │   └── contact/default/    # Contact details
│   ├── legal/                  # Legal sections
│   │   ├── core/default/       # Basic legal entity (moved from core/legal)
│   │   ├── entity/default/     # Detailed entity info
│   │   ├── compliance/default/ # Compliance requirements
│   │   └── disclaimers/default/# Legal disclaimers
│   ├── voice/                  # Voice & tone sections
│   ├── audience/               # Audience definition sections
│   ├── content/                # Content strategy sections
│   ├── visual/                 # Visual identity sections
│   ├── products/               # Product/service sections
│   ├── communication/          # Communication guidelines
│   ├── operations/             # Operational sections
│   └── ... (other categories)
└── .claude/                    # Claude Code settings
```

### BrandKit AIGen (`C:\Users\Business\BrandKit AIGen\`)
```
BrandKit AIGen/
├── _config/
│   ├── generation-order.json   # Section generation sequence & dependencies
│   └── domain-detection.json   # Domain auto-detection keywords
├── sections/                   # Per-section AI generation configs
│   ├── core--identity.json
│   ├── legal--core.json        # Renamed from core--legal.json
│   ├── legal--entity.json
│   └── ... (50 section configs)
├── _DOCS/                      # Documentation
│   └── ai instructions/        # AI generation planning docs
└── prompts/                    # (planned) Prompt templates
```

### BrandLib (`C:\Users\Business\BrandLib\`)
```
BrandLib/
├── _config/
│   ├── domains.json            # Domain definitions
│   └── field-mappings.json     # Field-to-library mappings
├── universal/                  # Domain-agnostic libraries
│   ├── tone.json
│   ├── emoji.json
│   ├── cta-patterns.json
│   └── ...
├── therapeutic/                # Therapeutic domain libraries
│   ├── vocabulary.json
│   ├── client-outcomes.json
│   └── ...
└── ecommerce/                  # (planned) Ecommerce libraries
```

### BrandKit Workflow (`C:\Users\Business\BrandKit Workflow\`) - Phase 8 Complete
```
BrandKit Workflow/
├── _config/
│   ├── global.json             # Central config with all paths
│   ├── section-models.json     # Section-to-model AI mapping
│   ├── schemas/
│   │   ├── global.schema.json          # Config validation schema
│   │   ├── domain-detection.schema.json # Domain detection config schema
│   │   └── domains.schema.json         # Domains config schema
│   ├── paths.json              # DEPRECATED - use global.json
│   ├── settings.json           # Workflow settings
│   └── workflow-steps.json     # Workflow step definitions
├── DOCS/
│   ├── DOC-INDEX.md            # READ FIRST - documentation guide
│   ├── Build Plans/            # PHASE-1-PLAN.md through PHASE-10-PLAN.md
│   ├── Build Lists Detailed/   # BUILD-CHECKLIST.md, PROJECT-INVENTORY.md, INTEGRATION-TESTS.md, LIBRARIAN-APP.md
│   ├── Future Builds Plans/    # PLAN-OVERVIEW.md, future enhancements
│   └── EXTENSIBILITY-GUIDE.md  # How to extend the system
├── lib/
│   ├── server/                 # Server-side utilities
│   │   ├── index.ts            # Library exports
│   │   ├── brand-status.ts     # 15 brand status functions (Phase 1.5)
│   │   ├── upload-handler.ts   # 20+ upload handling functions (Phase 2.1)
│   │   ├── website-fetcher.ts  # 15+ website fetching functions (Phase 2.2)
│   │   ├── template-generator.ts # Template generation (Phase 2.4)
│   │   ├── domain-detector.ts  # Domain detection + selection (Phase 3)
│   │   ├── prompt-builder.ts   # AI prompt assembly (Phase 4.1)
│   │   ├── section-utils.ts    # Section save/load/approve (Phase 4.2)
│   │   ├── ai-client.ts        # Multi-provider AI client (Phase 4.3)
│   │   ├── core-sections.ts    # Core sections handler (Phase 4.3b)
│   │   ├── section-router.ts   # Section routing + dependencies (Phase 4.4)
│   │   ├── response-processor.ts # AI response processing (Phase 4.5)
│   │   ├── library-matcher.ts  # Library matching + weighted confidence (Phase 5A.1 + 5B.3)
│   │   ├── library-linker.ts   # Save link selections (Phase 5A.2)
│   │   ├── library-browser.ts  # Browse library (Phase 5A.3)
│   │   ├── library-creator.ts  # Create entries with weight/source (Phase 5B.1)
│   │   ├── library-editor.ts   # Edit entries, update weights (Phase 5B.2)
│   │   ├── companies-house.ts  # Companies House API integration (Phase 6.1)
│   │   ├── legal-templates.ts  # Legal templates + placeholders (Phase 6.2/6.4)
│   │   ├── disclaimers.ts      # Domain-specific disclaimers (Phase 6.3)
│   │   ├── css-token-parser.ts # CSS/SCSS/Design Token parsing (Phase 7A)
│   │   ├── visual-router.ts    # Visual section routing (Phase 7B.1)
│   │   ├── logo-handler.ts     # Logo upload + variants (Phase 7B.2)
│   │   ├── photography-handler.ts # Photography styles (Phase 7B.3)
│   │   ├── visual-guidelines-generator.ts # Generate guidelines (Phase 7B.4)
│   │   ├── colourtoken-integration.ts # ColourToken app integration (Phase 7C.1)
│   │   ├── typographybuilder-integration.ts # TypographyBuilder integration (Phase 7C.2)
│   │   ├── logomaker-integration.ts # LogoMaker integration (Phase 7C.3)
│   │   ├── imagebrowser-integration.ts # ImageBrowser integration (Phase 7C.4)
│   │   ├── export-router.ts    # Export routing + orchestration (Phase 8.1)
│   │   ├── export-manager.ts   # High-level export API (Phase 8.6)
│   │   ├── exporters/          # Export generators (Phase 8)
│   │   │   ├── index.ts        # Barrel exports
│   │   │   ├── json-exporter.ts # JSON export generation (Phase 8.2)
│   │   │   ├── markdown-exporter.ts # Markdown export generation (Phase 8.3)
│   │   │   ├── pdf-exporter.ts # PDF/HTML export generation (Phase 8.4)
│   │   │   └── prompt-pack-exporter.ts # AI prompt pack generation (Phase 8.5)
│   │   └── types/
│   │       ├── brand.ts        # Brand type definitions
│   │       ├── upload.ts       # Upload type definitions (Phase 2.1)
│   │       ├── website.ts      # Website type definitions (Phase 2.2)
│   │       ├── template.ts     # Template type definitions (Phase 2.4)
│   │       ├── domain.ts       # Domain type definitions (Phase 3)
│   │       ├── prompt.ts       # Prompt type definitions (Phase 4.1)
│   │       ├── section.ts      # Section type definitions (Phase 4.2)
│   │       ├── ai-client.ts    # AI client type definitions (Phase 4.3)
│   │       ├── core-sections.ts # Core sections handler types (Phase 4.3b)
│   │       ├── section-router.ts # Section router types (Phase 4.4)
│   │       ├── response-processor.ts # Response processor types (Phase 4.5)
│   │       ├── library.ts      # Library type definitions + weighting (Phase 5)
│   │       ├── legal.ts        # Legal type definitions (Phase 6)
│   │       ├── visual.ts       # Visual type definitions (Phase 7)
│   │       └── export.ts       # Export type definitions (Phase 8)
│   └── parsers/                # File parsers (Phase 2.3)
│       ├── index.ts            # Parser registry and routing
│       ├── pdf-parser.ts       # PDF text extraction
│       ├── docx-parser.ts      # DOCX parsing via mammoth
│       ├── json-parser.ts      # JSON to text conversion
│       ├── csv-parser.ts       # CSV parsing
│       ├── text-parser.ts      # Text/MD passthrough
│       └── types/
│           └── parser.ts       # Parser type definitions
├── scripts/                    # Dev/testing scripts
│   ├── test-ai.ts              # AI model testing & comparison tool
│   ├── test-templates.ts       # Template generator tests
│   ├── test-core-sections.ts   # Core sections handler tests
│   ├── test-section-router.ts  # Section router tests
│   ├── test-response-processor.ts # Response processor tests
│   ├── test-library-matcher.ts # Library matching tests (Phase 5A - 21 tests)
│   ├── test-library-creator.ts # Library creator/editor tests (Phase 5B - 28 tests)
│   ├── test-library-improvements.ts # Phase 5+ improvements (22 tests)
│   ├── test-legal.ts           # Legal pathway tests (Phase 6 - 54 tests)
│   ├── test-css-parser.ts      # CSS token parser tests (Phase 7A - 104 tests)
│   ├── test-visual.ts          # Visual router/handler tests (Phase 7B - 61 tests)
│   ├── test-integrations.ts    # Utility app integration tests (Phase 7C - 65 tests)
│   └── batch-generate.ts       # Batch section generation script
├── package.json                # npm project with ts-node + Phase 2/3 deps
├── tsconfig.json               # TypeScript config
├── .env                        # API keys (AI providers, COMPANIES_HOUSE_API_KEY, image APIs)
└── node_modules/
```

### BrandData (`C:\Users\Business\BrandData\`) - Has test-brand
```
BrandData/
└── test-brand/                 # Created by FolderCreator test
    ├── _config/
    │   ├── brand.json          # {id, name, domain, region, status}
    │   └── business.json       # Companies House data, VAT, registration (Phase 6)
    ├── _meta/
    │   └── progress.json       # 49 sections tracked
    ├── brandkit/               # Approved/complete sections
    │   ├── core/
    │   ├── legal/
    │   ├── voice/
    │   └── ... (12 categories)
    ├── draft/                  # Work-in-progress sections
    ├── context/
    │   ├── sources.json        # Input source manifest
    │   ├── website/            # Fetched website content
    │   ├── documents/          # Uploaded documents
    │   └── visual/             # Uploaded visual assets (logos, colours, etc.)
    ├── legal/                  # Generated legal content (Phase 6)
    │   ├── cookie-policy.json
    │   ├── privacy-policy.json
    │   ├── terms.json
    │   ├── disclaimers.json
    │   └── footer.json
    └── libraries/              # Brand-specific library entries
```

### FolderCreator (`C:\Users\Business\FolderCreator\`) - Complete
```
FolderCreator/
├── src/
│   ├── index.ts                # Library exports
│   ├── cli.ts                  # CLI entry point
│   ├── create-brand.ts         # Brand folder creation logic
│   ├── utils/
│   │   ├── paths.ts            # Path resolution, slugify
│   │   ├── json-utils.ts       # JSON read/write helpers
│   │   └── validators.ts       # Input validation (name, region, domain)
│   └── types/
│       └── index.ts            # TypeScript interfaces
├── _config/
│   └── templates.json          # Folder structure templates
├── package.json                # Dependencies: commander, ts-node
├── tsconfig.json
└── node_modules/
```

### APIKeyManager (`C:\Users\Business\APIKeyManager\`) - Complete
```
APIKeyManager/
├── src/
│   ├── index.ts                # Library exports
│   ├── cli.ts                  # CLI entry point (set, get, list, delete, has, verify)
│   ├── get-key.ts              # Key retrieval functions
│   ├── set-key.ts              # Key storage functions
│   ├── list-keys.ts            # List services and keys
│   ├── delete-key.ts           # Key deletion functions
│   ├── services.ts             # Service validation
│   └── utils/
│       ├── encryption.ts       # AES-256-GCM encryption
│       └── storage.ts          # Encrypted file storage
├── _config/
│   ├── services.json           # 8 supported services (anthropic, canva, unsplash, etc.)
│   └── .keys.encrypted         # Encrypted key storage (gitignored)
├── package.json                # Dependencies: commander, ts-node
├── tsconfig.json
├── .gitignore                  # Excludes encrypted keys
└── node_modules/
```

---

## Key Configuration Files

| File | Location | Purpose |
|------|----------|---------|
| `global.json` | `BrandKit Workflow/_config/` | Central config with all project paths |
| `generation-order.json` | `BrandKit AIGen/_config/` | Section generation sequence |
| `domains.json` | `BrandLib/_config/` | Domain definitions for libraries |
| `field-mappings.json` | `BrandLib/_config/` | Field-to-library mappings |
| `brand.json` | `BrandData/{brand}/_config/` | Per-brand metadata |
| `progress.json` | `BrandData/{brand}/_meta/` | Per-brand section status |

---

## Related/Adjacent Projects

| Folder | Relationship | Notes |
|--------|--------------|-------|
| `Content Pipeline/` | Legacy | Older brand system, separate from BrandKit |
| `CanvaAPP/` | Integration | Canva integration (Phase 9) |
| `ColorTokenGenerator/` | Utility | May be used for ColourToken integration |

---

## Build Progress

| Project | Phase | Status |
|---------|-------|--------|
| BrandKit | - | Complete (schemas exist) |
| BrandKit AIGen | - | Partial (50 section configs, some pending) |
| BrandLib | - | Complete (libraries exist, field-mappings.json fixed) |
| BrandKit Workflow | Phase 8 Complete | Phases 1-8 done (Export System added) |
| BrandData | - | Ready (test-brand with uploads, website fetch, parsed files) |
| FolderCreator | 1.3 | Complete (templates updated for new sources format) |
| APIKeyManager | 1.4 | Complete |

### Test Summary

| Phase | Tests | Description |
|-------|-------|-------------|
| Phase 5A | 21 | Library matcher, linker, browser |
| Phase 5B | 28 | Library creator, editor, weighting |
| Phase 5+ | 22 | Improvements (usage tracking, Levenshtein, health check) |
| Phase 6 | 54 | Companies House, legal templates, disclaimers, placeholders |
| Phase 7A | 104 | CSS token parser (73) + SCSS/CSS-in-JS/Design Tokens (31) |
| Phase 7B | 61 | Visual router, logo handler, photography handler, guidelines |
| Phase 7C | 65 | Utility app integrations (ColourToken, TypographyBuilder, LogoMaker, ImageBrowser) |
| Phase 8 | - | Export system (no test script yet - manual verification) |
| **Total** | **355** | All tests passing |

### Phase 8 Files Created

| File | Purpose |
|------|---------|
| `lib/server/types/export.ts` | Export type definitions |
| `lib/server/export-router.ts` | Export routing and orchestration |
| `lib/server/export-manager.ts` | High-level export management API |
| `lib/server/exporters/index.ts` | Barrel exports |
| `lib/server/exporters/json-exporter.ts` | JSON export generation |
| `lib/server/exporters/markdown-exporter.ts` | Markdown export generation |
| `lib/server/exporters/pdf-exporter.ts` | PDF/HTML export generation |
| `lib/server/exporters/prompt-pack-exporter.ts` | AI prompt pack generation |

---

*Last updated: 2026-01-16 - Phase 8 Complete (Export System - JSON, Markdown, PDF, Prompt Pack)*
