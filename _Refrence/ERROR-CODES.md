# Error Code Registry

> **Purpose:** Central registry of all system error codes
> **For:** All modules, FailureHandler, monitoring, documentation
> **Version:** 1.9.0

---

## Format

```
{CATEGORY}_{SPECIFIC}
```

**Examples:**
- `PATH_OUTSIDE_ALLOWED` - Path category, specific: outside allowed roots
- `API_RATE_LIMIT` - API category, specific: rate limited
- `LOCK_TIMEOUT` - Lock category, specific: timeout waiting

---

## Error Levels

| Level | Name | Meaning | Default Action |
|-------|------|---------|----------------|
| 1 | Transient | Temporary, will likely succeed on retry | Auto-retry |
| 2 | Recoverable | Can be fixed with different input/approach | Return to caller |
| 3 | Degraded | Partial success possible | Continue with fallback |
| 4 | Fatal | Cannot proceed, human intervention needed | Escalate |

---

## Categories

### PATH_ (Path/Filesystem Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `PATH_OUTSIDE_ALLOWED` | 4 | Resolved path not in allowed roots | Security rejection - none | Writer, FileManager |
| `PATH_TRAVERSAL_DETECTED` | 4 | Path contains `..` after normalization | Security rejection - none | Writer, FileManager |
| `PATH_CREATE_FAILED` | 2 | Cannot create parent directory | Check permissions | Writer |
| `PATH_NOT_FOUND` | 2 | Directory path doesn't exist | Check path | FileManager |

---

### FILE_ (File Operation Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `FILE_SIZE_EXCEEDED` | 2 | File larger than configured limit | Use `bypassSizeLimit` option | Writer, FileManager |
| `FILE_EMPTY` | 2 | Zero-byte file detected | Check for corruption | FileManager |
| `FILE_NOT_FOUND` | 2 | File doesn't exist at path | Use `exists()` first | FileManager |
| `FILE_CORRUPTED` | 3 | File exists but unreadable/invalid | Restore from backup | FileManager |

---

### ENTRY_ (Data Entry Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `ENTRY_NOT_FOUND` | 2 | Entry ID not found in repo | Use Librarian to discover alternatives | FileManager |
| `ENTRY_INVALID` | 2 | Entry fails schema validation | Fix data and retry | Archivist |
| `ENTRY_DUPLICATE` | 2 | Entry ID already exists | Use different ID or update existing | Archivist |
| `ENTRY_LOCKED` | 1 | Entry is locked for editing | Retry after lock released | Archivist |

---

### REPO_ (Repository Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `REPO_NOT_FOUND` | 4 | Repo name not in registry | Check repo name in config | FileManager |
| `REPO_UNREADABLE` | 4 | Repo path exists but not readable | Check permissions | FileManager |
| `REPO_EMPTY` | 2 | Repo has no entries | Expected for new repos | Librarian |

---

### SCHEMA_ (Schema/Validation Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `SCHEMA_NOT_FOUND` | 4 | Schema file doesn't exist | Check schema name | FileManager |
| `SCHEMA_INVALID` | 4 | Schema file is malformed | Fix schema file | FileManager |
| `SCHEMA_VALIDATION_FAILED` | 2 | Data doesn't match schema | Fix data per error details | FileManager, Archivist |

---

### JSON_ (JSON Parsing Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `JSON_PARSE_ERROR` | 2 | File contains invalid JSON | Fix file manually, use `raw: true` to inspect | FileManager |
| `JSON_STRINGIFY_ERROR` | 2 | Object cannot be serialized | Check for circular refs, BigInt | Writer |

---

### LOCK_ (File Locking Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `LOCK_TIMEOUT` | 1 | Could not acquire lock in time | Retry later | Writer |
| `LOCK_STALE_RECOVERED` | 0 | Broke stale lock from dead process | Informational - operation proceeds | Writer |
| `LOCK_DEADLOCK_DETECTED` | 4 | Circular lock dependency | Bug - investigate | Writer |

**Note:** `LOCK_STALE_RECOVERED` is Level 0 (informational) - not an error, just a warning that stale lock was cleaned.

---

