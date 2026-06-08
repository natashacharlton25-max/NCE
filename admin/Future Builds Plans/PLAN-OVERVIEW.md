# BrandKit Workflow - Plan Overview

> High-level architecture and key decisions for the BrandKit Workflow system.
> For detailed implementation steps, see individual PHASE-X-PLAN.md files.
> For task tracking, see BUILD-CHECKLIST.md.

---

## System Architecture

### The 5 Core Projects

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           BrandKit Ecosystem                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐          │
│  │   BrandKit      │    │ BrandKit AIGen  │    │    BrandLib     │          │
│  │   (Schemas)     │    │  (AI Config)    │    │   (Libraries)   │          │
│  │                 │    │                 │    │                 │          │
│  │ _system/        │    │ sections/       │    │ universal/      │          │
│  │ ├─ core/        │    │ _config/        │    │ therapeutic/    │          │
│  │ ├─ voice/       │    │ ├─ generation-  │    │ ecommerce/      │          │
│  │ ├─ audience/    │    │ │  order.json   │    │ _config/        │          │
│  │ ├─ legal/       │    │ └─ domain-      │    │                 │          │
│  │ └─ visual/      │    │    detection    │    │                 │          │
│  └────────┬────────┘    └────────┬────────┘    └────────┬────────┘          │
│           │                      │                      │                    │
│           └──────────────────────┼──────────────────────┘                    │
│                                  │                                           │
│                                  ▼                                           │
│                    ┌─────────────────────────┐                               │
│                    │   BrandKit Workflow     │                               │
│                    │     (Orchestrator)      │                               │
│                    │                         │                               │
│                    │  SvelteKit App that     │                               │
│                    │  coordinates everything │                               │
│                    └────────────┬────────────┘                               │
│                                 │                                            │
│                                 ▼                                            │
│                    ┌─────────────────────────┐                               │
│                    │      BrandData          │                               │
│                    │      (Output)           │                               │
│                    │                         │                               │
│                    │  {brand-name}/          │                               │
│                    │  ├─ _config/            │                               │
│                    │  ├─ brandkit/           │                               │
│                    │  ├─ draft/              │                               │
│                    │  └─ context/            │                               │
│                    └─────────────────────────┘                               │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Project Locations

| Project | Path | Purpose |
|---------|------|---------|
| **BrandKit** | `C:\Users\Business\BrandKit` | Schema definitions - what a brand contains |
| **BrandKit AIGen** | `C:\Users\Business\BrandKit AIGen` | AI configuration - how to generate content |
| **BrandLib** | `C:\Users\Business\BrandLib` | Shared libraries - reusable content patterns |
| **BrandKit Workflow** | `C:\Users\Business\BrandKit Workflow` | Orchestrator - the main application |
| **BrandData** | `C:\Users\Business\BrandData` | Output - where brand folders are created |

---

### The 4 Utility Apps

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Utility Apps                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────┐    ┌─────────────────┐                                 │
│  │  FolderCreator  │    │  APIKeyManager  │                                 │
│  │                 │    │                 │                                 │
│  │ Creates brand   │    │ Secure storage  │                                 │
│  │ folder          │    │ for API keys    │                                 │
│  │ structures      │    │ (encrypted)     │                                 │
│  └─────────────────┘    └─────────────────┘                                 │
│                                                                              │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐          │
│  │  ColourToken    │    │ TypographyBuilder│   │   LogoMaker     │          │
│  │      App        │    │      App        │    │      App        │          │
│  │                 │    │                 │    │                 │          │
│  │ Build colour    │    │ Select fonts    │    │ Generate logo   │          │
│  │ palettes        │    │ from Google     │    │ variants        │          │
│  └─────────────────┘    └─────────────────┘    └─────────────────┘          │
│                                                                              │
│  ┌─────────────────┐                                                        │
│  │  ImageBrowser   │                                                        │
│  │      App        │                                                        │
│  │                 │                                                        │
│  │ Search stock    │                                                        │
│  │ photos          │                                                        │
│  └─────────────────┘                                                        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Utility App Locations

| App | Path | Purpose | Status |
|-----|------|---------|--------|
| **FolderCreator** | `C:\Users\Business\FolderCreator` | Create brand folder structures | ✓ Built |
| **APIKeyManager** | `C:\Users\Business\APIKeyManager` | Secure API key storage | ✓ Built |
| **ColourToken** | `C:\Users\Business\ColourToken` | Interactive colour palette builder | Not Built |
| **TypographyBuilder** | `C:\Users\Business\TypographyBuilder` | Font selection with Google Fonts | Not Built |
| **LogoMaker** | `C:\Users\Business\LogoMaker` | Generate logo variants | Not Built |
| **ImageBrowser** | `C:\Users\Business\ImageBrowser` | Search Unsplash/Pexels | Not Built |
| **VisualConsistencyChecker** | `C:\Users\Business\VisualConsistencyChecker` | Palette extraction and brand consistency | Not Built |

### Utility App Planning Documents

