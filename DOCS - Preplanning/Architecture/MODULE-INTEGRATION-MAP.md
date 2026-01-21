# Module Integration Map

> **Purpose:** Visual overview of all BrandKit Workflow modules, their connections, and what's missing.
> **Created:** 2026-01-16
> **Status:** Planning Document

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              TRIGGERS                                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │ Manual   │  │ API Call │  │ Webhook  │  │ Schedule │  │ Event    │      │
│  │ (UI/CLI) │  │ (Script) │  │ (Ext)    │  │ (Cron)   │  │ (Watch)  │      │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘      │
│       │             │             │             │             │             │
│       └─────────────┴─────────────┴─────────────┴─────────────┘             │
│                                   │                                          │
│                                   ▼                                          │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    ORCHESTRATOR (Missing)                            │    │
│  │  • Job queue          • Error handling      • Retry logic           │    │
│  │  • Progress tracking  • Logging             • Notifications         │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CORE MODULES (Built)                               │
│                                                                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐  │
│  │   PHASE 1   │───▶│   PHASE 3   │───▶│   PHASE 4   │───▶│   PHASE 8   │  │
│  │ Brand Setup │    │  AI Caller  │    │  Generator  │    │   Export    │  │
│  │             │    │             │    │             │    │             │  │
│  │ • Folders   │    │ • Claude    │    │ • Sections  │    │ • JSON      │  │
│  │ • Config    │    │ • Prompts   │    │ • Drafts    │    │ • Markdown  │  │
│  │ • Domain    │    │ • Response  │    │ • Approval  │    │ • PDF       │  │
│  └─────────────┘    └─────────────┘    └─────────────┘    │ • Prompts   │  │
│         │                 │                   │           └─────────────┘  │
│         │                 │                   │                  │          │
│         │           ┌─────┴─────┐       ┌─────┴─────┐           │          │
│         │           ▼           ▼       ▼           ▼           │          │
│         │    ┌───────────┐ ┌───────────┐ ┌───────────┐          │          │
│         │    │  PHASE 5  │ │  PHASE 6  │ │  PHASE 7  │          │          │
│         │    │  Library  │ │   Legal   │ │  Visual   │          │          │
│         │    │           │ │           │ │           │          │          │
│         │    │ • Tones   │ │ • Co.House│ │ • Colours │          │          │
│         │    │ • Values  │ │ • Disclaim│ │ • Fonts   │          │          │
│         │    │ • Match   │ │ • Templates│ │ • Logos  │          │          │
│         │    └───────────┘ └───────────┘ └───────────┘          │          │
│         │           │            │             │                 │          │
│         └───────────┴────────────┴─────────────┴─────────────────┘          │
│                                   │                                          │
│                                   ▼                                          │
│                          ┌─────────────────┐                                │
│                          │   File Manager  │                                │
│                          │   (Phase 1.3)   │                                │
│                          │                 │                                │
│                          │ • Read/Write    │                                │
│                          │ • Paths         │                                │
│                          │ • Validation    │                                │
│                          └─────────────────┘                                │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              DATA STORES                                     │
│                                                                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐  │
│  │  BrandData  │    │   Library   │    │   Config    │    │   Exports   │  │
│  │  /{brand}/  │    │  /library/  │    │  /_config/  │    │  /exports/  │  │
│  │             │    │             │    │             │    │             │  │
│  │ • brandkit/ │    │ • tone/     │    │ • global    │    │ • JSON      │  │
│  │ • draft/    │    │ • values/   │    │ • domains   │    │ • PDF       │  │
│  │ • _config/  │    │ • colours/  │    │ • prompts   │    │ • prompts/  │  │
│  │ • _meta/    │    │             │    │             │    │ • _history  │  │
│  └─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CONSUMERS (Outputs)                                │
│                                                                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐  │
│  │  Content    │    │   Client    │    │  External   │    │  Analytics  │  │
│  │  Pipeline   │    │  Delivery   │    │   Tools     │    │  Dashboard  │  │
│  │             │    │             │    │             │    │             │  │
│  │ Uses:       │    │ Uses:       │    │ Uses:       │    │ Uses:       │  │
│  │ • Prompts   │    │ • PDF       │    │ • JSON API  │    │ • History   │  │
│  │ • JSON      │    │ • Markdown  │    │ • Webhooks  │    │ • Progress  │  │
│  └─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Module Status Summary