### DISK_ (Disk/Storage Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `DISK_FULL` | 4 | No space left on device | Free space, human escalation | Writer |
| `DISK_SPACE_LOW` | 2 | Pre-flight check: insufficient space | Free space or write smaller | Writer |
| `DISK_READ_ERROR` | 3 | OS-level read failure | Check disk health | FileManager |
| `DISK_WRITE_ERROR` | 3 | OS-level write failure | Check disk health | Writer |

---

### PERMISSION_ (Access Control Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `PERMISSION_DENIED` | 4 | OS-level permission error | Human escalation | Writer, FileManager |
| `PERMISSION_READ_DENIED` | 4 | Cannot read file/directory | Check file permissions | FileManager |
| `PERMISSION_WRITE_DENIED` | 4 | Cannot write to path | Check directory permissions | Writer |

---

### MODULE_ (Module Lifecycle Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `MODULE_NOT_READY` | 1 | Called before initialize() completed | Wait for initialization | Writer, FileManager |
| `MODULE_DISABLED` | 3 | Module is disabled in config | Enable in config or use fallback | Any |
| `MODULE_CONFIG_INVALID` | 4 | Module configuration is invalid | Fix config | Any |

---

### INIT_ (Initialization Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `INIT_ALREADY_RUNNING` | 0 | Another initialize() in progress | Safe to ignore | Writer |
| `INIT_FAILED` | 4 | Initialization failed critically | Check logs, fix config | Any |
| `INIT_PARTIAL` | 3 | Initialization completed with warnings | Review warnings | FileManager |

---

### TEMPLATE_ (Template Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `TEMPLATE_NOT_FOUND` | 2 | Template ID not found | Check template ID | Templates |
| `TEMPLATE_INVALID` | 2 | Template fails validation | Fix template | Templates |
| `TEMPLATE_CIRCULAR_INHERIT` | 4 | Circular inheritance detected | Fix template hierarchy | Templates |
| `TEMPLATE_COMPILE_ERROR` | 2 | Template compilation failed | Check template syntax | Templates |

---

### CACHE_ (Caching Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `CACHE_MISS` | 0 | Entry not in cache | Normal - fetch from source | CacheHandler |
| `CACHE_EXPIRED` | 0 | Cache entry expired | Normal - refresh | CacheHandler |
| `CACHE_CORRUPTED` | 2 | Cache entry is corrupted | Evict and refetch | CacheHandler |
| `CACHE_FULL` | 2 | Cache at capacity | Evict entries per policy | CacheHandler |

---

### INDEX_ (Search Index Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `INDEX_NOT_FOUND` | 2 | Search index doesn't exist | Rebuild index | Librarian |
| `INDEX_STALE` | 2 | Index is out of date | Rebuild index | Librarian |
| `INDEX_CORRUPTED` | 2 | Index is corrupted | Delete and rebuild | Librarian |
| `INDEX_BUILD_FAILED` | 3 | Could not build index | Check source data | Librarian |

---

### SEARCH_ (Search Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `SEARCH_NO_RESULTS` | 0 | Search returned no matches | Normal - try different terms | Librarian |
| `SEARCH_QUERY_INVALID` | 2 | Query syntax is invalid | Fix query | Librarian |
| `SEARCH_TIMEOUT` | 1 | Search took too long | Retry with narrower scope | Librarian |

---

### API_ (External API Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `API_TIMEOUT` | 1 | Request timed out | Retry | AICaller, Integrations |
| `API_RATE_LIMIT` | 1 | Rate limited by provider | Retry with backoff | AICaller, Integrations |
| `API_5XX` | 1 | Server error from provider | Retry | AICaller, Integrations |
| `API_4XX` | 2 | Client error (bad request) | Check request parameters | AICaller, Integrations |
| `API_AUTH_FAILED` | 4 | Authentication failed | Check API keys | AICaller, Integrations |
| `API_QUOTA_EXCEEDED` | 4 | Usage quota exceeded | Human escalation | AICaller, Integrations |

---

