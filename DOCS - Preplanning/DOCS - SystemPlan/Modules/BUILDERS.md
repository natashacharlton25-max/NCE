# Builders Module

> **Purpose:** Scaffold structures, manage output paths, construct data from templates
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

Builders creates and manages structures. This includes:
1. **Output paths** - Created BEFORE chain steps run, so modules have somewhere to save
2. **Brand scaffolding** - Folder structures for new brands
3. **Entry scaffolding** - New repo entries from templates

```
Chain starts → Builders.createOutputPath() → Path ready
Module runs  → Saves to path
On failure   → FailureHandler → Builders.deletePath()
Path error   → FailureHandler → Builders.createNewPath()
```

---

## Roles & Rules

### Builders DOES:
- Create output paths BEFORE modules execute
- Delete paths when jobs fail (cleanup)
- Validate paths exist and are writable
- Report path issues to Logger (empty, broken, used)
- Scaffold brand folder structures
- Create new repo entries from templates
- Generate IDs from names

### Builders does NOT:
- Execute modules (Orchestrator does that)
- Write actual content to paths (modules do that)
- Decide when to create/delete (told by Orchestrator/FailureHandler)
- Handle failure logic (FailureHandler does that)

### Boundaries:
- Cannot modify existing files (only create/delete paths)
- Cannot create paths outside configured directories
- Cannot delete paths that have content without explicit request

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `createOutputPath(jobId, step, module)` | Create path before module runs | jobId, step, module | pathInfo |
| `deletePath(path, reason)` | Delete path on failure | path, reason | deleted |
| `validatePath(path)` | Check path is valid/writable | path | validation |
| `reportPathIssues(jobId)` | Report empty/broken paths to Logger | jobId | report |
| `scaffoldBrand(brandId, template)` | Create brand folder structure | brandId, template | scaffolded |
| `scaffoldEntry(repo, template, data)` | Create new repo entry structure | repo, template, data | entry |
| `buildFromTemplate(templateId, data)` | Build data from template | templateId, data | built |
| `mergeDefaults(data, defaults)` | Merge data with default values | data, defaults | merged |
| `generateId(prefix, name)` | Generate kebab-case ID | prefix, name | id |

---

## Detailed Function Specs

### createOutputPath(jobId, step, module)

**KEY FUNCTION** - Creates output path BEFORE a module executes.

```javascript
// Input (from Orchestrator before running step)
{
  jobId: "job_20260117_143052_brandkit_abc",
  step: 3,
  module: "AICaller",
  pathType: "temp"  // "temp" or "permanent"
}

// Process
1. Generate path based on jobId + step + module
2. Create parent directories if needed
3. Validate path is writable
4. Track path for this job (for cleanup later)

// Output
{
  success: true,
  path: "/jobs/temp/job_20260117_143052_step3_aicaller_response.json",
  pathType: "temp",
  jobId: "job_20260117_143052_brandkit_abc",
  step: 3,
  module: "AICaller",
  created: "2026-01-17T14:31:00Z"
}

// On error
{
  success: false,
  error: {
    code: "PATH_CREATE_FAILED",
    message: "Cannot create directory: permission denied",
    path: "/jobs/temp/..."
  }
}
```

### deletePath(path, reason)

Deletes path when job fails (cleanup).

```javascript
// Input (from FailureHandler)
{
  path: "/jobs/temp/job_20260117_143052_step3_aicaller_response.json",
  reason: "job_failed"  // "job_failed", "step_failed", "manual_cleanup"
}

// Process
1. Check path exists
2. Check path is in allowed directories
3. Delete file
4. Log deletion via Logger

// Output
{
  deleted: true,
  path: "/jobs/temp/job_20260117_143052_step3_aicaller_response.json",
  reason: "job_failed",
  logged: true
}

// If path has content and needs explicit approval
{
  deleted: false,
  requiresApproval: true,
  hasContent: true,
  contentSize: 4523,
  message: "Path has content - explicit deletion required"
}
```

### validatePath(path)

Checks if path is valid and writable.

```javascript
// Input
"/jobs/temp/job_20260117_143052_step3_aicaller_response.json"

// Output (valid)
{
  valid: true,
  path: "/jobs/temp/...",
  exists: false,
  parentExists: true,
  writable: true
}

// Output (invalid)
{
  valid: false,
  path: "/jobs/temp/...",
  error: "parent_not_found",
  message: "Parent directory does not exist"
}
```

### reportPathIssues(jobId)

Reports path issues to Logger. Called periodically or on job completion.

```javascript
// Input
"job_20260117_143052_brandkit_abc"

// Process
1. Get all paths created for this job
2. Check each path:
   - Empty (created but never written to)
   - Broken (no longer accessible)
   - Orphaned (job finished but path not cleaned up)
3. Report to Logger

// Output
{
  jobId: "job_20260117_143052_brandkit_abc",
  totalPaths: 6,
  issues: [
    {
      path: "/jobs/temp/job_20260117_143052_step4_parser_output.json",
      issue: "empty",
      message: "Path created but never written to"
    }
  ],
  healthy: 5,
  reported: true
}
```

### scaffoldBrand(brandId, template)

Creates folder structure for new brand.

```javascript
// Input
{
  brandId: "acme",
  template: "standard"  // or "minimal", "full"
}

// Process
1. Load template structure
2. Create brand folder
3. Create subfolders
4. Create initial config files

// Output
{
  success: true,
  brandId: "acme",
  structure: {
    root: "/repos/brands/acme/",
    folders: [
      "/repos/brands/acme/assets/",
      "/repos/brands/acme/exports/",
      "/repos/brands/acme/content/"
    ],
    files: [
      "/repos/brands/acme/brand.config.json"
    ]
  }
}
```

