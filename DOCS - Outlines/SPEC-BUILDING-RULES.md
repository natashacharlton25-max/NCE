# Module Spec Building Rules

> **Purpose:** Instructions for Claude to build consistent, production-grade module specifications
> **For:** Any Claude session continuing this work
> **Version:** 1.0.0

---

## Quick Start

1. Read this file first
2. Check [MODULE-SPEC-CHECKLIST.md](MODULE-SPEC-CHECKLIST.md) for current progress and next module
3. Reference [ERROR-CODES.md](ERROR-CODES.md) for error code registry
4. Follow the process below

---

## Core Philosophy

**We are writing specs, not code.** Each spec must be complete enough that:
- Any developer could implement it without asking questions
- No undefined behavior exists
- All edge cases are documented
- Security is explicit, not assumed

**Key principles:**
- No hand-waving
- No "common sense" dependencies
- Fail fast
- Defense in depth
- Clear responsibility boundaries
- Deterministic behavior

---

## Process: Building a Module Spec

### Step 1: Gather Context

Read these files in order:

```
1. DOCS - Preplanning/DOCS - SystemPlan/Modules/[MODULENAME].md
   → Planning doc with initial function ideas

2. DOCS - StructureDefined/ROLE-MATRIX.md
   → Current function definitions for all modules

3. Any approved specs for related modules
   → e.g., Writer before FileManager, FileManager before Librarian
```

### Step 2: Identify Discrepancies

Compare planning doc vs ROLE-MATRIX. Common issues:
- Function name differences (read vs get)
- Missing functions in one source
- Unclear responsibility boundaries

**ASK the user about discrepancies.** Don't assume.

### Step 3: Draft the Spec

Create file at: `DOCS - StructureDefined/systems/[ModuleName]/[ModuleName].md`

Use the template structure below.

### Step 4: User Review

Present the draft. Expect feedback on:
- Security holes
- Undefined behavior
- Missing edge cases
- Boundary violations
- Performance considerations

### Step 5: Iterate

Address all feedback. Common additions:
- Operational Guarantees section
- Additional error codes
- Size limits
- Initialization patterns
- Platform notes

### Step 6: Approval

When user approves:
1. Update MODULE-SPEC-CHECKLIST.md (mark [x] for Spec and Approved)
2. Update ROLE-MATRIX.md if functions changed
3. Add entry to Change Log in checklist
4. Update ERROR-CODES.md if new error codes were added

---

## Error Code Management

All error codes live in [ERROR-CODES.md](ERROR-CODES.md).

**When writing a spec:**
1. Check if existing error codes fit your module's needs
2. Reuse existing codes where possible (e.g., `PATH_OUTSIDE_ALLOWED` is shared)
3. Add new codes to the registry if needed
4. List module-specific codes in the spec's Error Handling section

**Error code format:**
```
{CATEGORY}_{SPECIFIC}
```

**Error levels:**
| Level | Meaning | Default Action |
|-------|---------|----------------|
| 1 | Transient | Auto-retry |
| 2 | Recoverable | Return to caller |
| 3 | Degraded | Continue with fallback |
| 4 | Fatal | Escalate |

---

## Spec Template Structure

Every spec follows this exact structure:

```markdown
# [ModuleName] Module

> **Purpose:** [One-line description]
> **Type:** [Category] Module
> **Status:** Spec Complete
> **Version:** 1.0.0

---

## Overview

[2-3 sentences explaining what this module does]

[Simple diagram or flow if helpful]

**Kitchen Analogy:** [Relatable metaphor - we use kitchen analogies]

---

## Roles & Rules

### [ModuleName] DOES:
- [Bullet list of responsibilities]

### [ModuleName] does NOT:
- [Thing] ([OtherModule] does that)
- [Continue pattern]

### Boundaries:
- [Hard limits on what module can access/do]

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `functionName(params)` | param1, param2 | resultType | Brief description |

---

## Options Type Definitions

### [OptionType]

```javascript
{
  optionName?: type,    // default: value - explanation
}
```

---

## Detailed Function Specs

### functionName(params)

[One sentence description]

```javascript
// Input
{
  param1: "value",
  param2: { ... }
}

// Process
1. Step one
2. Step two
3. Step three

// Output (success)
{
  success: true,
  data: { ... }
}

// Output (error - specific case)
{
  success: false,
  error: {
    code: "ERROR_CODE",
    message: "Human readable message"
  }
}
```

[Repeat for each function]

---

## Configuration

```json
{
  "module": "[ModuleName]",
  "version": "1.0.0",
  "description": "[Description]",
  "type": "[category]",

  "config": {
    "setting1": "value",
    "setting2": {
      "nested": true
    }
  }
}
```

### Config Explained

| Setting | Value | Purpose |
|---------|-------|---------|
| `setting1` | value | What it does |

---

## Error Handling

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `ERROR_CODE` | When this happens | How to fix |

---

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| [What was decided] | [Yes/No/Value] | [Why] |

---

## Operational Guarantees

### 1. [Guarantee Name]

[Explanation of the guarantee]

```javascript
// Code example showing behavior
```

**Rationale:** [Why this guarantee exists]

[Repeat numbered guarantees]

---

## Constraints & Assumptions

### Platform Semantics

[What OS/filesystem assumptions are made]

### Scaling Notes

| Operation | Scaling Consideration |
|-----------|----------------------|
| `operation()` | What to watch for at scale |

---

## Used By

| Module | Function | Purpose |
|--------|----------|---------|
| **ModuleName** | function() | What they use it for |

---

## Dependencies

| Depends On | For |
|------------|-----|
| ModuleName | What we need from them |

---

*Spec version: 1.0.0*
*Created: [Date]*
*Updated: [Date] - [What changed]*
*Status: [Draft/Awaiting Approval/Approved]*
```