### TOKEN_ (Token/AI Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `TOKEN_LIMIT_EXCEEDED` | 2 | Content exceeds token limit | Reduce content or chunk | TokenManager |
| `TOKEN_COUNT_FAILED` | 2 | Could not count tokens | Use estimate | TokenManager |
| `TOKEN_BUDGET_EXCEEDED` | 3 | Token budget for job exceeded | Stop or get approval | CostBudgetManager |

---

### PROMPT_ (Prompt Building Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `PROMPT_COMPILE_ERROR` | 2 | Prompt template compilation failed | Fix template | PromptBuilder |
| `PROMPT_MISSING_VAR` | 2 | Required variable not provided | Provide missing variable | PromptBuilder |
| `PROMPT_TOO_LONG` | 2 | Compiled prompt exceeds limit | Reduce content | PromptBuilder |

---

### JOB_ (Job/Orchestration Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `JOB_NOT_FOUND` | 2 | Job ID not found | Check job ID | Orchestrator |
| `JOB_ALREADY_COMPLETE` | 0 | Job already finished | Informational | Orchestrator |
| `JOB_CANCELLED` | 0 | Job was cancelled | Informational | Orchestrator |
| `JOB_TIMEOUT` | 3 | Job exceeded time limit | Review and retry | Orchestrator |
| `JOB_DEPENDENCY_FAILED` | 3 | Dependent job failed | Fix dependency | Orchestrator |

---

### CHAIN_ (Chain Execution Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `CHAIN_STEP_FAILED` | 3 | Step in chain failed | Check step error | Orchestrator |
| `CHAIN_VALIDATION_FAILED` | 2 | Chain input/output validation failed | Fix data | Orchestrator |
| `CHAIN_LOOP_DETECTED` | 4 | Circular chain reference | Fix chain definition | Orchestrator |

---

### BRAND_ (Brand Data Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `BRAND_NOT_FOUND` | 2 | Brand ID not found | Use Librarian | BrandManager, ThemeGenerator |
| `BRAND_INCOMPLETE` | 2 | Brand profile missing required data | Complete brand profile | BrandManager |
| `BRAND_NAME_MISSING` | 4 | Brand has no name defined | Human escalation | BrandManager |

---

### AUDIENCE_ (Audience Data Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `AUDIENCE_NOT_FOUND` | 2 | Audience ID not found | Check audienceId or omit | AudienceManager, ThemeGenerator, TemplateEngine |

---

### THEME_ (Theme Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `THEME_NOT_FOUND` | 2 | Theme ID not found | Check themeId or generate new | ThemeGenerator, TemplateEngine |
| `THEME_ALREADY_EXISTS` | 2 | Theme with same ID exists | Use different name or set allowDuplicate: true | ThemeGenerator |
| `THEME_INVALID_DERIVATION` | 2 | Can't derive broader scope from narrower | Use a broader theme as source | ThemeGenerator |
| `THEME_INVALID_RATING` | 2 | Rating score not 1-5 | Provide valid score | ThemeGenerator |
| `THEME_INVALID_UPDATE` | 2 | Tried to update immutable field | Can't change id, createdAt, createdBy | ThemeGenerator |
| `THEME_HAS_DEPENDENTS` | 2 | Theme has derived themes (hard delete) | Archive instead or delete dependents first | ThemeGenerator |
| `THEME_INVALID_STRUCTURE` | 2 | Theme missing required fields | Check theme object structure | ThemeGenerator |

---

### DATABASE_ (Database Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `DATABASE_ERROR` | 3 | D1 statement execution failed (read/write/batch) | Surface to caller; FailureHandler may retry | DatabaseHandler, ThemeGenerator |
| `DATABASE_INIT_FAILED` | 4 | Could not initialize/access the D1 binding | Check binding/config | DatabaseHandler, ThemeGenerator |
| `DATABASE_MIGRATION_FAILED` | 4 | Schema migration failed to apply | Check migration; halt | DatabaseHandler, ThemeGenerator |
| `DATABASE_QUERY_INVALID` | 2 | Unparameterized statement, or unknown table/column in query builder | Use bound params; fix table/column | DatabaseHandler |
| `DATABASE_MIGRATION_CONFLICT` | 4 | Applied migration's checksum changed (drift) | Halt for review; do not re-run | DatabaseHandler |

