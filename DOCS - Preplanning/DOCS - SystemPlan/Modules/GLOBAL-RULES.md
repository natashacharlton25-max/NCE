# Global Rules

> **Purpose:** System-wide standards for all modules
> **Last updated:** 2026-01-17

---

## Module Documentation Standard

Every module MUST document these sections:

### 1. BEFORE (Prerequisites)
What must happen BEFORE this module runs:
- What inputs are required?
- What paths need to exist?
- What modules must run first?
- What checks must pass?

### 2. DURING (Execution)
What happens DURING module execution:
- Step-by-step process
- What data is read/written?
- What outputs are produced?

### 3. AFTER (Post-execution)
What happens AFTER this module completes:
- What modules run next?
- What gets logged?
- What cleanup happens?
- What triggers on success/failure?

### 4. RULES (Boundaries)
What this module CAN and CANNOT do:
- Permissions and boundaries
- What it DOES (responsibilities)
- What it does NOT do (out of scope)
- Error handling approach

---

## Module Template

```markdown
# {Module} Module

> **Purpose:** One-line description
> **Type:** Service | System Service | Pipeline | Integration
> **Status:** Planning | Active | Deprecated

---

## Overview

Brief description of what this module does.

---

## Before → During → After

### BEFORE (Prerequisites)
- [ ] PolicyEngine.canRun() approved
- [ ] Builders.createOutputPath() completed
- [ ] Required inputs available: {list}
- [ ] Previous steps completed: {list}

### DURING (Execution)
1. Step one
2. Step two
3. Step three

### AFTER (Completion)
- On SUCCESS: {what happens}
- On FAILURE: {what happens}
- Logger receives: {what data}
- Next steps: {what modules run}

---

## Rules

### DOES:
- Responsibility 1
- Responsibility 2

### Does NOT:
- Out of scope 1
- Out of scope 2

### Boundaries:
- Cannot do X
- Cannot do Y

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| ... | ... | ... | ... |

---

## Config

### module.config.json
{config block}

---

## Dependencies / Used By

...
```

---

## Core System Principles

### 1. Pass References, Not Data
- Modules receive paths/IDs, not actual data
- Each module fetches what it needs
- Lean payloads, no data duplication

### 2. Single Responsibility
- Each module does ONE thing well
- Clear boundaries between modules
- No overlapping responsibilities

### 3. Kitchen Analogy
- **Orchestrator** = Head Chef (coordinates)
- **Modules** = Stations (independent)
- **Repos** = Pantry (data storage)
- **FailureHandler** = Manager (handles problems)

### 4. 4-Level Failure Escalation
| Level | Problem | Handler | Solution |
|-------|---------|---------|----------|
| 1 | Transient | Retry | Wait and retry |
| 2 | Missing data | Librarian | Find alternative |
| 3 | No alternative | Self-healing | Create new entry |
| 4 | Novel decision | Human | Queue for review |

### 5. Execution Flow
```
1. PolicyEngine.canRun() → "Can this run?"
2. Builders.createOutputPath() → Create paths
3. Module executes → Save to path
4. Logger.addToJobLog() → Log step
5. On failure → FailureHandler → Builders.deletePath()
```

---

## Naming Conventions

### Job IDs
```
job_{YYYYMMDD}_{HHMMSS}_{chain}_{random}
Example: job_20260117_143052_brandkit_abc123
```

### Output Paths
```
/jobs/temp/{jobId}_step{N}_{module}_{function}.json
Example: /jobs/temp/job_20260117_143052_step3_aicaller_response.json
```

### Entry IDs
```
{kebab-case-name}
Example: formal-corporate, business-professional
```

---

## Error Codes

### Format
```
{CATEGORY}_{SPECIFIC}
```

### Categories
| Prefix | Category | Level |
|--------|----------|-------|
| `API_` | API/Network errors | 1 (transient) |
| `NOT_FOUND_` | Missing data | 2-4 (depends) |
| `VALIDATION_` | Validation errors | 2 |
| `AUTH_` | Authentication | 4 (human) |
| `BUDGET_` | Budget/cost | 4 (human) |
| `SYSTEM_` | System errors | 4 (human) |
| `PATH_` | Path errors | 1-2 |

---

## Data Classification (Three Tiers)

| Tier | Type | If Missing | Examples |
|------|------|------------|----------|
| 1 | Required - No Substitute | Auto-fail to human | brand.name, client.id, legal.* |
| 2 | Required - Can Find Alternative | Ask Librarian | tone, voice, style, values |
| 3 | Optional - Can Be Created | Self-heal via AI | writingRules, formatGuidelines |

---

## Module Communication

```
Modules never call each other directly.

✓ Module → Orchestrator → Next Module
✗ Module → Module

Exception: Utility modules (FileManager, Writer, Logger)
```

---

*Last updated: 2026-01-17*