| App | Planning Document | Integration Layer |
|-----|-------------------|-------------------|
| **ColourToken** | [COLOURTOKEN-APP.md](COLOURTOKEN-APP.md) | Phase 7C.1 ✓ Complete |
| **TypographyBuilder** | [TYPOGRAPHYBUILDER-APP.md](TYPOGRAPHYBUILDER-APP.md) | Phase 7C.2 ✓ Complete |
| **LogoMaker** | [LOGOMAKER-APP.md](LOGOMAKER-APP.md) | Phase 7C.3 ✓ Complete |
| **ImageBrowser** | [IMAGEBROWSER-APP.md](IMAGEBROWSER-APP.md) | Phase 7C.4 ✓ Complete |
| **VisualConsistencyChecker** | [VISUAL-CONSISTENCY-CHECKER-APP.md](VISUAL-CONSISTENCY-CHECKER-APP.md) | Pending |

> **Note:** The BrandKit Workflow integration layer for ColourToken, TypographyBuilder, LogoMaker, and ImageBrowser is complete (Phase 7C). VisualConsistencyChecker integration layer needs to be built.

### Future Utility Apps (Planned)

| App | Purpose | Planning Document | Status |
|-----|---------|-------------------|--------|
| **Canva API** | Generate Canva Brand Kits from exports | [CANVA-API-APP.md](CANVA-API-APP.md) | Planned |
| **FolderCreator Enhancements** | Google Drive sync, asset organization | [FOLDER-CREATOR-APP.md](FOLDER-CREATOR-APP.md) | Core Built, Enhancements Planned |
| **AI Legal App** | AI-assisted creation of legal templates, policies, disclaimers | - | Planned |
| **Librarian** | Smart library management with learning, suggestions, entry promotion | - | Planned |

> **Note:** These apps will be separate projects that integrate with BrandKit Workflow.

---

## Phase Summary

| Phase | Focus | Key Outputs | Status |
|-------|-------|-------------|--------|
| **1** | Foundation | Global config, Folder Creator, API Keys Manager, Brand Status | Complete |
| **2** | Input Gathering | File uploads, Website fetcher, Import parser, Templates | Complete |
| **3** | Domain Detection | Keyword scoring, Domain suggestion, User selection fallback | Complete |
| **4** | AI Generation | Prompt builder, Section router, AI response handling | Complete |
| **5** | Library Matching | Library loader, Field matcher, Match scoring, Weighting | Complete (71 tests) |
| **6** | Legal Pathway | Companies House, Legal templates, Disclaimers, Placeholders | Complete (54 tests) |
| **7** | Visual Pathway | CSS parser, Utility app integration, Visual guidelines | Complete (230 tests) |
| **8** | Export | JSON, Markdown, PDF, Prompt Pack exports | Complete |
| **9** | Integrations | Canva, Unsplash, Pexels, Google Fonts, Registries | Not started |
| **10** | UI/App | SvelteKit dashboard, editors, pathways, settings | Not started |

---

## Key Policies

### Domain Lifecycle Policy

**Domain is immutable after brand creation.**

| Scenario | Action |
|----------|--------|
| Domain set at creation | User selects OR AI detects from context |
| Want to change domain | Create new brand with correct domain |
| Similar domains | May share libraries (e.g., therapeutic ↔ mental-health) |
| Completely different domain | Create new brand, future "import from brand" feature |

**Rationale:** Domain determines which libraries are available. Changing domain would invalidate existing library selections and require re-matching all sections.

---

### Legal Section Dependencies

```
core/identity
      │
      ▼
┌─────────────┐
│ legal/core  │  Registration details, business name
└─────┬───────┘
      │
      ▼
┌─────────────┐
│legal/entity │  Entity type, structure, directors
└─────┬───────┘
      │
      ▼
┌──────────────────┐
│legal/compliance  │  Regulatory requirements, policies
└─────┬────────────┘
      │
      ▼
┌──────────────────┐
│legal/disclaimers │  Generated legal statements
└──────────────────┘
```

**Note:** Legal sections form a chain - each depends on the previous. All legal work is separate from the core brand workflow.

---

### Section Status Flow

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│  not_started │ ───▶ │  in_progress │ ───▶ │   complete   │
│              │      │              │      │              │
│  (no data)   │      │  (in draft/) │      │ (in brandkit/)│
└──────────────┘      └──────────────┘      └──────────────┘
                             │
                             ▼
                      ┌──────────────┐
                      │   editing    │  (copy from brandkit to draft)
                      └──────────────┘