---

### AI_ (AI Generation Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `AI_GENERATION_FAILED` | 3 | AI failed to generate content | Check AICaller logs, retry | ThemeGenerator, TemplateEngine |

---

### CONTENT_ (Content Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `CONTENT_TOO_SHORT` | 2 | Generated content below minimum | Regenerate with longer target | Content |
| `CONTENT_TOO_LONG` | 2 | Generated content exceeds maximum | Truncate or regenerate | Content |
| `CONTENT_QUALITY_LOW` | 3 | Content failed quality check | Regenerate or human review | ContentTextAssessor |
| `CONTENT_OFF_BRAND` | 3 | Content doesn't match brand voice | Regenerate with brand context | ContentAlignment |

---

### GOVERNANCE_ (Truth/Angle Governance Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `GOVERNANCE_TRUTH_REVIEW_REQUIRED` | 1 | Truth requires human approval before use | Submit for review or use trusted brand | ThemeGenerator |
| `GOVERNANCE_TRUTH_FLAGGED` | 1 | Truth flagged for poor performance | Review and refine truth | ThemeGenerator |
| `GOVERNANCE_ANGLE_LOCKED` | 1 | Angle is locked and cannot be regenerated | Use existing angle or unlock | ThemeGenerator |
| `GOVERNANCE_APPROVAL_MODE_INVALID` | 2 | Brand truthApprovalMode not recognized | Check brand config (strict/guided/trusted) | ThemeGenerator, BrandManager |
| `GOVERNANCE_ANGLE_CONTRADICTS_VALUES` | 2 | Generated angle conflicts with brand values | Regenerate angle or review brand values | ThemeGenerator |

---

### OUTCOME_ (Outcomes Library Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `OUTCOME_NOT_FOUND` | 2 | Outcome ID not found in library | Check outcome ID or omit target_outcomes | ThemeGenerator |
| `OUTCOME_PERSPECTIVE_MISSING` | 1 | Outcome lacks natasha_perspective field | Add perspective to outcome or generate without | ThemeGenerator |
| `OUTCOME_DIVERGENT` | 1 | Target outcomes have different outcomeTheme values | Set allowDivergentOutcomes: true or narrow focus | ThemeGenerator |
| `OUTCOME_LIMIT_EXCEEDED` | 2 | Too many outcomes selected (max 3 for theme) | Reduce target_outcomes count | ThemeGenerator |

---

### TRANSFORMATION_ (Transformation Framework Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `TRANSFORMATION_CONTEXT_INCOMPLETE` | 2 | Transformation framework missing required data | Provide complete transformation context | ThemeGenerator |
| `TRANSFORMATION_BELIEF_SHIFT_UNAVAILABLE` | 2 | No belief_shift defined for selected outcomes | Add belief_shift to outcomes or generate without | ThemeGenerator |

---

### THEME_ (Theme Validation Errors - v1.7.0)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `THEME_VISUAL_KEYWORDS_INSUFFICIENT` | 1 | Not enough concrete/visual keywords for ImageBrowser | Regenerate with visualKeywordMin or add manually | ThemeGenerator |
| `THEME_TRUTHS_OUTCOME_MISALIGNMENT` | 2 | Generated truths don't support target outcomes | Regenerate with outcome-aligned truths | ThemeGenerator |
| `THEME_TRUTHS_DONT_FACILITATE_SHIFT` | 2 | Truths can't drive required belief transformation | Review transformation framework and truths | ThemeGenerator |
| `THEME_PERSPECTIVE_MISMATCH` | 1 | Theme perspective doesn't match brand natasha_perspective | Check natasha_perspective alignment | ThemeGenerator |

---