| Module | Phase | Status | Test Count | Key Functions |
|--------|-------|--------|------------|---------------|
| Brand Setup | 1-2 | ✅ Built | ~30 | createBrand, detectDomain, loadConfig |
| AI Caller | 3 | ✅ Built | ~25 | callClaude, buildPrompt, parseResponse |
| Section Generator | 4 | ✅ Built | ~40 | generateSection, saveDraft, approve |
| Library System | 5 | ✅ Built | ~50 | matchEntry, scoreMatch, bulkOps |
| Legal Pathway | 6 | ✅ Built | 54 | lookupCompany, generateDisclaimers |
| Visual Pathway | 7 | ✅ Built | 104 | parseCSS, generatePalette, typography |
| Export System | 8 | ✅ Built | 58 | exportJSON, exportPDF, promptPack |
| **Orchestrator** | - | ❌ Missing | - | jobQueue, runWorkflow, handleErrors |
| **API Layer** | - | ❌ Missing | - | REST endpoints, auth, rate limiting |
| **UI Dashboard** | - | ❌ Missing | - | progress view, analytics, controls |

---

## Data Flow: Brand Generation

```
START: New Brand Request
         │
         ▼
┌─────────────────┐
│ 1. BRAND SETUP  │  Input: brand name, website, domain
│    Phase 1-2    │  Output: brand folder structure, config
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 2. DOMAIN       │  Input: brand config
│    DETECTION    │  Output: detected domain, keywords
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 3. LIBRARY      │  Input: domain, section type
│    MATCHING     │  Output: matched entries (tones, values)
│    Phase 5      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 4. SECTION      │  Input: brand config, library matches, prompts
│    GENERATION   │  Output: draft sections
│    Phase 3-4    │
└────────┬────────┘
         │
         ├──────────────────┬──────────────────┐
         ▼                  ▼                  ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ 5a. LEGAL       │ │ 5b. VISUAL      │ │ 5c. VOICE       │
│     Phase 6     │ │     Phase 7     │ │     Phase 4     │
│                 │ │                 │ │                 │
│ • Co. House     │ │ • Colours       │ │ • Tone          │
│ • Disclaimers   │ │ • Typography    │ │ • Personality   │
│ • Terms         │ │ • Logo specs    │ │ • Vocabulary    │
└────────┬────────┘ └────────┬────────┘ └────────┬────────┘
         │                   │                   │
         └───────────────────┼───────────────────┘
                             │
                             ▼
┌─────────────────┐
│ 6. APPROVAL     │  Input: all draft sections
│    WORKFLOW     │  Output: approved brandkit
│    (Manual/Auto)│
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 7. EXPORT       │  Input: approved brandkit
│    Phase 8      │  Output: JSON, PDF, Markdown, Prompts
└────────┬────────┘
         │
         ▼
END: Exported Brand Assets
```

---

## Missing Modules Detail

### 1. Orchestrator (Critical)

**Purpose:** Coordinates all modules, manages workflow execution

**Why needed:**
- Currently, calling modules requires manual scripting
- No way to run "generate full brand" as single command
- No error recovery if one step fails
- No progress tracking during long operations

**Functions needed:**

| Function | Purpose |
|----------|---------|
| `runWorkflow(brandPath, workflowName)` | Execute named workflow (e.g., "full-brand", "visual-only") |
| `addJob(jobType, params)` | Add job to queue |
| `getJobStatus(jobId)` | Check job progress |
| `cancelJob(jobId)` | Cancel running job |
| `retryJob(jobId)` | Retry failed job |
| `getWorkflowProgress(workflowId)` | Get % complete, current step |

**Workflows to define:**

| Workflow | Steps | Use Case |
|----------|-------|----------|
| `full-brand` | Setup → All sections → Export | New brand from scratch |
| `visual-refresh` | Phase 7 only → Export visual | Update colours/fonts |
| `legal-update` | Phase 6 only → Export legal | Company info changed |
| `re-export` | Export only (no generation) | New format needed |

---

### 2. API Layer (Optional for automation)