```

---

## Key Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Framework** | SvelteKit | Fast, reactive, good DX |
| **CSS** | Tailwind CSS | Utility-first, consistent |
| **Icons** | Phosphor Icons | Clean, comprehensive |
| **File naming** | kebab-case | Consistent across all files |
| **Root path** | `C:\Users\Business\` | All projects under one root |
| **API key storage** | AES-256-GCM encrypted | Secure, no external deps |
| **Default region** | Australia (AU) | Primary user location |
| **State management** | Svelte stores | Native, simple |

---

## File Naming Convention

| Type | Convention | Examples |
|------|------------|----------|
| Multi-word files | kebab-case | `brand-status.ts`, `legal-reviews.json` |
| Single word files | lowercase | `progress.json`, `brand.json` |
| Folders | kebab-case or single word | `api-keys/`, `context/` |
| Config folders | underscore prefix | `_config/`, `_meta/` |

---

## Brand Folder Structure

```
BrandData/{brand-slug}/
├── _config/
│   ├── brand.json              # Brand metadata (name, domain, region)
│   └── business.json           # Companies House data, VAT (Phase 6)
├── _meta/
│   ├── progress.json           # Section completion tracking
│   └── legal-reviews.json      # Legal review status
├── brandkit/                   # Approved sections (complete)
│   ├── core/
│   ├── voice/
│   ├── legal/
│   └── visual/
├── draft/                      # Work-in-progress sections
├── context/
│   ├── sources.json            # Input manifest
│   ├── website/                # Fetched content
│   ├── documents/              # Uploaded files
│   └── visual/                 # Visual assets
├── legal/                      # Generated legal content (Phase 6)
│   ├── cookie-policy.json
│   ├── privacy-policy.json
│   └── terms.json
├── exports/                    # Export outputs (Phase 8)
│   ├── _history.json           # Export history tracking
│   ├── {brand}-brandkit.json   # JSON export
│   ├── {brand}-guide.md        # Markdown export
│   ├── {brand}-guidelines.pdf  # PDF export (or .html)
│   └── {brand}-prompts/        # Prompt Pack folder
│       ├── system-prompt.md
│       ├── voice-guide.md
│       ├── quick-reference.md
│       ├── writing-checklist.md
│       └── example-prompts.md
└── libraries/                  # Brand-specific library entries
```

---

## Related Documents

| Document | Purpose |
|----------|---------|
| [BUILD-CHECKLIST.md](./BUILD-CHECKLIST.md) | Granular task tracking |
| [PHASE-1-PLAN.md](./PHASE-1-PLAN.md) | Foundation details |
| [PHASE-2-PLAN.md](./PHASE-2-PLAN.md) | Input gathering details |
| [PHASE-3-PLAN.md](./PHASE-3-PLAN.md) | Domain detection details |
| [PHASE-4-PLAN.md](./PHASE-4-PLAN.md) | AI generation details |
| [PHASE-5-PLAN.md](./PHASE-5-PLAN.md) | Library matching details |
| [PHASE-6-PLAN.md](./PHASE-6-PLAN.md) | Legal pathway details |
| [PHASE-7-PLAN.md](./PHASE-7-PLAN.md) | Visual pathway details |
| [PHASE-8-PLAN.md](./PHASE-8-PLAN.md) | Export details |
| [PHASE-9-PLAN.md](./PHASE-9-PLAN.md) | Integration details |
| [PHASE-10-PLAN.md](./PHASE-10-PLAN.md) | UI/App details |
| [EXTENSIBILITY-GUIDE.md](./EXTENSIBILITY-GUIDE.md) | How to extend the system |
| [PLAN-REVIEW-ISSUES.md](./PLAN-REVIEW-ISSUES.md) | Issue tracking |

---

## Future Enhancements

### Default Libraries

BrandLib will include default library entries for common use cases:

| Library | Default Entries | Purpose |
|---------|-----------------|---------|
| **legal/templates** | Cookie Policy, Privacy Policy, Terms of Service, GDPR | Common legal documents with placeholders |
| **legal/disclaimers** | Domain-specific disclaimers (therapeutic, ecommerce, etc.) | Pre-approved disclaimer text |
| **voice/tone** | Professional, Warm, Friendly, Authoritative | Common brand tones |
| **visual/colours** | Industry colour palettes | Starting points for brands |

> Default entries have `weight: 1.0` and `source: 'curated'` to prioritize them in matching.

### AI Future Enhancements

| Enhancement | App/Phase | Description |
|-------------|-----------|-------------|
| **AI Legal App** | Separate App | AI-assisted creation of legal templates with human review |
| **Librarian App** | Separate App | Smart library with learning from corrections, A/B suggestions |
| **Multi-jurisdiction** | Phase 6+ | Support for AU (ABN/ACN), US (EIN), EU business registries |
| **Smart Matching** | Phase 5B+ | Entry weighting, confidence scoring with weight factor |
| **Template Generation** | AI Legal App | AI generates initial drafts, human reviews and approves |

### AI Legal App (Future)

```
┌─────────────────────────────────────────────────────────┐
│                    AI Legal App                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  1. User selects template type (Privacy Policy, etc.)    │
│  2. AI generates initial draft based on brand context    │
│  3. Human reviews and edits with suggestions             │
│  4. Approved templates saved to BrandLib                 │
│  5. Templates versioned with audit trail                 │
│                                                          │
│  NOT for automatic generation - requires human review    │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Multi-Jurisdiction Support (Future)

| Region | Registry | API | Status |
|--------|----------|-----|--------|
| UK | Companies House | Public Data API | ✓ Complete |
| AU | ASIC (ABN/ACN) | ABR Lookup | Planned |
| US | State Registries | Varies by state | Planned |
| EU | Various | Country-specific | Planned |

> **Note:** UK Companies House is the only business registry integration currently implemented.