### OUTLINE_ (Outline Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `OUTLINE_NOT_FOUND` | 2 | Outline ID not found | Check outlineId or omit | OutlineGenerator, TemplateEngine |
| `OUTLINE_ALREADY_EXISTS` | 2 | Active outline exists for theme+template | Use existing or regenerate | OutlineGenerator |
| `OUTLINE_TRUTH_DEPENDENCY_VIOLATION` | 2 | Truth selected without required dependency | Include dependent truth or remove selection | OutlineGenerator |
| `OUTLINE_SECTION_INDEX_OUT_OF_BOUNDS` | 2 | regenerateSection() index invalid | Check section count | OutlineGenerator |
| `OUTLINE_PRIMARY_TRUTH_UNDERREPRESENTED` | 1 | Primary truth in <50% of body sections | Redistribute truths or accept as-is | OutlineGenerator |
| `OUTLINE_TEMPLATE_DRIFT_DETECTED` | 2 | Template structure changed since outline generation | Regenerate outline or use archived template | OutlineGenerator |
| `OUTLINE_COGNITIVE_TYPE_INCOMPATIBLE` | 1 | Cognitive type doesn't suit template structure | Use different type or proceed with caution | OutlineGenerator |
| `OUTLINE_SECTION_COUNT_MISMATCH` | 2 | Generated sections don't match template | Regenerate or check template | OutlineGenerator |

---

### CROSS_FS_ (Cross-Filesystem Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `CROSS_FS_MOVE_FAILED` | 3 | Cross-filesystem move failed | Check both filesystems | Writer |
| `CROSS_FS_SOURCE_ORPHAN` | 2 | Move succeeded but source deletion failed | Manual cleanup of source | Writer |

---

### LOG_ (Logging Errors)

| Code | Level | Description | Recovery | Used By |
|------|-------|-------------|----------|---------|
| `LOG_ENTRY_INVALID` | 2 | Log entry has unknown level, empty message, or job-type without job_id | Fix entry shape; logging degrades, never fails caller | Logger |
| `LOG_FORBIDDEN_FIELD` | 2 | Forbidden field (secret/PII/full-content) detected in entry | Field redacted/dropped automatically | Logger |
| `LOG_JOB_FINALIZED` | 2 | Step appended to an already-finalized job log | Written standalone, not folded into summary | Logger |

**Note:** Logger handles all its own errors under the silent-degrade invariant —
they are recorded best-effort (dual-write to Workers Logs) and never propagated to
the caller or routed to FailureHandler (avoids re-entrancy). See Logger Spec.md.

---

## Reserved Prefixes

These prefixes are reserved for future use:

| Prefix | Reserved For |
|--------|--------------|
| `IMAGE_` | Image processing errors |
| `PDF_` | PDF generation errors |
| `EMAIL_` | Email sending errors |
| `SOCIAL_` | Social media integration errors |
| `CANVA_` | Canva integration errors |
| `GOOGLE_` | Google integration errors |
| `YOUTUBE_` | YouTube API errors |
| `FONT_` | Font handling errors |
| `COLOR_` | Color processing errors |
| `RENDER_` | Renderer errors |

---

## Error Response Structure

All errors follow this structure:

```javascript
{
  success: false,
  error: {
    code: "ERROR_CODE",           // From this registry
    message: "Human readable",     // Descriptive message
    level: 2,                      // Error level (1-4)
    module: "ModuleName",          // Which module threw it
    timestamp: "ISO8601",          // When it occurred

    // Optional context
    details: { ... },              // Additional error-specific data
    recoverable: true,             // Can caller retry/fix?
    suggestion: "Try X instead"    // Actionable advice
  }
}
```

---

## FailureHandler Integration

FailureHandler uses this registry with `failure.rules.json` to map errors to solutions.

### Solution Strategies

| Strategy | Level | What It Does |
|----------|-------|--------------|
| `retry` | 1 | Wait and retry with backoff |
| `find_alternative` | 2 | Ask Librarian for similar entry |
| `self_heal` | 3 | Run AI chain to create missing entry |
| `human_escalation` | 4 | Create ticket, pause job, wait for human |
| `fail_immediately` | 4 | No recovery possible, fail and log |

### Complete Error → Solution Mapping

