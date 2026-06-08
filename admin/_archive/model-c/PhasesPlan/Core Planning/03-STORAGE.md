# Storage Structure

> Defines where data lives: SQLite for state, JSON for config, TypeScript for logic.

---

## Overview

| Type | Storage | Purpose |
|------|---------|---------|
| **Logic** | TypeScript | What components do |
| **Config** | JSON files | How components behave |
| **State** | SQLite | Runtime data, jobs, logs |

---

## SQLite Database Schema

### Core Tables

#### jobs
Stores all jobs and their status.

```sql
CREATE TABLE jobs (
    id TEXT PRIMARY KEY,
    type TEXT NOT NULL,              -- "brand-build", "colour-palette", etc.
    status TEXT NOT NULL,            -- "pending", "running", "completed", "failed"
    priority INTEGER DEFAULT 0,
    config JSON,                     -- Job-specific parameters
    result JSON,                     -- Output when complete
    error TEXT,                      -- Error message if failed
    progress INTEGER DEFAULT 0,      -- 0-100
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    started_at DATETIME,
    completed_at DATETIME,
    parent_job_id TEXT,              -- For sub-jobs
    FOREIGN KEY (parent_job_id) REFERENCES jobs(id)
);
```

#### tasks
Stores individual tasks within jobs.

```sql
CREATE TABLE tasks (
    id TEXT PRIMARY KEY,
    job_id TEXT NOT NULL,
    system TEXT NOT NULL,            -- "colour", "brand", "ai-provider", etc.
    action TEXT NOT NULL,            -- "generate-palette", "fetch-brand", etc.
    status TEXT NOT NULL,            -- "pending", "running", "completed", "failed"
    sequence INTEGER,                -- Order in job
    depends_on JSON,                 -- Array of task IDs ["task-1", "task-2"]
    input JSON,                      -- Task input data
    output JSON,                     -- Task output data
    error TEXT,
    attempts INTEGER DEFAULT 0,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    started_at DATETIME,
    completed_at DATETIME,
    FOREIGN KEY (job_id) REFERENCES jobs(id)
);
```

#### task_queue
Active queue for task execution.

```sql
CREATE TABLE task_queue (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    task_id TEXT NOT NULL,
    priority INTEGER DEFAULT 0,
    scheduled_for DATETIME,
    locked_by TEXT,                  -- Worker ID if locked
    locked_at DATETIME,
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);
```

#### batches
Groups of tasks processed together by Batch Manager.

```sql
CREATE TABLE batches (
    id TEXT PRIMARY KEY,
    job_id TEXT,
    batch_type TEXT NOT NULL,        -- "prep", "ai_call", "post_process"
    task_ids JSON NOT NULL,          -- Array of task IDs in this batch
    status TEXT NOT NULL,            -- "pending", "processing", "complete", "failed"
    priority INTEGER DEFAULT 0,
    model TEXT,                      -- Locked model for this batch (consistency rule)
    reserved_tokens INTEGER,         -- Tokens reserved for this batch
    reserved_budget_usd REAL,        -- Budget reserved for this batch
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    started_at DATETIME,
    completed_at DATETIME,
    FOREIGN KEY (job_id) REFERENCES jobs(id)
);

CREATE INDEX idx_batches_job ON batches(job_id);
CREATE INDEX idx_batches_status ON batches(status);
```

### Publishing Tables

#### published_content
Tracks all content published to external platforms. **Critical for idempotency - prevents duplicate publishing.**

```sql
CREATE TABLE published_content (
    id TEXT PRIMARY KEY,
    job_id TEXT NOT NULL,
    task_id TEXT,

    -- What was published
    content_type TEXT NOT NULL,         -- "image", "post", "carousel", "story", "reel", "article"
    content_hash TEXT NOT NULL,         -- Hash of content for duplicate detection
    local_file_path TEXT,               -- Path to local file if applicable

    -- Where it was published
    platform TEXT NOT NULL,             -- "instagram", "twitter", "linkedin", "facebook", "tiktok"
    platform_account TEXT,              -- Account/page identifier

    -- External identifiers (from platform)
    external_id TEXT,                   -- Platform's ID for this content
    external_url TEXT,                  -- URL to view on platform

    -- Status tracking
    status TEXT NOT NULL,               -- "pending", "publishing", "published", "failed", "deleted"
    scheduled_for DATETIME,             -- If scheduled for later
    published_at DATETIME,              -- When actually published

    -- Metadata
    caption TEXT,                       -- Post caption/text
    hashtags JSON,                      -- Array of hashtags used
    mentions JSON,                      -- Array of accounts mentioned
    platform_response JSON,             -- Full response from platform API

    -- Error tracking
    error_message TEXT,
    retry_count INTEGER DEFAULT 0,
    last_retry_at DATETIME,

    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (job_id) REFERENCES jobs(id),
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);

CREATE INDEX idx_published_platform ON published_content(platform, status);
CREATE INDEX idx_published_job ON published_content(job_id);
CREATE INDEX idx_published_external ON published_content(platform, external_id);
CREATE INDEX idx_published_hash ON published_content(content_hash);
```