---

## Quality Checklist

Before presenting a spec, verify:

### Completeness
- [ ] All functions from ROLE-MATRIX documented
- [ ] All options types defined
- [ ] All error codes listed
- [ ] Input/Output for every function
- [ ] Used By section populated
- [ ] Dependencies listed

### Security
- [ ] Path validation documented (if applicable)
- [ ] Symlink policy stated (if file operations)
- [ ] Size limits defined (if reading/writing)
- [ ] Boundary violations handled

### Clarity
- [ ] No undefined behavior
- [ ] No "should" or "might" - use "does" or "will"
- [ ] Every edge case has explicit handling
- [ ] Error recovery is clear

### Consistency
- [ ] Matches related module patterns (Writer ↔ FileManager symmetry)
- [ ] Error code naming is consistent
- [ ] Config structure matches other specs
- [ ] Version format is X.Y.Z

---

## Common Patterns

### Initialization Pattern

Modules that need startup validation should have:

```javascript
initialize()
// Called once at system startup
// Validates configuration, paths, connections
// Sets ready state
// All other functions check ready state first

// Error if called before ready:
{
  success: false,
  error: {
    code: "MODULE_NOT_READY",
    message: "[Module] not initialized"
  }
}
```

### Size Limit Pattern

For modules handling variable-size data:

```javascript
config: {
  sizeLimits: {
    warnAt: 10485760,      // 10MB - log warning
    rejectAt: 104857600,   // 100MB - reject operation
    bypassable: true       // Allow override with option
  }
}

// Option to bypass
{
  bypassSizeLimit: true,
  reason: "Required for migration"  // Mandatory if bypassing
}

// Error code
FILE_SIZE_EXCEEDED
```

### Path Safety Pattern

For modules accessing filesystem:

```javascript
config: {
  pathSafety: {
    validatePaths: true,
    blockTraversal: true,      // Block .. attacks
    resolveSymlinks: true      // Follow but validate resolved path
  }
}

// Internal function
validatePath(path)
// 1. Normalize path
// 2. Check for traversal
// 3. Resolve symlinks
// 4. Verify resolved path in allowed roots

// Error codes
PATH_TRAVERSAL_DETECTED
PATH_OUTSIDE_ALLOWED
```

### Result Pattern

All functions return structured results:

```javascript
// Success
{
  success: true,
  // ... result data
}

// Failure
{
  success: false,
  error: {
    code: "CONSTANT_CASE_ERROR",
    message: "Human readable description",
    // ... additional context
  }
}
```

---

## Module Relationships

Understanding these relationships helps maintain consistency:

### Data Management Layer

```
Writer (write) ←→ FileManager (read)
    ↓                  ↓
    └──── Archivist ────┘  (validation workflow)
              ↓
         Librarian (discovery/search)
              ↓
        CacheHandler (caching layer)
```

### Key Symmetries

| Writer | FileManager |
|--------|-------------|
| write() | get() |
| remove() | exists() |
| Atomic writes | Relies on atomic writes |
| Size limits (50MB) | Size limits (100MB) |
| Path validation | Path validation |
| Symlink resolution | Symlink resolution |
| Advisory locking | No locking (reads) |

---

## Approved Specs Reference

When building new specs, reference these approved patterns:

| Module | Key Patterns to Copy |
|--------|---------------------|
| **Writer** | Operational Guarantees structure, lock ordering, atomic writes, dry run mode |
| **FileManager** | Options types, exists with stats, initialize pattern, schema boundary |

---

## Common Review Feedback

Expect these issues to be raised:

1. **"What if the file is huge?"** → Add size limits
2. **"What about symlinks?"** → Add symlink policy
3. **"What happens during startup?"** → Add initialize()
4. **"What if two processes do this?"** → Add concurrency notes
5. **"This crosses into [OtherModule]'s job"** → Clarify boundaries
6. **"What order are results in?"** → Add ordering guarantees
7. **"What about Windows?"** → Add platform notes

---

## Don't Do These Things

- Don't start writing specs without reading planning docs first
- Don't assume - ask about discrepancies
- Don't add features beyond what's in ROLE-MATRIX
- Don't skip the Operational Guarantees section
- Don't use vague language ("should", "might", "usually")
- Don't leave error cases undefined
- Don't forget to update ROLE-MATRIX after changes

---

## Session Handoff

When ending a session:

1. Ensure MODULE-SPEC-CHECKLIST.md is updated
2. Note which module was in progress (if any)
3. List any open questions for user

When starting a session:

1. Read this file
2. Read MODULE-SPEC-CHECKLIST.md
3. Ask user: "Ready to continue with [NextModule]?"

---

*Created: 2026-01-18*
*For: Module specification process documentation*