| Error Code | Level | Strategy | On Success | On Failure |
|------------|-------|----------|------------|------------|
| **Level 1 - Transient** |
| `API_TIMEOUT` | 1 | retry (3x, exponential) | continue | → Level 2 |
| `API_RATE_LIMIT` | 1 | retry (use retry-after) | continue | → Level 2 |
| `API_5XX` | 1 | retry (3x, exponential) | continue | → Level 2 |
| `LOCK_TIMEOUT` | 1 | retry (5x) → check stale | continue | → Level 4 |
| `MODULE_NOT_READY` | 1 | retry (3x, linear) | continue | → Level 4 |
| **Level 2 - Find Alternative** |
| `ENTRY_NOT_FOUND` | 2 | Librarian.recommend | substitute | → Level 3 |
| `TEMPLATE_NOT_FOUND` | 2 | Librarian.recommend | substitute | → Level 3 |
| `SCHEMA_VALIDATION_FAILED` | 2 | fix data per errors | retry | → Level 4 |
| `FILE_SIZE_EXCEEDED` | 2 | use bypassSizeLimit | retry | → Level 4 |
| `TOKEN_LIMIT_EXCEEDED` | 2 | chunk content | retry | → Level 3 |
| `CACHE_CORRUPTED` | 2 | evict and refetch | continue | — |
| `INDEX_STALE` | 2 | rebuild index | retry | → Level 3 |
| **Level 3 - Self-Heal / Degraded** |
| `INDEX_BUILD_FAILED` | 3 | use stale index | degraded | → Level 4 |
| `CONTENT_QUALITY_LOW` | 3 | regenerate | retry | → Level 4 |
| `CONTENT_OFF_BRAND` | 3 | regenerate with brand | retry | → Level 4 |
| `JOB_DEPENDENCY_FAILED` | 3 | skip or use partial | degraded | → Level 4 |
| `CHAIN_STEP_FAILED` | 3 | retry step | continue | → Level 4 |
| **Level 4 - Human Required** |
| `BRAND_NAME_MISSING` | 4 | human ticket | apply decision | fail job |
| `API_AUTH_FAILED` | 4 | human ticket | apply decision | fail job |
| `API_QUOTA_EXCEEDED` | 4 | human ticket | apply decision | fail job |
| `DISK_FULL` | 4 | human ticket | apply decision | fail job |
| `DISK_SPACE_LOW` | 4 | human + auto-cleanup | apply decision | fail job |
| `PERMISSION_DENIED` | 4 | human ticket | apply decision | fail job |
| `REPO_NOT_FOUND` | 4 | human ticket | fix config | fail job |
| `SCHEMA_NOT_FOUND` | 4 | human ticket | fix config | fail job |
| **No Recovery** |
| `PATH_OUTSIDE_ALLOWED` | — | fail immediately | — | — |
| `PATH_TRAVERSAL_DETECTED` | — | fail immediately | — | — |
| `LOCK_DEADLOCK_DETECTED` | — | fail + investigate | — | — |
| `TEMPLATE_CIRCULAR_INHERIT` | — | fail + fix template | — | — |
| `CHAIN_LOOP_DETECTED` | — | fail + fix chain | — | — |

### failure.rules.json Structure

```json
{
  "errorSolutions": {
    "API_TIMEOUT": {
      "level": 1,
      "strategy": "retry",
      "config": {
        "maxRetries": 3,
        "backoff": "exponential",
        "baseDelay": 1000
      },
      "onExhausted": "escalate",
      "escalateTo": 2
    },

    "ENTRY_NOT_FOUND": {
      "level": 2,
      "strategy": "find_alternative",
      "config": {
        "method": "Librarian.recommend",
        "params": {
          "similar_to": "{{entryId}}",
          "repo": "{{repo}}",
          "exclude": ["{{entryId}}"]
        }
      },
      "onSuccess": "substitute_and_retry",
      "onFailure": "escalate",
      "escalateTo": 3
    },

    "DISK_SPACE_LOW": {
      "level": 4,
      "strategy": "human_escalation",
      "config": {
        "ticketType": "system_alert",
        "priority": "critical",
        "suggestedActions": [
          { "action": "free_disk_space", "label": "Clean up temp files" },
          { "action": "expand_storage", "label": "Add more storage" }
        ],
        "autoActions": [
          { "action": "Writer.initialize", "condition": "always" }
        ]
      }
    },

    "PATH_OUTSIDE_ALLOWED": {
      "level": 4,
      "strategy": "fail_immediately",
      "config": {
        "reason": "security_violation",
        "log": true,
        "alert": true
      },
      "noRecovery": true
    }
  }
}
```