**Idempotency Check:**
Before publishing, query: `SELECT * FROM published_content WHERE content_hash = ? AND platform = ? AND status = 'published'`
If exists → Skip (already published)

---

### Logging Tables

#### logs
System-wide logging.

```sql
CREATE TABLE logs (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    level TEXT NOT NULL,             -- "debug", "info", "warn", "error"
    system TEXT,                     -- Which system logged this
    component TEXT,                  -- Manager, Worker, or Function name
    job_id TEXT,
    task_id TEXT,
    message TEXT NOT NULL,
    data JSON,                       -- Additional structured data
    FOREIGN KEY (job_id) REFERENCES jobs(id),
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);
```

#### error_log
Detailed error tracking.

```sql
CREATE TABLE error_log (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    job_id TEXT,
    task_id TEXT,
    system TEXT NOT NULL,
    component TEXT NOT NULL,
    error_type TEXT NOT NULL,        -- "api_error", "validation", "timeout", etc.
    error_message TEXT NOT NULL,
    stack_trace TEXT,
    context JSON,                    -- Relevant context data
    resolved BOOLEAN DEFAULT FALSE,
    resolved_at DATETIME,
    resolution TEXT,
    FOREIGN KEY (job_id) REFERENCES jobs(id),
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);
```

### Cost Tables

#### cost_records
Tracks all costs incurred (with shadow billing support). **Purpose: Budgeting, value analysis.**

Unlike token_usage (which tracks capacity), this table tracks money for:
- Shadow billing (what free tier usage WOULD cost)
- Budget forecasting and planning
- Cost vs quality analysis
- Value per model comparison

```sql
CREATE TABLE cost_records (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    job_id TEXT,
    task_id TEXT,
    system TEXT NOT NULL,
    provider TEXT,                   -- "openai", "anthropic", "canva", etc.
    model TEXT,                      -- "gpt-4o", "claude-opus", etc.
    operation TEXT,                  -- "completion", "image-gen", "api-call", etc.
    tokens_input INTEGER,
    tokens_output INTEGER,

    -- Shadow billing fields
    would_cost_usd REAL NOT NULL,    -- What this WOULD cost at paid rates
    actual_charged_usd REAL NOT NULL, -- What was actually charged (free tier = 0)
    tier TEXT NOT NULL,              -- "free", "paid", "reserved_credits"

    -- Quality tracking (filled in after review)
    quality_score REAL,              -- Composite quality score 0-10

    metadata JSON,
    FOREIGN KEY (job_id) REFERENCES jobs(id),
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);
```

#### budgets
Budget definitions and tracking.

```sql
CREATE TABLE budgets (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL,              -- "daily", "weekly", "monthly", "total"
    limit_usd REAL NOT NULL,
    spent_usd REAL DEFAULT 0,
    period_start DATETIME,
    period_end DATETIME,
    alert_threshold REAL DEFAULT 0.8, -- Alert at 80% by default
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

#### budget_approvals
Human approval requests for large costs.

```sql
CREATE TABLE budget_approvals (
    id TEXT PRIMARY KEY,
    job_id TEXT NOT NULL,
    task_id TEXT,
    requested_amount_usd REAL NOT NULL,
    reason TEXT NOT NULL,            -- Why approval needed
    alternatives JSON,               -- Cheaper options offered
    status TEXT NOT NULL,            -- "pending", "approved", "rejected", "modified"
    requested_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    decided_at DATETIME,
    decided_by TEXT,                 -- Human who approved/rejected
    approved_amount_usd REAL,        -- May differ from requested
    notes TEXT,
    FOREIGN KEY (job_id) REFERENCES jobs(id),
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);
```

### Job Estimation Tables

#### job_estimates
Pre-flight estimates for jobs before execution.

```sql
CREATE TABLE job_estimates (
    id TEXT PRIMARY KEY,
    job_id TEXT NOT NULL,
    job_type TEXT NOT NULL,
    estimated_tokens INTEGER NOT NULL,
    estimated_cost_usd REAL NOT NULL,
    confidence REAL NOT NULL,        -- 0.0-1.0
    buffer_percent REAL NOT NULL,    -- Buffer added based on confidence
    breakdown JSON NOT NULL,         -- Per-task breakdown
    input_analysis JSON,             -- Dynamic adjustments from input
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (job_id) REFERENCES jobs(id)
);
```

#### estimation_history
Historical actuals vs estimates for learning.

```sql
CREATE TABLE estimation_history (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    job_type TEXT NOT NULL,
    task_type TEXT,                  -- NULL for job-level, set for task-level
    estimated_tokens INTEGER NOT NULL,
    actual_tokens INTEGER NOT NULL,
    estimated_cost_usd REAL NOT NULL,
    actual_cost_usd REAL NOT NULL,
    variance_percent REAL NOT NULL,  -- (actual - estimated) / estimated * 100
    job_id TEXT NOT NULL,
    completed_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (job_id) REFERENCES jobs(id)
);