**Purpose:** HTTP endpoints to trigger operations from external systems

**Why needed:**
- Other tools can trigger brand generation
- Webhooks can notify on completion
- Rate limiting prevents overload

**Endpoints needed:**

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/brands` | GET | List all brands |
| `/brands` | POST | Create new brand |
| `/brands/:id` | GET | Get brand status |
| `/brands/:id/generate` | POST | Start generation workflow |
| `/brands/:id/export` | POST | Trigger export |
| `/jobs/:id` | GET | Get job status |
| `/webhooks` | POST | Register webhook |

**Note:** If everything runs locally via scripts, API layer may not be needed.

---

### 3. UI Dashboard (For visibility)

**Purpose:** Visual interface for monitoring and control

**Why needed:**
- See all brands at a glance
- Monitor generation progress
- View analytics (how many brands, exports, etc.)
- Manual approval workflow

**Views needed:**

| View | Purpose | Data Source |
|------|---------|-------------|
| Brand List | See all brands, status | `BrandData/*/` |
| Brand Detail | See sections, progress | `_meta/progress.json` |
| Generation Progress | Live progress bar | Orchestrator events |
| Export History | Past exports, download | `exports/_history.json` |
| Library Browser | Browse/edit library entries | `library/` |
| Analytics | Counts, trends | Aggregated from all |

---

## Integration Points

### What calls what:

```
┌─────────────────────────────────────────────────────────────────┐
│                    INTEGRATION MATRIX                            │
├─────────────────┬───────┬───────┬───────┬───────┬───────┬──────┤
│                 │ Setup │ AI    │ Gen   │ Lib   │ Legal │ Vis  │
├─────────────────┼───────┼───────┼───────┼───────┼───────┼──────┤
│ Brand Setup     │   -   │       │       │       │       │      │
│ AI Caller       │       │   -   │       │       │       │      │
│ Generator       │   ✓   │   ✓   │   -   │   ✓   │       │      │
│ Library         │   ✓   │       │       │   -   │       │      │
│ Legal           │   ✓   │   ✓   │       │       │   -   │      │
│ Visual          │   ✓   │       │       │   ✓   │       │   -  │
│ Export          │   ✓   │       │   ✓   │       │       │   ✓  │
│ File Manager    │   ✓   │       │   ✓   │   ✓   │   ✓   │   ✓  │
└─────────────────┴───────┴───────┴───────┴───────┴───────┴──────┘

Legend: ✓ = calls/depends on
```

### External integrations (Phase 9 planned):

| Integration | Purpose | Priority |
|-------------|---------|----------|
| Canva API | Sync brand assets to Canva | Medium |
| Unsplash/Pexels | Fetch stock photos | Low |
| Google Fonts | Font previews/downloads | Low |
| Companies House | Legal entity lookup | ✅ Built |

---

## Recommended Build Order

Based on what's missing and dependencies:

| Priority | Module | Reason |
|----------|--------|--------|
| **1** | Orchestrator | Everything needs coordination |
| **2** | CLI Commands | Run workflows from terminal |
| **3** | Analytics/Stats | Visibility into what's happening |
| **4** | UI Dashboard | Nice-to-have for monitoring |
| **5** | API Layer | Only if external systems need access |
| **6** | External Integrations | Phase 9 - Canva, stock photos |

---

## Questions to Answer

Before building missing modules:

1. **How do you want to trigger brand generation?**
   - [ ] CLI command (`npm run generate-brand "Brand Name"`)
   - [ ] Script import (`import { generateBrand } from ...`)
   - [ ] API endpoint (`POST /brands`)
   - [ ] UI button

2. **What approval workflow?**
   - [ ] Fully automated (no human review)
   - [ ] Human reviews each section
   - [ ] Human reviews final output only
   - [ ] Configurable per brand

3. **Where do generated brands go?**
   - [ ] Local folder only
   - [ ] Also to cloud storage
   - [ ] Also to external tool (Canva, etc.)

4. **Who consumes the exports?**
   - [ ] Content creation pipeline (needs Prompt Pack)
   - [ ] Client delivery (needs PDF)
   - [ ] Developers (needs JSON API)
   - [ ] All of the above

---

*Next: Answer questions above, then detail each missing module's functions*