### File Location

```
/repos/config/
  failure.rules.json      ← Error → Solution mapping (runtime)

/docs/
  ERROR-CODES.md          ← This file (human reference)
```

---

## Adding New Error Codes

When adding a new error code:

1. Check if existing code fits (reuse > create)
2. Use appropriate category prefix
3. Assign correct level (1-4)
4. Add recovery guidance
5. List which modules use it
6. Update module spec's error table

**Naming rules:**
- UPPER_SNAKE_CASE
- Category prefix required
- Specific, not generic ("FILE_TOO_LARGE" not "FILE_ERROR")
- Max 3 words after prefix

---

## Module Error Summary

Quick reference: which modules have which errors

| Module | Primary Error Categories |
|--------|-------------------------|
| Writer | PATH_, FILE_, LOCK_, DISK_, PERMISSION_, CROSS_FS_ |
| FileManager | PATH_, FILE_, ENTRY_, REPO_, SCHEMA_, JSON_, MODULE_ |
| Archivist | ENTRY_, SCHEMA_, LOCK_ |
| Librarian | INDEX_, SEARCH_, REPO_ |
| CacheHandler | CACHE_ |
| TemplateEngine | TEMPLATE_, BRAND_, AUDIENCE_, THEME_, OUTLINE_, DATABASE_, AI_, MODULE_ |
| ThemeGenerator | THEME_, BRAND_, AUDIENCE_, DATABASE_, AI_, GOVERNANCE_, OUTCOME_, TRANSFORMATION_, MODULE_ |
| OutlineGenerator | OUTLINE_, THEME_, TEMPLATE_, CONTENT_, DATABASE_, AI_, MODULE_ |
| AICaller | API_, TOKEN_ |
| PromptBuilder | PROMPT_, TEMPLATE_ |
| Orchestrator | JOB_, CHAIN_ |
| TokenManager | TOKEN_ |
| CostBudgetManager | TOKEN_, JOB_ |
| Logger | LOG_, DATABASE_, MODULE_ |
| DatabaseHandler | DATABASE_, MODULE_ |

---

*Created: 2026-01-18*
*Updated: 2026-01-19 - Added GOVERNANCE_ and OUTCOME_ error categories, updated ThemeGenerator errors*
*Updated: 2026-01-19 - v1.5.0: Added OUTCOME_DIVERGENT, THEME_VISUAL_KEYWORDS_INSUFFICIENT, OUTLINE_TRUTH_DEPENDENCY_VIOLATION*
*Updated: 2026-01-19 - v1.6.0: Expanded OUTLINE_ category with TEMPLATE_DRIFT_DETECTED, SECTION_INDEX_OUT_OF_BOUNDS, PRIMARY_TRUTH_UNDERREPRESENTED, COGNITIVE_TYPE_INCOMPATIBLE, SECTION_COUNT_MISMATCH*
*Updated: 2026-01-19 - v1.7.0: Added TRANSFORMATION_ category, OUTCOME_LIMIT_EXCEEDED, theme validation errors (TRUTHS_OUTCOME_MISALIGNMENT, TRUTHS_DONT_FACILITATE_SHIFT, PERSPECTIVE_MISMATCH), GOVERNANCE_ANGLE_CONTRADICTS_VALUES*
*Updated: 2026-06-07 - v1.8.0: Added LOG_ category (LOG_ENTRY_INVALID, LOG_FORBIDDEN_FIELD, LOG_JOB_FINALIZED) for Logger module; added Logger to Module Error Summary*
*Updated: 2026-06-07 - v1.9.0: Added DATABASE_QUERY_INVALID (L2) + DATABASE_MIGRATION_CONFLICT (L4) for DatabaseHandler; broadened DATABASE_ Used-By to DatabaseHandler (D1 target); added DatabaseHandler to Module Error Summary*
*Version: 1.9.0*
