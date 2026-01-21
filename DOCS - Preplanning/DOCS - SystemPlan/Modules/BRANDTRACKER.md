# BrandTracker Module

> **Purpose:** Track brand build progress, section completion, and validation status
> **Type:** Tracker Module
> **Status:** Planning

---

## Overview

BrandTracker is a **status tracking module** that monitors brand build progress. It maintains a live cache of which sections are complete, when they changed, and which need validation. Think of it as the "checklist keeper" for brand builds.

```
Section updates → BrandTracker records → PolicyEngine checks → Gates access
```

**BrandTracker is NOT:**
- A manager (doesn't coordinate modules)
- A database (just tracks status, data lives in Brand module)
- A validator (Parsers does validation, BrandTracker records result)

**BrandTracker IS:**
- A status tracker
- A completion cache
- A gate provider for PolicyEngine

---

## Roles & Rules

### BrandTracker DOES:
- Track section completion status for each brand
- Record when sections were last changed
- Record validation status per section
- Provide status checks for PolicyEngine
- Maintain completion percentage per brand
- Track phase progression
- Cache status for quick lookups

### BrandTracker does NOT:
- Store brand data (Brand module does that)
- Validate data (Parsers does that)
- Make decisions about what to do next (BrandManager does that)
- Coordinate modules (Managers do that)
- Log to audit trail (Logger does that)

### Boundaries:
- Read-only access to brand data
- Cannot modify brand content
- Cannot skip BrandManager updates
- Must reflect actual data state (no stale cache)
- Provides status only - doesn't enforce gates (PolicyEngine does)

---

## Why BrandTracker?

**Problem:** Without tracking, we don't know:
- Can this brand use content generation? (Are required sections complete?)
- Which brands are incomplete?
- What needs approval?
- When was this section last validated?

**Solution:** BrandTracker maintains a live status cache:
- PolicyEngine asks: "Can brand X generate content?"
- BrandTracker responds: "No - missing typography and visuals"
- User asks: "What brands need attention?"
- BrandTracker responds: "Brand A - awaiting approval, Brand B - 60% complete"

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `initBrand(brandId)` | Create tracking entry for new brand | brandId | tracker |
| `updateSection(brandId, section, status)` | Update section status | brandId, section, status | updated |
| `getStatus(brandId)` | Get full brand status | brandId | statusReport |
| `getSectionStatus(brandId, section)` | Get specific section status | brandId, section | sectionStatus |
| `canProceed(brandId, requiredSections)` | Check if can proceed with operation | brandId, sections[] | canProceed |
| `listIncomplete()` | List all incomplete brands | - | brandList |
| `listAwaitingApproval()` | List brands awaiting approval | - | brandList |
| `markValidated(brandId, section)` | Mark section as validated | brandId, section | validated |
| `markApproved(brandId, section)` | Mark section as approved | brandId, section | approved |

---

## Detailed Function Specs

### initBrand(brandId)

Creates a new tracking entry when brand build starts.

```javascript
// Input
{
  brandId: "acme"
}

// Process
1. Create tracking entry with all sections as "not_started"
2. Set created timestamp
3. Initialize completion at 0%

// Output
{
  created: true,
  brandId: "acme",
  tracker: {
    sections: {
      core: "not_started",
      mission: "not_started",
      values: "not_started",
      voice: "not_started",
      typography: "not_started",
      visuals: "not_started"
    },
    completion: 0,
    phase: 0,
    createdAt: "2026-01-17T14:30:00Z"
  }
}
```

### updateSection(brandId, section, status)

Updates a section's status.

```javascript
// Input
{
  brandId: "acme",
  section: "core",
  status: "validated"  // "not_started" | "in_progress" | "draft" | "pending_approval" | "validated" | "complete"
}

// Process
1. Update section status
2. Update lastChanged timestamp
3. Recalculate completion percentage
4. Check if phase can advance

// Output
{
  updated: true,
  brandId: "acme",
  section: "core",
  previousStatus: "in_progress",
  newStatus: "validated",
  completion: 17,  // percentage
  phaseAdvanced: false
}
```

### getStatus(brandId)

Gets full status report for a brand.

```javascript
// Input
{
  brandId: "acme"
}

// Output
{
  brandId: "acme",
  completion: 50,
  phase: 2,
  status: "in_progress",

  sections: {
    core: { status: "complete", validatedAt: "2026-01-17", lastChanged: "2026-01-17" },
    mission: { status: "complete", validatedAt: "2026-01-17", lastChanged: "2026-01-17" },
    values: { status: "pending_approval", lastChanged: "2026-01-17" },
    voice: { status: "draft", lastChanged: "2026-01-17" },
    typography: { status: "not_started" },
    visuals: { status: "not_started" }
  },

  summary: {
    complete: ["core", "mission"],
    inProgress: ["values", "voice"],
    notStarted: ["typography", "visuals"],
    awaitingApproval: ["values"]
  },

  canGenerate: {
    content: false,
    reason: "Missing: typography, visuals"
  }
}
```

### canProceed(brandId, requiredSections)

Checks if brand can proceed with an operation requiring specific sections.

```javascript
// Input
{
  brandId: "acme",
  requiredSections: ["core", "mission", "typography", "visuals"]
}

// Output
{
  canProceed: false,
  brandId: "acme",
  required: ["core", "mission", "typography", "visuals"],
  complete: ["core", "mission"],
  missing: ["typography", "visuals"],
  message: "Cannot proceed: missing typography, visuals"
}
```

### listIncomplete()

Lists all brands with incomplete builds.

```javascript
// Output
{
  incomplete: [
    { brandId: "acme", completion: 50, phase: 2, waitingFor: "typography" },
    { brandId: "globex", completion: 17, phase: 1, waitingFor: "core approval" },
    { brandId: "initech", completion: 83, phase: 3, waitingFor: "final validation" }
  ],
  total: 3
}
```

### listAwaitingApproval()

Lists brands with sections awaiting human approval.

```javascript
// Output
{
  awaitingApproval: [
    { brandId: "acme", section: "values", since: "2026-01-17T10:00:00Z" },
    { brandId: "globex", section: "core", since: "2026-01-16T15:30:00Z" }
  ],
  total: 2
}
```

---

## Section Statuses

| Status | Description | Next Possible |
|--------|-------------|---------------|
| `not_started` | Section hasn't been touched | `in_progress` |
| `in_progress` | Being worked on | `draft`, `validated` |
| `draft` | Content created, not validated | `pending_approval`, `validated` |
| `pending_approval` | Awaiting human approval | `validated`, `in_progress` |
| `validated` | Passed validation | `complete` |
| `complete` | Fully done and approved | - |

---

## Phases

| Phase | Description | Required Sections |
|-------|-------------|-------------------|
| 0 | Not started | - |
| 1 | Core | core |
| 2 | Identity | mission, values, voice |
| 3 | Visuals | typography, visuals |
| 4 | Complete | All sections validated |

---

## PolicyEngine Integration

PolicyEngine uses BrandTracker to check gates:

```javascript
// PolicyEngine asks BrandTracker
const canGenerate = await BrandTracker.canProceed("acme", ["core", "typography", "visuals"]);

if (!canGenerate.canProceed) {
  return {
    approved: false,
    reason: `Brand incomplete: ${canGenerate.message}`,
    blockingGate: "brand_complete"
  };
}
```

### Common Gates

| Gate | Required Sections | Used For |
|------|-------------------|----------|
| `can_generate_content` | core, mission, typography, visuals | Content generation |
| `can_create_social` | core, mission, voice | Social media posts |
| `can_export_brand` | all sections | Brand kit export |
| `can_edit_typography` | core | Typography selection |

---

## Tracker Storage

BrandTracker maintains a simple status cache:

```json
// /repos/trackers/brand-tracker.json
{
  "brands": {
    "acme": {
      "completion": 50,
      "phase": 2,
      "status": "in_progress",
      "createdAt": "2026-01-15T10:00:00Z",
      "lastUpdated": "2026-01-17T14:30:00Z",
      "sections": {
        "core": {
          "status": "complete",
          "lastChanged": "2026-01-15T12:00:00Z",
          "validatedAt": "2026-01-15T12:30:00Z"
        },
        "mission": {
          "status": "complete",
          "lastChanged": "2026-01-16T09:00:00Z",
          "validatedAt": "2026-01-16T10:00:00Z"
        },
        "values": {
          "status": "pending_approval",
          "lastChanged": "2026-01-17T14:30:00Z"
        },
        "voice": {
          "status": "draft",
          "lastChanged": "2026-01-17T11:00:00Z"
        },
        "typography": {
          "status": "not_started"
        },
        "visuals": {
          "status": "not_started"
        }
      }
    },
    "globex": {
      ...
    }
  },
  "lastSync": "2026-01-17T14:30:00Z"
}
```

---

## Example Flow

```
1. Brand build starts
   └── BrandManager → BrandTracker.initBrand("acme")
       → All sections "not_started", completion 0%

2. User provides core data
   └── BrandManager → BrandTracker.updateSection("acme", "core", "in_progress")
       → completion 8%

3. Core validated
   └── BrandManager → BrandTracker.markValidated("acme", "core")
       → core "complete", phase advances to 1, completion 17%

4. AI generates mission
   └── BrandManager → BrandTracker.updateSection("acme", "mission", "draft")
       → completion 25%

5. Mission needs approval
   └── BrandManager → BrandTracker.updateSection("acme", "mission", "pending_approval")
       → Brand appears in listAwaitingApproval()

6. User tries to generate content
   └── PolicyEngine → BrandTracker.canProceed("acme", ["typography", "visuals"])
       → { canProceed: false, missing: ["typography", "visuals"] }
       → Content generation blocked

7. All sections complete
   └── BrandTracker.getStatus("acme")
       → completion 100%, phase 4, canGenerate: true
```

---

## Config

### module.config.json

```json
{
  "module": "BrandTracker",
  "version": "1.0.0",
  "description": "Track brand build progress and validation status",
  "type": "tracker",

  "functions": [
    {
      "name": "initBrand",
      "description": "Create tracking entry for new brand",
      "input": ["brandId"],
      "output": "tracker"
    },
    {
      "name": "updateSection",
      "description": "Update section status",
      "input": ["brandId", "section", "status"],
      "output": "updated"
    },
    {
      "name": "getStatus",
      "description": "Get full brand status",
      "input": ["brandId"],
      "output": "statusReport"
    },
    {
      "name": "getSectionStatus",
      "description": "Get specific section status",
      "input": ["brandId", "section"],
      "output": "sectionStatus"
    },
    {
      "name": "canProceed",
      "description": "Check if can proceed with operation",
      "input": ["brandId", "requiredSections"],
      "output": "canProceed"
    },
    {
      "name": "listIncomplete",
      "description": "List all incomplete brands",
      "input": [],
      "output": "brandList"
    },
    {
      "name": "listAwaitingApproval",
      "description": "List brands awaiting approval",
      "input": [],
      "output": "brandList"
    },
    {
      "name": "markValidated",
      "description": "Mark section as validated",
      "input": ["brandId", "section"],
      "output": "validated"
    },
    {
      "name": "markApproved",
      "description": "Mark section as approved",
      "input": ["brandId", "section"],
      "output": "approved"
    }
  ],

  "config": {
    "trackerPath": "/repos/trackers/brand-tracker.json",
    "sections": ["core", "mission", "values", "voice", "typography", "visuals"],
    "phases": {
      "0": { "name": "not_started", "sections": [] },
      "1": { "name": "core", "sections": ["core"] },
      "2": { "name": "identity", "sections": ["mission", "values", "voice"] },
      "3": { "name": "visuals", "sections": ["typography", "visuals"] },
      "4": { "name": "complete", "sections": "all" }
    }
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading/writing tracker file |

---

## Used By

| Component | How |
|-----------|-----|
| BrandManager | Updates section status |
| PolicyEngine | Checks completion gates |
| UI | Display brand progress |

---

*Last updated: 2026-01-17*