CREATE INDEX idx_estimation_history_job_type ON estimation_history(job_type);
CREATE INDEX idx_estimation_history_task_type ON estimation_history(task_type);
```

### Model Tables

#### model_ratings
Model quality ratings for equivalency queries.

```sql
CREATE TABLE model_ratings (
    id TEXT PRIMARY KEY,             -- Model ID (e.g., "gpt-5", "claude-opus")
    provider TEXT NOT NULL,
    display_name TEXT NOT NULL,
    quality_rating REAL NOT NULL,    -- 0-10 overall quality
    task_ratings JSON,               -- Per-task ratings {"blog": 9.2, "code": 8.5}
    cost_per_1k_input REAL NOT NULL,
    cost_per_1k_output REAL NOT NULL,
    tokens_per_minute INTEGER,       -- Rate limit
    enabled BOOLEAN DEFAULT TRUE,
    tier TEXT NOT NULL,              -- "free", "paid", "enterprise"
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Validator State Tables

#### reservations
Active resource reservations (backup for memory state).

```sql
CREATE TABLE reservations (
    id TEXT PRIMARY KEY,
    batch_id TEXT NOT NULL,
    resource_type TEXT NOT NULL,     -- "tokens", "budget", "rate"
    provider TEXT,                   -- For provider-specific reservations
    model TEXT,                      -- For model-specific reservations
    amount REAL NOT NULL,
    priority INTEGER DEFAULT 0,      -- Higher = more priority
    status TEXT NOT NULL,            -- "active", "consuming", "released"
    reserved_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    expires_at DATETIME,
    consumed REAL DEFAULT 0,         -- How much of reservation used
    released_at DATETIME
);

CREATE INDEX idx_reservations_batch ON reservations(batch_id);
CREATE INDEX idx_reservations_status ON reservations(status);
```

#### priority_queue
Batches waiting for resource availability.

```sql
CREATE TABLE priority_queue (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    batch_id TEXT NOT NULL,
    resource_type TEXT NOT NULL,     -- "tokens", "budget", "rate"
    provider TEXT,
    model TEXT,
    amount_needed REAL NOT NULL,
    priority INTEGER NOT NULL,
    status TEXT NOT NULL,            -- "waiting", "notified", "claimed"
    queued_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    notify_after DATETIME,           -- When to check for availability
    notified_at DATETIME,
    claimed_at DATETIME
);

CREATE INDEX idx_priority_queue_status ON priority_queue(status);
CREATE INDEX idx_priority_queue_notify ON priority_queue(notify_after);
```

#### validator_snapshots
Periodic snapshots of validator memory state for recovery.

```sql
CREATE TABLE validator_snapshots (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    validator_type TEXT NOT NULL,    -- "api_rate", "token", "budget"
    snapshot_data JSON NOT NULL,     -- Full memory state
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_validator_snapshots_type ON validator_snapshots(validator_type, created_at);
```

#### provider_rate_limits
Persistent state for Provider Rate Limiter (backup for memory).

```sql
CREATE TABLE provider_rate_limits (
    id TEXT PRIMARY KEY,             -- Provider ID: "openai", "anthropic", "google"
    requests_this_minute INTEGER DEFAULT 0,
    tokens_this_minute INTEGER DEFAULT 0,
    requests_today INTEGER DEFAULT 0,
    tokens_today INTEGER DEFAULT 0,
    minute_reset_at DATETIME,        -- When minute counters reset
    day_reset_at DATETIME,           -- When daily counters reset
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Token Tables

#### token_usage
Tracks AI token usage per call. **Purpose: Rate limits, capacity planning.**

Unlike cost_records (which tracks money), this table tracks raw token consumption for:
- Detecting rate limit proximity
- Capacity forecasting
- Identifying high-token tasks

```sql
CREATE TABLE token_usage (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    job_id TEXT,
    task_id TEXT,
    provider TEXT NOT NULL,
    model TEXT NOT NULL,
    tokens_input INTEGER NOT NULL,
    tokens_output INTEGER NOT NULL,
    tokens_total INTEGER NOT NULL,
    estimated_cost_usd REAL,         -- Quick reference, detailed cost in cost_records
    FOREIGN KEY (job_id) REFERENCES jobs(id),
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);

CREATE INDEX idx_token_usage_provider ON token_usage(provider, timestamp);
CREATE INDEX idx_token_usage_model ON token_usage(model, timestamp);
```

### Analytics Tables

#### metrics
System metrics storage.

```sql
CREATE TABLE metrics (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    metric_name TEXT NOT NULL,
    metric_value REAL NOT NULL,
    dimensions JSON,                 -- {"system": "colour", "worker": "palette"}
    period TEXT                      -- "minute", "hour", "day"
);
```

#### events
System events for analytics.

```sql
CREATE TABLE events (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    event_type TEXT NOT NULL,        -- "job_started", "task_completed", etc.
    entity_type TEXT,                -- "job", "task", "system"
    entity_id TEXT,
    data JSON
);
```

### Cache Tables

#### cache
Persistent cache storage.

```sql
CREATE TABLE cache (
    key TEXT PRIMARY KEY,
    value JSON NOT NULL,
    expires_at DATETIME,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    accessed_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    access_count INTEGER DEFAULT 0
);
```

### Archive Tables

#### archived_jobs
Completed jobs moved to archive for long-term storage.

```sql
CREATE TABLE archived_jobs (
    id TEXT PRIMARY KEY,                -- Same ID as original job
    type TEXT NOT NULL,
    status TEXT NOT NULL,               -- "completed", "failed", "cancelled"
    priority INTEGER,
    config JSON,
    result JSON,
    error TEXT,
    progress INTEGER,

    -- Timing
    created_at DATETIME,
    started_at DATETIME,
    completed_at DATETIME,
    archived_at DATETIME DEFAULT CURRENT_TIMESTAMP,

    -- Summary data (denormalized for quick access)
    total_tasks INTEGER,
    completed_tasks INTEGER,
    failed_tasks INTEGER,
    total_cost_usd REAL,               -- Actual charged
    total_would_cost_usd REAL,         -- Shadow billing total
    total_tokens INTEGER,

    -- Full task data preserved
    tasks_snapshot JSON,               -- All tasks with inputs/outputs
    cost_records_snapshot JSON,        -- All cost records for this job

    parent_job_id TEXT
);

CREATE INDEX idx_archived_type ON archived_jobs(type);
CREATE INDEX idx_archived_date ON archived_jobs(completed_at);
```

#### archive_metadata
Tracks archiving operations and retention.

```sql
CREATE TABLE archive_metadata (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    archived_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    jobs_archived INTEGER NOT NULL,
    tasks_archived INTEGER NOT NULL,
    logs_purged INTEGER NOT NULL,
    cost_records_archived INTEGER NOT NULL,
    oldest_job_date DATETIME,          -- Oldest job in this archive batch
    newest_job_date DATETIME,          -- Newest job in this archive batch
    retention_policy TEXT,             -- Policy used: "30_days", "90_days", etc.
    archive_size_bytes INTEGER
);
```

---

### Schedule Tables

#### scheduled_jobs
Recurring and future jobs.

```sql
CREATE TABLE scheduled_jobs (
    id TEXT PRIMARY KEY,
    job_type TEXT NOT NULL,
    job_config JSON NOT NULL,
    schedule_type TEXT NOT NULL,     -- "once", "cron", "interval"
    schedule_value TEXT NOT NULL,    -- Cron expression or interval
    timezone TEXT DEFAULT 'UTC',
    next_run DATETIME,
    last_run DATETIME,
    enabled BOOLEAN DEFAULT TRUE,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## JSON Config Structure

### System Configs

Each system has a JSON config file in `/config/systems/`:

```
/config/systems/
├── orchestration.json
├── ai-provider.json
├── prompt.json
├── brand.json
├── colour.json
├── typography.json
├── theme.json
├── image-creation.json
├── image-storage.json
├── text-generation.json
├── language-check.json
├── document.json
├── output.json
├── email.json
├── calendar.json
├── publishing.json
├── metrics.json
├── research.json
├── database.json
├── file.json
├── cache.json
├── logger.json
├── api.json
├── auth.json
├── cost.json
├── policy.json
└── archiver.json
```

### Example Config: ai-provider.json

```json
{
  "defaultProvider": "openai",
  "fallbackOrder": ["openai", "anthropic", "gemini", "groq"],

  "providers": {
    "openai": {
      "enabled": true,
      "baseUrl": "https://api.openai.com/v1",
      "models": {
        "default": "gpt-4o",
        "fast": "gpt-4o-mini",
        "vision": "gpt-4o"
      },
      "timeout": 60000,
      "maxRetries": 3,
      "rateLimit": {
        "requestsPerMinute": 60,
        "tokensPerMinute": 90000
      }
    },
    "anthropic": {
      "enabled": true,
      "baseUrl": "https://api.anthropic.com/v1",
      "models": {
        "default": "claude-sonnet-4-20250514",
        "fast": "claude-3-5-haiku-20241022",
        "advanced": "claude-opus-4-20250514"
      },
      "timeout": 60000,
      "maxRetries": 3
    }
  },

  "taskModelMapping": {
    "simple-text": "fast",
    "complex-reasoning": "advanced",
    "image-analysis": "vision"
  }
}
```

### Example Config: colour.json

```json
{
  "defaults": {
    "paletteSize": 5,
    "accessibilityLevel": "WCAG-AA",
    "includeNeutrals": true
  },

  "workers": {
    "palette": {
      "aiProvider": "openai",
      "model": "gpt-4o",
      "timeout": 30000
    },
    "token": {
      "outputFormats": ["css", "scss", "json", "tailwind"],
      "includeVariations": true
    }
  },

  "moodMappings": {
    "professional": ["navy", "grey", "white"],
    "playful": ["coral", "teal", "yellow"],
    "luxury": ["gold", "black", "cream"],
    "natural": ["green", "brown", "beige"]
  },

  "accessibility": {
    "WCAG-AA": {
      "normalText": 4.5,
      "largeText": 3.0
    },
    "WCAG-AAA": {
      "normalText": 7.0,
      "largeText": 4.5
    }
  }
}
```

### Example Config: publishing.json

```json
{
  "platforms": {
    "instagram": {
      "enabled": true,
      "postTypes": ["image", "carousel", "reel", "story"],
      "characterLimit": 2200,
      "hashtagLimit": 30,
      "imageSpecs": {
        "square": { "width": 1080, "height": 1080 },
        "portrait": { "width": 1080, "height": 1350 },
        "landscape": { "width": 1080, "height": 566 }
      }
    },
    "twitter": {
      "enabled": true,
      "characterLimit": 280,
      "imageLimit": 4
    },
    "linkedin": {
      "enabled": true,
      "characterLimit": 3000
    }
  },

  "scheduling": {
    "minIntervalMinutes": 60,
    "maxQueueSize": 100
  },

  "retries": {
    "maxAttempts": 3,
    "backoffMultiplier": 2
  }
}
```

---

## Job Estimation Configs

### Location
```
/config/jobs/
├── job-profiles.json           # Job type token/cost profiles
└── token-estimates.json        # Task-level token estimates
```

### Example Config: token-estimates.json

```json
{
  "tasks": {
    "generate-tagline": {
      "estTokensIn": 500,
      "estTokensOut": 100,
      "model": "any",
      "confidence": 0.9
    },
    "generate-blog-post": {
      "estTokensIn": 1500,
      "estTokensOut": 3000,
      "model": "high-quality",
      "confidence": 0.8
    },
    "analyse-brand-values": {
      "estTokensIn": 2000,
      "estTokensOut": 1500,
      "model": "reasoning",
      "confidence": 0.85
    },
    "generate-colour-palette": {
      "estTokensIn": 800,
      "estTokensOut": 500,
      "model": "any",
      "confidence": 0.9
    },
    "check-grammar": {
      "estTokensIn": 1000,
      "estTokensOut": 500,
      "model": "fast",
      "confidence": 0.95
    }
  }
}
```

### Example Config: job-profiles.json

```json
{
  "brand-build": {
    "description": "Complete brand identity build",
    "tasks": [
      { "type": "analyse-brand-values", "count": 3 },
      { "type": "generate-tagline", "count": 5 },
      { "type": "generate-colour-palette", "count": 2 },
      { "type": "generate-typography", "count": 2 },
      { "type": "generate-content", "count": 8 }
    ],
    "estimatedTotalTokens": 32000,
    "estimatedCost": 1.20,
    "confidence": 0.8,
    "historicalAvg": 28500,
    "historicalStdDev": 4200,
    "maxBudget": 10.00
  },
  "blog-post-batch": {
    "description": "Batch blog post generation",
    "tasks": [
      { "type": "generate-blog-post", "countPer": 1 }
    ],
    "tokensPerItem": 4500,
    "costPerItem": 0.15,
    "confidence": 0.85,
    "maxBudget": 50.00
  },
  "social-campaign": {
    "description": "Multi-platform social media campaign",
    "tasks": [
      { "type": "generate-social-post", "count": 12 },
      { "type": "generate-hashtags", "count": 12 },
      { "type": "generate-image-prompt", "count": 6 }
    ],
    "estimatedTotalTokens": 18000,
    "estimatedCost": 0.65,
    "confidence": 0.82,
    "maxBudget": 5.00
  }
}
```

---

## Task Chain Configs

Task chains define the sequence of tasks for job types.

### Location
```
/config/tasks/
├── brand-build.json
├── colour-palette.json
├── social-campaign.json
└── ...
```

### Example: colour-palette.json

```json
{
  "jobType": "colour-palette",
  "description": "Generate a brand colour palette",

  "tasks": [
    {
      "id": "get-brand",
      "system": "brand",
      "action": "fetch-values",
      "required": true
    },
    {
      "id": "generate-palette",
      "system": "colour",
      "action": "generate-palette",
      "dependsOn": ["get-brand"],
      "required": true
    },
    {
      "id": "check-accessibility",
      "system": "colour",
      "action": "check-accessibility",
      "dependsOn": ["generate-palette"],
      "required": true
    },
    {
      "id": "generate-tokens",
      "system": "colour",
      "action": "generate-tokens",
      "dependsOn": ["check-accessibility"],
      "required": false
    },
    {
      "id": "save-palette",
      "system": "database",
      "action": "save",
      "dependsOn": ["generate-palette", "check-accessibility"],
      "required": true
    }
  ],

  "onFailure": {
    "strategy": "retry-then-notify",
    "maxRetries": 2,
    "notifyAfter": 2
  }
}
```

---

## Summary

| Data Type | Storage | Location |
|-----------|---------|----------|
| Runtime state | SQLite | `/database/system.db` |
| System config | JSON | `/config/systems/*.json` |
| Task chains | JSON | `/config/tasks/*.json` |
| Job profiles | JSON | `/config/jobs/job-profiles.json` |
| Token estimates | JSON | `/config/jobs/token-estimates.json` |
| Secrets | Environment | `.env` (never committed) |
| Logic | TypeScript | `/src/systems/**/*.ts` |

### SQLite Tables Summary

| Category | Tables |
|----------|--------|
| **Core** | jobs, tasks, task_queue, batches |
| **Publishing** | published_content |
| **Logging** | logs, error_log |
| **Cost** | cost_records (budgeting, value analysis), budgets, budget_approvals |
| **Tokens** | token_usage (rate limits, capacity) |
| **Estimation** | job_estimates, estimation_history |
| **Models** | model_ratings |
| **Validators** | reservations, priority_queue, validator_snapshots, provider_rate_limits |
| **Analytics** | metrics, events |
| **Cache** | cache |
| **Archive** | archived_jobs, archive_metadata |
| **Schedule** | scheduled_jobs |

### Key Table Distinctions

| Table | Purpose | Used For |
|-------|---------|----------|
| **token_usage** | Raw token consumption | Rate limits, capacity planning |
| **cost_records** | Money (real + shadow) | Budgeting, value analysis |

---

## Next Documents

- [01-ARCHITECTURE.md](01-ARCHITECTURE.md) - Core architecture
- [02-SYSTEMS.md](02-SYSTEMS.md) - Complete list of all systems
- [04-FILE-STRUCTURE.md](04-FILE-STRUCTURE.md) - Project folder layout
- [05-API.md](05-API.md) - Interface layer (CLI, API, Svelte webapp)
- [06-BUILD-PLAN.md](06-MIGRATION-PLAN.md) - What to build, existing projects as reference
