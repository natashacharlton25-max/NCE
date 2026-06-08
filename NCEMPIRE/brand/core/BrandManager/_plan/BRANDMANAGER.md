# BrandManager

> **Purpose:** Domain-scoped orchestrator for brand building and editing operations
> **Type:** Manager (Domain Coordinator)
> **Status:** Planning

---

## Overview

BrandManager is the **domain coordinator** for all brand-related operations. It sits between Orchestrator and the modules, handling cross-module coordination within the brand domain.

```
Orchestrator assigns job → BrandManager coordinates modules → Signals completion
```

**BrandManager is NOT a module** - it's a **manager** that coordinates modules to complete brand configs.

---

## Roles & Rules

### BrandManager DOES:
- Own all brand-related configs (brand-core, brand-values, brand-mission, brand-voice)
- Coordinate modules needed for brand operations (Brand, AICaller, Writer, Parsers, BrandTracker)
- Track progress within the brand domain
- Signal Orchestrator when domain work is complete
- Signal Orchestrator when needs another domain's work (e.g., typography, visuals)
- Manage brand section sequencing (core must complete before visuals)

### BrandManager does NOT:
- Call other managers directly (must go through Orchestrator)
- Execute module functions itself (coordinates, doesn't do)
- Make cross-domain decisions (Orchestrator handles that)
- Store brand data directly (Brand module does that)
- Handle AI calls directly (AICaller does that)

### Boundaries:
- Cannot bypass Orchestrator for cross-domain work
- Cannot modify data directly - uses Writer
- Cannot skip validation - uses Parsers and PolicyEngine
- Must update BrandTracker on section changes
- Must respect section dependencies (core → visuals → typography)

---

## Domain Scope

### Configs Owned

| Config | Description | Trigger |
|--------|-------------|---------|
| `brand-core` | Core brand identity (name, mission, values) | Human |
| `brand-values` | Brand values and principles | Human |
| `brand-mission` | Mission statement generation | Auto/Human |
| `brand-voice` | Tone and voice guidelines | Auto |
| `brand-update` | Section editing/updating | Human |
| `brand-validate` | Validation of brand sections | Auto |

### Modules Coordinated

| Module | Purpose in Domain |
|--------|-------------------|
| **Brand** | Store/retrieve brand data |
| **AICaller** | Generate brand content via AI |
| **Writer** | Save brand files |
| **Parsers** | Validate brand data format |
| **BrandTracker** | Track section completion status |

---

## Section Dependencies

Brand building has **strict dependencies** - sections must be completed in order:

```
┌──────────────────────────────────────────────────────────────┐
│  PHASE 1: CORE (Required first)                              │
│  • Brand name, industry, description                         │
│  • Must be validated before Phase 2                          │
└──────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────┐
│  PHASE 2: IDENTITY                                           │
│  • Mission statement                                         │
│  • Values                                                    │
│  • Voice/tone                                                │
│  → BrandManager signals: "Identity done, need typography"    │
└──────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────┐
│  PHASE 3: VISUALS (Requires Orchestrator cross-domain)       │
│  → Orchestrator triggers TypographyManager                   │
│  → Orchestrator triggers VisualManager                       │
│  → Results fed back to BrandManager                          │
└──────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────┐
│  PHASE 4: COMPLETE                                           │
│  • All sections validated                                    │
│  • Brand marked complete in BrandTracker                     │
│  • Brand unlocked for content generation                     │
└──────────────────────────────────────────────────────────────┘
```

**Key:** BrandManager cannot trigger TypographyManager or VisualManager directly. It signals Orchestrator, which then assigns work to those managers.

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `startBuild(brandId, input)` | Start new brand build | brandId, input data | buildSession |
| `updateSection(brandId, section, data)` | Update a brand section | brandId, section, data | updateResult |
| `validateSection(brandId, section)` | Validate a section | brandId, section | validationResult |
| `getStatus(brandId)` | Get brand build status | brandId | statusReport |
| `requestCrossDomain(brandId, domain)` | Signal need for another domain | brandId, domain | request |
| `receiveResult(brandId, domain, result)` | Receive cross-domain result | brandId, domain, result | received |
| `completeBuild(brandId)` | Finalize brand build | brandId | completion |

---

## Detailed Function Specs

### startBuild(brandId, input)

Initiates a new brand build session.

```javascript
// Input
{
  brandId: "acme",
  input: {
    source: "user",  // "user" | "ai" | "website"
    data: {
      name: "Acme Corp",
      industry: "Technology",
      description: "Enterprise software solutions"
    }
  }
}

// Process
1. Validate input data via Parsers
2. Create brand folder via Builders (through Orchestrator)
3. Initialize BrandTracker entry
4. Save initial data via Writer
5. Return build session

// Output
{
  success: true,
  brandId: "acme",
  sessionId: "build_acme_20260117",
  currentPhase: 1,
  nextStep: "brand-core",
  status: "in_progress"
}
```

### updateSection(brandId, section, data)

Updates a specific brand section.

```javascript
// Input
{
  brandId: "acme",
  section: "mission",
  data: {
    source: "ai",
    content: "Empowering businesses through innovative software...",
    approved: false
  }
}

// Process
1. Check section dependencies (can this section be updated?)
2. Validate data format via Parsers
3. Save to brand section via Writer
4. Update BrandTracker with change
5. If AI-generated, mark for approval

// Output
{
  success: true,
  brandId: "acme",
  section: "mission",
  status: "pending_approval",
  nextStep: "await human approval",
  trackerUpdated: true
}
```

### validateSection(brandId, section)

Validates a brand section is complete and correct.

```javascript
// Input
{
  brandId: "acme",
  section: "core"
}

// Process
1. Load section data from Brand module
2. Run validation via Parsers
3. Check required fields present
4. Update BrandTracker with validation status
5. Check if phase can advance

// Output
{
  valid: true,
  brandId: "acme",
  section: "core",
  issues: [],
  canAdvancePhase: true,
  nextPhase: 2
}
```

### requestCrossDomain(brandId, domain)

Signals Orchestrator that cross-domain work is needed.

```javascript
// Input
{
  brandId: "acme",
  domain: "typography",
  context: {
    industry: "technology",
    style: "modern",
    brandVoice: "professional"
  }
}

// Process
1. Package request with brand context
2. Signal Orchestrator
3. Wait for Orchestrator acknowledgment

// Output
{
  requested: true,
  brandId: "acme",
  domain: "typography",
  orchestratorJobId: "job_xxx",
  status: "pending"
}
```

### receiveResult(brandId, domain, result)

Receives results from cross-domain work.

```javascript
// Input
{
  brandId: "acme",
  domain: "typography",
  result: {
    fonts: {
      primary: "Inter",
      secondary: "Merriweather"
    },
    validated: true
  }
}

// Process
1. Validate received data
2. Save to brand via Writer
3. Update BrandTracker
4. Check if build can proceed

// Output
{
  received: true,
  brandId: "acme",
  domain: "typography",
  integrated: true,
  buildStatus: "phase_3_complete"
}
```

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `BrandManager.initBrand` | Start new brand | startBuild → validateSection |
| `BrandManager.buildCore` | Build core section | updateSection → validateSection |
| `BrandManager.generateMission` | AI generate mission | AICaller → updateSection → validateSection |
| `BrandManager.requestVisuals` | Request visual work | requestCrossDomain |
| `BrandManager.finalizeBrand` | Complete brand build | validateSection → completeBuild |

---

## Example: Complete Brand Build Flow

```
Orchestrator: "Start brand build for Acme"
    │
    ├── Step 1: BrandManager.initBrand [HUMAN trigger]
    │   ├── Validate input (Parsers)
    │   ├── Create brand folder (via Builders)
    │   ├── Save initial data (Writer)
    │   └── Initialize tracker (BrandTracker)
    │   → BrandManager: "Core initialized, awaiting input"
    │
    ├── Step 2: BrandManager.buildCore [HUMAN trigger]
    │   ├── User provides core data
    │   ├── Validate data (Parsers)
    │   ├── Save to brand (Writer)
    │   └── Update tracker (BrandTracker)
    │   → BrandManager: "Core complete, starting identity"
    │
    ├── Step 3: BrandManager.generateMission [AUTO trigger]
    │   ├── Build prompt (PromptBuilder)
    │   ├── Call AI (AICaller)
    │   ├── Parse response (Parsers)
    │   └── Save draft (Writer)
    │   → BrandManager: "Mission draft ready for approval"
    │
    ├── Step 4: Wait for approval [HUMAN trigger]
    │   ├── User reviews mission
    │   ├── Approves or requests changes
    │   └── Update tracker (BrandTracker)
    │   → BrandManager: "Identity phase complete, need typography"
    │
    ├── Step 5: BrandManager signals Orchestrator
    │   └── requestCrossDomain("typography")
    │   → Orchestrator: "Routing to TypographyManager"
    │
    ├── Step 6: Orchestrator → TypographyManager [AUTO]
    │   └── (handled by TypographyManager)
    │   → TypographyManager: "Fonts selected"
    │
    ├── Step 7: Orchestrator → VisualManager [AUTO]
    │   └── (handled by VisualManager)
    │   → VisualManager: "Colors selected"
    │
    ├── Step 8: BrandManager.receiveResult [AUTO]
    │   ├── Receive typography result
    │   ├── Receive visual result
    │   ├── Save to brand (Writer)
    │   └── Update tracker (BrandTracker)
    │   → BrandManager: "All phases complete"
    │
    └── Step 9: BrandManager.completeBuild [AUTO]
        ├── Final validation
        ├── Mark complete in BrandTracker
        └── Signal Orchestrator: "Brand fully validated"
        → Brand unlocked for content generation
```

**Note:** This build might span days or weeks, with human triggers at approval points.

---

## Manager Config Structure

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
    "brand-voice",
    "brand-update",
    "brand-validate"
  ],

  "steps": {
    "initBrand": {
      "sequence": ["validateInput", "createFolder", "saveInitial", "initTracker"],
      "trigger": "human"
    },
    "buildCore": {
      "sequence": ["collectInput", "validateData", "saveCore", "updateTracker"],
      "trigger": "human"
    },
    "generateMission": {
      "sequence": ["buildPrompt", "callAI", "parseResponse", "saveDraft"],
      "trigger": "auto"
    },
    "approveMission": {
      "sequence": ["loadDraft", "userReview", "saveApproved", "updateTracker"],
      "trigger": "human"
    },
    "requestVisuals": {
      "sequence": ["packageContext", "signalOrchestrator"],
      "trigger": "auto"
    },
    "completeBuild": {
      "sequence": ["finalValidation", "markComplete", "signalDone"],
      "trigger": "auto"
    }
  },

  "dependencies": {
    "phase1": ["core"],
    "phase2": ["mission", "values", "voice"],
    "phase3": ["typography", "visuals"],
    "phase4": ["complete"]
  },

  "crossDomain": {
    "typography": "TypographyManager",
    "visuals": "VisualManager"
  }
}
```

---

## State Tracking

BrandManager maintains state during builds:

```json
{
  "brandId": "acme",
  "sessionId": "build_acme_20260117",
  "currentPhase": 2,
  "status": "in_progress",

  "sections": {
    "core": { "status": "validated", "lastUpdated": "2026-01-17" },
    "mission": { "status": "pending_approval", "lastUpdated": "2026-01-17" },
    "values": { "status": "not_started" },
    "voice": { "status": "not_started" },
    "typography": { "status": "awaiting_cross_domain" },
    "visuals": { "status": "awaiting_cross_domain" }
  },

  "pendingCrossDomain": ["typography", "visuals"],
  "approvalsPending": ["mission"],

  "nextStep": "await human approval for mission"
}
```

---

## Error Handling

When errors occur within BrandManager's domain:

```javascript
// Module error (e.g., AICaller fails)
try {
  await AICaller.call(request);
} catch (error) {
  // Don't handle retry logic - signal Orchestrator
  await signalOrchestrator({
    type: "module_error",
    brandId,
    module: "AICaller",
    error,
    step: "generateMission"
  });
  // Orchestrator routes to FailureHandler
}

// Validation error (e.g., missing required field)
const validation = await Parsers.validate(data);
if (!validation.valid) {
  return {
    success: false,
    reason: "validation_failed",
    issues: validation.issues,
    action: "request user correction"
  };
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Orchestrator | Receiving work, cross-domain requests |
| Brand | Storing/retrieving brand data |
| AICaller | AI content generation |
| Writer | Saving brand files |
| Parsers | Validating data |
| BrandTracker | Tracking build progress |
| Builders | Path creation (via Orchestrator) |

---

## Used By

| Component | How |
|-----------|-----|
| Orchestrator | Delegates brand domain work |

---

*Last updated: 2026-01-17*
