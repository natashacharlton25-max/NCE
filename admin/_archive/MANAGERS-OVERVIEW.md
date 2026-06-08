# Managers Overview

> **Purpose:** Compartmentalize system configs by domain to prevent Orchestrator bottleneck
> **Type:** Architecture Pattern
> **Status:** Planning

---

## The Three-Tier Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│  ORCHESTRATOR (cross-domain)                                    │
│  • Assigns work to correct manager                              │
│  • Cross-domain sequencing                                      │
│  • Long-running job tracking (days/weeks)                       │
│  • Human/auto trigger management                                │
│  • NOT bottlenecked - delegates domain work to managers         │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  MANAGERS (domain-scoped orchestrators)                         │
│  • Each manager owns configs for ONE domain                     │
│  • Coordinates modules needed for those configs                 │
│  • Cannot call other managers directly                          │
│  • Signals Orchestrator when needs other domain                 │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  MODULES (single responsibility)                                │
│  • Own bricks only                                              │
│  • No coordination                                              │
│  • Just do their job when called                                │
└─────────────────────────────────────────────────────────────────┘
```

---

## Why Managers?

**Problem:** If Orchestrator handles ALL configs for ALL domains, it becomes:
- Overloaded (bottleneck)
- Single point of failure
- Complex to maintain

**Solution:** Compartmentalize configs by domain:
- Each domain gets a Manager
- Manager = domain configs + module coordination for those configs
- Orchestrator = assigns to managers + cross-domain coordination

**Result:** Same total work, distributed better.

---

## Manager Responsibilities

Each Manager is essentially a **domain-scoped orchestrator**:

| Responsibility | Description |
|---------------|-------------|
| **Own domain configs** | Knows all configs related to its domain |
| **Module coordination** | Coordinates the modules needed to execute those configs |
| **State tracking** | Tracks progress within its domain |
| **Signal completion** | Tells Orchestrator when domain work is done |
| **Request other domains** | Signals Orchestrator when needs another domain's work |

### Key Rule: Managers Don't Call Other Managers

```
BrandManager needs typography work?
    ✗ BrandManager → TypographyManager (WRONG - direct call)
    ✓ BrandManager → Orchestrator → TypographyManager (CORRECT)
```

This keeps:
- Clear separation of concerns
- Orchestrator in control of cross-domain sequencing
- Clean audit trail

---

## Managers List

| Manager | Domain | Modules Coordinated |
|---------|--------|---------------------|
| **BrandManager** | Brand building/editing | Brand, AICaller, Writer, Parsers, BrandTracker |
| **TypographyManager** | Font selection/management | Typography, FontManager |
| **VisualManager** | Colors, images, visual assets | ColourMaker, ColourMix, ImageCollections |
| **ContentManager** | Content generation | Content, AICaller, LanguageCheck, Parsers |
| **ExportManager** | Output/export operations | PDFMaker, Export modules |
| **SocialManager** | Social media operations | SocialMedia-Planner, SocialMedia-Manager |

---

## Example: Brand Building Flow

A brand build spans multiple domains and takes time (days/weeks):

```
ORCHESTRATOR: Job "Build Acme Brand"
│
├── Step 1: BrandManager.buildCore [HUMAN trigger]
│   ├── Brand module (store data)
│   ├── AICaller (generate mission)
│   ├── Writer (save)
│   └── Parsers (validate)
│   → BrandManager signals: "Core done, need typography"
│
├── Step 2: Orchestrator triggers TypographyManager [AUTO]
│   ├── Typography module
│   ├── FontManager
│   └── Writer
│   → TypographyManager signals: "Fonts done"
│
├── Step 3: Orchestrator triggers VisualManager [AUTO]
│   ├── ColourMaker
│   ├── ColourMix
│   └── Writer
│   → VisualManager signals: "Visuals done, need approval"
│
├── Step 4: Wait for human approval [HUMAN trigger]
│
└── Step 5: BrandTracker marks complete [AUTO]
```

**Note:**
- Orchestrator manages the cross-domain sequence
- Each Manager handles its domain's module coordination
- Mix of human and auto triggers

---

## Trigger Types

Jobs can have mixed triggers:

| Trigger | Description | Example |
|---------|-------------|---------|
| **AUTO** | Previous step completes → next starts automatically | Core validated → auto-trigger typography |
| **HUMAN** | Step completes → wait for human action | AI generates palette → human approves |

Orchestrator tracks job state across triggers:

```json
{
  "jobId": "brand_acme_setup",
  "created": "2026-01-01",
  "status": "waiting_human",
  "currentStep": 5,
  "waitingFor": "visual_approval",
  "completedSteps": [1, 2, 3, 4],
  "nextTrigger": "human"
}
```

---

## Manager Config Structure

Each manager has a config defining:
- Which modules it coordinates
- What configs it owns
- Step sequences for its domain

```json
{
  "manager": "BrandManager",
  "domain": "brand",
  "description": "Brand building and editing",

  "modules": [
    "Brand",
    "AICaller",
    "Writer",
    "Parsers",
    "BrandTracker"
  ],

  "configs": [
    "brand-core",
    "brand-values",
    "brand-mission",
    "brand-voice"
  ],

  "steps": {
    "buildCore": {
      "sequence": ["collectInput", "validateInput", "generateMission", "save"],
      "trigger": "human"
    },
    "updateSection": {
      "sequence": ["loadSection", "editSection", "validate", "save"],
      "trigger": "human"
    }
  }
}
```

---

## Trackers

Some domains need trackers to monitor completion status:

| Tracker | Purpose |
|---------|---------|
| **BrandTracker** | Tracks brand section completion, validation status, gates access |

Trackers:
- Receive updates when sections complete
- Provide status checks ("can this brand use content generation?")
- Feed PolicyEngine for validation gates
- Pass to Logger for audit trail

---

## Adding a New Manager

1. **Identify the domain** - What logical grouping of configs?
2. **List modules needed** - What modules does this domain coordinate?
3. **Define configs** - What configs belong to this domain?
4. **Define steps** - What sequences of module calls?
5. **Create manager doc** - Document in `/Modules/MANAGERNAME.md`
6. **Register in system.config** - Add manager to Orchestrator's manager list

---

## Dependencies

| Depends On | For |
|------------|-----|
| Orchestrator | Cross-domain coordination, job assignment |
| All coordinated modules | Executing domain work |
| Logger | Audit trail |

---

## Used By

| Component | How |
|-----------|-----|
| Orchestrator | Delegates domain work to managers |
| PolicyEngine | Checks with trackers for validation gates |

---

*Last updated: 2026-01-17*