### scaffoldEntry(repo, template, data)

Creates new repo entry from template.

```javascript
// Input
{
  repo: "tones",
  template: "tone-basic",
  data: {
    name: "Friendly Corporate",
    domain: "business",
    style: "warm but professional"
  }
}

// Process
1. Load template for repo
2. Merge data with template defaults
3. Generate ID from name
4. Add required fields (created, version, status)

// Output
{
  id: "friendly-corporate",
  name: "Friendly Corporate",
  domain: "business",
  style: "warm but professional",
  vocabulary: [],  // from template default
  avoid: [],       // from template default
  created: "2026-01-17",
  updated: "2026-01-17",
  status: "draft",
  version: "1.0"
}
```

### generateId(prefix, name)

Generates kebab-case ID from name.

```javascript
// Input
{
  prefix: "tone",  // optional
  name: "Friendly Corporate"
}

// Process
1. Lowercase
2. Replace spaces with hyphens
3. Remove special characters
4. Add prefix if provided

// Output
"friendly-corporate"
// or with prefix: "tone-friendly-corporate"
```

---

## Path Management Flow

```
1. Chain starts
   → Orchestrator tells Builders: createOutputPath for each step

2. Before each step
   → Builders.createOutputPath(jobId, step, module)
   → Path created and tracked

3. Module executes
   → Uses path from Builders
   → Saves output to path

4. On success
   → Logger reads from path
   → Path marked as "used"

5. On failure
   → FailureHandler tells Builders: deletePath()
   → Or: FailureHandler asks Builders: createNewPath() for retry

6. Job completes
   → Builders.reportPathIssues(jobId)
   → Cleanup empty/orphan paths
```

---

## Path Naming Convention

```
/jobs/temp/{jobId}_step{N}_{module}_{function}.json

Examples:
/jobs/temp/job_20260117_143052_step1_promptbuilder_compile.json
/jobs/temp/job_20260117_143052_step2_modelmanager_select.json
/jobs/temp/job_20260117_143052_step3_aicaller_response.json
```

---

## Templates

Templates stored in `/templates/`:

```
/templates/
  brand/
    standard.json       # Standard brand folder structure
    minimal.json        # Minimal structure
  entries/
    tone-basic.json     # Basic tone entry template
    prompt-basic.json   # Basic prompt template
  responses/
    brand-intro.json    # Brand intro response structure
```

### Example Template

```json
{
  "id": "tone-basic",
  "name": "Basic Tone Template",
  "repo": "tones",
  "structure": {
    "id": "{{generated}}",
    "name": "{{name}}",
    "style": "{{style}}",
    "domain": "{{domain|default:general}}",
    "vocabulary": [],
    "avoid": [],
    "examples": [],
    "status": "draft",
    "version": "1.0"
  },
  "required": ["name", "style"],
  "optional": ["domain", "vocabulary", "avoid"]
}
```

---

## Error Handling

| Error | Handling |
|-------|----------|
| `PATH_CREATE_FAILED` | Report to FailureHandler, try alternative location |
| `PATH_NOT_WRITABLE` | Report to FailureHandler |
| `PATH_OUTSIDE_ALLOWED` | Reject immediately (security) |
| `PATH_DELETE_HAS_CONTENT` | Require explicit approval |
| `TEMPLATE_NOT_FOUND` | Report to FailureHandler |

---

## Config

### module.config.json

```json
{
  "module": "Builders",
  "version": "2.0.0",
  "description": "Scaffold structures, manage output paths",
  "type": "system-service",

  "functions": [
    {
      "name": "createOutputPath",
      "description": "Create path before module runs",
      "input": ["jobId", "step", "module"],
      "output": "pathInfo"
    },
    {
      "name": "deletePath",
      "description": "Delete path on failure",
      "input": ["path", "reason"],
      "output": "deleted"
    },
    {
      "name": "validatePath",
      "description": "Check path is valid/writable",
      "input": ["path"],
      "output": "validation"
    },
    {
      "name": "reportPathIssues",
      "description": "Report empty/broken paths to Logger",
      "input": ["jobId"],
      "output": "report"
    },
    {
      "name": "scaffoldBrand",
      "description": "Create brand folder structure",
      "input": ["brandId", "template"],
      "output": "scaffolded"
    },
    {
      "name": "scaffoldEntry",
      "description": "Create new repo entry structure",
      "input": ["repo", "template", "data"],
      "output": "entry"
    },
    {
      "name": "buildFromTemplate",
      "description": "Build data from template",
      "input": ["templateId", "data"],
      "output": "built"
    },
    {
      "name": "mergeDefaults",
      "description": "Merge data with default values",
      "input": ["data", "defaults"],
      "output": "merged"
    },
    {
      "name": "generateId",
      "description": "Generate kebab-case ID",
      "input": ["prefix", "name"],
      "output": "id"
    }
  ],

  "config": {
    "templatesPath": "/templates/",
    "tempPath": "/jobs/temp/",
    "allowedDirectories": ["/jobs/", "/repos/"],
    "defaultStatus": "draft",
    "defaultVersion": "1.0",
    "trackPaths": true,
    "autoCleanupEmpty": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Loading templates, checking paths |
| Writer | Creating files/folders |
| Logger | Reporting path issues |

---

## Used By

| Module | How |
|--------|-----|
| Orchestrator | Creates paths before each step |
| FailureHandler | Deletes paths on failure, requests new paths |
| Self-healing | Creating missing entries (Level 3) |
| BrandKit | Scaffolding new brands |

---

*Last updated: 2026-01-17*
