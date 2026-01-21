# File Structure

> Project folder layout for the system.

---

## Root Structure

```
/project-root
│
├── .env                       # Environment variables (NEVER committed)
├── .env.example               # Template for env vars (committed)
│
├── /config                    # JSON configuration files
│   ├── /systems               # System configs
│   ├── /jobs                  # Job estimation profiles
│   └── /tasks                 # Task chain definitions
│
├── /database                  # SQLite database
│   ├── system.db
│   └── /migrations            # SQL migration files
│       ├── 001-initial.sql
│       ├── 002-add-batches.sql
│       └── ...
│
├── /src                       # TypeScript source code
│   ├── /core                  # Core orchestration
│   ├── /validators            # Cross-cutting validators
│   ├── /cross-cutting         # Cross-cutting service managers
│   ├── /systems               # All 26 systems
│   └── /shared                # Shared utilities
│
├── /templates                 # Template files
│   ├── /prompts               # AI prompt templates
│   ├── /email                 # Email templates
│   └── /documents             # Document templates
│
├── /storage                   # File storage
│   ├── /images                # Image files
│   ├── /exports               # Generated exports
│   └── /temp                  # Temporary files
│       └── /{job-id}
│           ├── /raw           # Raw AI output before parsing
│           └── /clean         # Parsed/cleaned output
│
├── /logs                      # Log files (if file-based logging)
│
└── /tests                     # Test files
    ├── /unit                  # Unit tests per system
    ├── /integration           # Integration tests
    ├── /e2e                   # End-to-end tests
    └── /fixtures              # Test data
```

---

## Config Directory

```
/config
│
├── /systems                   # One JSON per system
│   ├── orchestration.json
│   ├── ai-provider.json
│   ├── prompt.json
│   ├── brand.json
│   ├── colour.json
│   ├── typography.json
│   ├── theme.json
│   ├── image-creation.json
│   ├── image-storage.json
│   ├── text-generation.json
│   ├── language-check.json
│   ├── document.json
│   ├── output.json
│   ├── email.json
│   ├── calendar.json
│   ├── publishing.json
│   ├── metrics.json
│   ├── research.json
│   ├── database.json
│   ├── file.json
│   ├── cache.json
│   ├── logger.json
│   ├── api.json
│   ├── auth.json
│   ├── cost.json
│   ├── policy.json
│   └── archiver.json          # Archiver config (retention, triggers)
│
├── /jobs                      # Job estimation configs
│   ├── job-profiles.json      # Job type profiles with token/cost estimates
│   └── token-estimates.json   # Task-level token estimates
│
└── /tasks                     # Task chain definitions
    ├── brand-build.json
    ├── colour-palette.json
    ├── logo-creation.json
    ├── social-campaign.json
    ├── email-campaign.json
    └── ...
```

---

## Source Directory

```
/src
│
├── /core                      # Core orchestration layer
│   ├── job-manager.ts         # Creates and tracks jobs
│   ├── task-manager.ts        # Breaks jobs into tasks, manages order
│   ├── batch-manager.ts       # Groups tasks by type for efficient processing
│   ├── worker-manager.ts      # Assigns batches to workers
│   ├── queue-manager.ts       # Task queue management
│   ├── job-estimator.ts       # Pre-flight token/cost estimation
│   ├── recovery-manager.ts    # Handles system restart, resumes work
│   ├── archiver.ts            # Archives completed jobs, manages retention
│   └── types.ts               # Core type definitions
│
├── /validators                # Cross-cutting validators
│   ├── api-rate-validator.ts  # Tracks API requests per system
│   ├── token-validator.ts     # Tracks token usage per model
│   ├── budget-validator.ts    # Tracks budget and authorization
│   ├── provider-rate-limiter.ts # Tracks rate limits per provider across ALL systems
│   └── types.ts               # Validator type definitions
│
├── /systems                   # All 26 systems
│   │
│   ├── /orchestration
│   │   ├── index.ts           # System entry point
│   │   ├── manager.ts         # OrchestrationManager
│   │   ├── /workers
│   │   │   ├── job-worker.ts
│   │   │   ├── queue-worker.ts
│   │   │   ├── schedule-worker.ts
│   │   │   └── status-worker.ts
│   │   └── /functions
│   │       └── ...
│   │
│   ├── /ai-provider
│   │   ├── index.ts
│   │   ├── manager.ts         # AIManager
│   │   ├── /workers
│   │   │   ├── openai-worker.ts
│   │   │   ├── claude-worker.ts
│   │   │   ├── gemini-worker.ts
│   │   │   ├── groq-worker.ts
│   │   │   └── openrouter-worker.ts
│   │   └── /functions
│   │       ├── response-parser.ts
│   │       └── token-counter.ts
│   │
│   ├── /prompt
│   │   ├── index.ts
│   │   ├── manager.ts         # PromptManager
│   │   ├── /workers
│   │   │   ├── template-worker.ts
│   │   │   └── builder-worker.ts
│   │   └── /functions
│   │       ├── variable-resolver.ts
│   │       └── constraint-applier.ts
│   │
│   ├── /brand
│   │   ├── index.ts
│   │   ├── manager.ts         # BrandManager
│   │   ├── /workers
│   │   │   ├── brand-worker.ts
│   │   │   ├── audience-worker.ts
│   │   │   └── tracker-worker.ts
│   │   └── /functions
│   │       └── brand-validator.ts
│   │
│   ├── /colour
│   │   ├── index.ts
│   │   ├── manager.ts         # ColourManager
│   │   ├── /workers
│   │   │   ├── palette-worker.ts
│   │   │   └── token-worker.ts
│   │   └── /functions
│   │       ├── colour-mixer.ts
│   │       ├── harmony-generator.ts
│   │       └── accessibility-checker.ts
│   │
│   ├── /typography
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── font-worker.ts
│   │   │   └── pairing-worker.ts
│   │   └── /functions
│   │       ├── scale-generator.ts
│   │       └── font-matcher.ts
│   │
│   ├── /theme
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── token-worker.ts
│   │   │   └── theme-worker.ts
│   │   └── /functions
│   │       ├── token-formatter.ts
│   │       └── value-aligner.ts
│   │
│   ├── /image-creation
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── ai-image-worker.ts
│   │   │   ├── logo-worker.ts
│   │   │   ├── svg-worker.ts
│   │   │   └── canva-worker.ts
│   │   └── /functions
│   │       ├── image-optimizer.ts
│   │       └── style-applier.ts
│   │
│   ├── /image-storage
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── unsplash-worker.ts
│   │   │   ├── pexels-worker.ts
│   │   │   ├── local-worker.ts
│   │   │   └── collection-worker.ts
│   │   └── /functions
│   │       └── image-tagger.ts
│   │
│   ├── /text-generation
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── content-worker.ts
│   │   │   ├── website-worker.ts
│   │   │   └── tagline-worker.ts
│   │   └── /functions
│   │       ├── text-formatter.ts
│   │       └── tone-adjuster.ts
│   │
│   ├── /language-check
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── grammar-worker.ts
│   │   │   ├── spell-worker.ts
│   │   │   └── quality-worker.ts
│   │   └── /functions
│   │       └── suggestion-formatter.ts
│   │
│   ├── /document
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── pdf-worker.ts
│   │   │   ├── legal-worker.ts
│   │   │   └── google-docs-worker.ts
│   │   └── /functions
│   │       ├── template-loader.ts
│   │       └── data-merger.ts
│   │
│   ├── /output
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── export-worker.ts
│   │   │   ├── format-worker.ts
│   │   │   ├── delivery-worker.ts
│   │   │   └── canva-export-worker.ts
│   │   └── /functions
│   │       └── packager.ts
│   │
│   ├── /email
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── compose-worker.ts
│   │   │   ├── sendgrid-worker.ts
│   │   │   └── smtp-worker.ts
│   │   └── /functions
│   │       ├── template-renderer.ts
│   │       └── personalization-resolver.ts
│   │
│   ├── /calendar
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── planner-worker.ts
│   │   │   ├── google-calendar-worker.ts
│   │   │   └── schedule-worker.ts
│   │   └── /functions
│   │       ├── date-calculator.ts
│   │       └── conflict-checker.ts
│   │
│   ├── /publishing
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── instagram-worker.ts
│   │   │   ├── facebook-worker.ts
│   │   │   ├── linkedin-worker.ts
│   │   │   ├── twitter-worker.ts
│   │   │   ├── tiktok-worker.ts
│   │   │   ├── pinterest-worker.ts
│   │   │   └── youtube-worker.ts
│   │   └── /functions
│   │       ├── post-formatter.ts
│   │       └── hashtag-generator.ts
│   │
│   ├── /metrics
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── social-metrics-worker.ts
│   │   │   ├── system-metrics-worker.ts
│   │   │   ├── youtube-metrics-worker.ts
│   │   │   └── report-worker.ts
│   │   └── /functions
│   │       ├── aggregator.ts
│   │       └── trend-analyzer.ts
│   │
│   ├── /research
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── web-search-worker.ts
│   │   │   ├── scraper-worker.ts
│   │   │   └── synthesis-worker.ts
│   │   └── /functions
│   │       ├── content-extractor.ts
│   │       └── source-validator.ts
│   │
│   ├── /database
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── query-worker.ts
│   │   │   ├── migration-worker.ts
│   │   │   └── connection-worker.ts
│   │   └── /functions
│   │       ├── query-builder.ts
│   │       └── result-mapper.ts
│   │
│   ├── /file
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── read-worker.ts
│   │   │   ├── write-worker.ts
│   │   │   ├── path-worker.ts
│   │   │   └── google-drive-worker.ts
│   │   └── /functions
│   │       ├── path-builder.ts
│   │       └── validator.ts
│   │
│   ├── /cache
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── memory-worker.ts
│   │   │   └── disk-worker.ts
│   │   └── /functions
│   │       ├── ttl-manager.ts
│   │       └── key-generator.ts
│   │
│   ├── /logger
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── write-worker.ts
│   │   │   └── query-worker.ts
│   │   └── /functions
│   │       ├── aggregator.ts
│   │       └── formatter.ts
│   │
│   ├── /api
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── key-worker.ts
│   │   │   ├── request-worker.ts
│   │   │   └── rate-limit-worker.ts
│   │   └── /functions
│   │       ├── retry-handler.ts
│   │       └── response-normalizer.ts
│   │
│   ├── /auth
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── token-worker.ts
│   │   │   └── oauth-worker.ts
│   │   └── /functions
│   │       ├── permission-checker.ts
│   │       └── token-validator.ts
│   │
│   ├── /cost
│   │   ├── index.ts
│   │   ├── manager.ts
│   │   ├── /workers
│   │   │   ├── tracker-worker.ts
│   │   │   ├── budget-worker.ts
│   │   │   └── alert-worker.ts
│   │   └── /functions
│   │       ├── calculator.ts
│   │       └── burn-rate-analyzer.ts
│   │
│   └── /policy
│       ├── index.ts
│       ├── manager.ts
│       ├── /workers
│       │   ├── gate-worker.ts
│       │   ├── validation-worker.ts
│       │   └── error-worker.ts
│       └── /functions
│           ├── rule-evaluator.ts
│           └── escalation-router.ts
│
├── /cross-cutting             # Cross-cutting service managers (interfaces)
│   ├── api-manager.ts         # API config (keys, endpoints) - uses /systems/api workers
│   ├── database-manager.ts    # SQLite operations - uses /systems/database workers
│   ├── cache-manager.ts       # Memory/disk caching - uses /systems/cache workers
│   ├── log-manager.ts         # Logging operations - uses /systems/logger workers
│   ├── file-manager.ts        # Filesystem operations - uses /systems/file workers
│   ├── config-manager.ts      # Config/secrets access
│   ├── event-manager.ts       # Pub/sub events
│   └── types.ts               # Cross-cutting type definitions
│
│   # NOTE: Cross-cutting managers are the PUBLIC INTERFACE.
│   # They delegate to workers in /systems/database, /systems/cache, etc.
│   # Systems provide the workers, cross-cutting provides the API.
│
├── /shared                    # Shared across systems
│   ├── types.ts               # Global type definitions
│   ├── constants.ts           # Global constants
│   ├── errors.ts              # Error classes
│   └── utils.ts               # General utilities
│
├── /api                       # REST API layer
│   ├── routes/
│   │   ├── jobs.ts            # /api/jobs endpoints
│   │   ├── tasks.ts           # /api/tasks endpoints
│   │   ├── brands.ts          # /api/brands endpoints
│   │   ├── budget.ts          # /api/budget endpoints
│   │   ├── settings.ts        # /api/settings endpoints
│   │   ├── archive.ts         # /api/archive endpoints
│   │   └── health.ts          # /api/health endpoint
│   ├── middleware/
│   │   ├── auth.ts            # Google OAuth verification
│   │   ├── validation.ts      # Request validation
│   │   └── error-handler.ts   # Error response formatting
│   ├── websocket/
│   │   └── handler.ts         # WebSocket event handling
│   └── server.ts              # Express/Fastify server setup
│
└── /cli                       # CLI commands
    ├── commands/
    │   ├── job.ts             # job:create, job:status, etc.
    │   ├── task.ts            # task:list, task:status
    │   ├── brand.ts           # brand:list, brand:create
    │   ├── budget.ts          # budget:status, budget:set
    │   ├── system.ts          # system:status, system:validators
    │   └── archive.ts         # archive:list, archive:view
    └── index.ts               # CLI entry point
```

---

## Templates Directory

```
/templates
│
├── /prompts                   # AI prompt templates
│   ├── colour-palette.md
│   ├── brand-values.md
│   ├── content-generation.md
│   ├── tagline-creation.md
│   └── ...
│
├── /email                     # Email templates
│   ├── welcome.html
│   ├── notification.html
│   └── ...
│
└── /documents                 # Document templates
    ├── brand-guidelines.html
    ├── contract.html
    ├── nda.html
    └── ...
```

---

## Storage Directory

```
/storage
│
├── /images                    # Image storage
│   ├── /brands                # Brand-specific images
│   │   └── /{brand-id}
│   ├── /collections           # Curated collections
│   └── /generated             # AI-generated images
│
├── /exports                   # Generated exports
│   └── /{job-id}
│
└── /temp                      # Temporary files
    └── /{session-id}
```

---

## System Pattern

Every system follows the same structure:

```
/systems/{system-name}
│
├── index.ts                   # System entry point, exports API
├── manager.ts                 # System manager
├── types.ts                   # System-specific types (optional)
│
├── /workers                   # Worker implementations
│   ├── {name}-worker.ts
│   └── ...
│
└── /functions                 # Pure functions
    ├── {name}.ts
    └── ...
```

### System Entry Point (index.ts)

```typescript
// /src/systems/colour/index.ts
import { ColourManager } from './manager';
import { ColourSystemConfig } from './types';

export class ColourSystem {
  private manager: ColourManager;

  constructor(config: ColourSystemConfig) {
    this.manager = new ColourManager(config);
  }

  // Public API for Task Manager
  async generatePalette(input: PaletteInput): Promise<PaletteOutput> {
    return this.manager.handle('generate-palette', input);
  }

  async checkAccessibility(input: AccessibilityInput): Promise<AccessibilityOutput> {
    return this.manager.handle('check-accessibility', input);
  }

  async generateTokens(input: TokenInput): Promise<TokenOutput> {
    return this.manager.handle('generate-tokens', input);
  }
}
```

---

## Summary

| Directory | Purpose |
|-----------|---------|
| `/config` | JSON configuration (how) |
| `/config/systems` | System behavior configs |
| `/config/jobs` | Job estimation profiles |
| `/config/tasks` | Task chain definitions |
| `/database` | SQLite database (state) |
| `/database/migrations` | SQL migration files |
| `/src/core` | Core orchestration logic (job, task, batch, worker managers) |
| `/src/validators` | Cross-cutting validators (4 total) |
| `/src/cross-cutting` | Cross-cutting service managers (7 total) |
| `/src/systems` | All 26 system implementations |
| `/src/shared` | Shared utilities and types |
| `/src/api` | REST API routes and middleware |
| `/src/cli` | CLI commands |
| `/templates` | Reusable templates |
| `/storage` | File storage |
| `/storage/temp/{job-id}/raw` | Raw AI output before parsing |
| `/storage/temp/{job-id}/clean` | Parsed/cleaned output |
| `/logs` | Log files |
| `/tests` | Test files (unit, integration, e2e, fixtures) |

### Files at Root

| File | Purpose | Committed |
|------|---------|-----------|
| `.env` | Environment variables, secrets | NO |
| `.env.example` | Template for env vars | YES |

---

## Next Documents

- [01-ARCHITECTURE.md](01-ARCHITECTURE.md) - Core architecture
- [02-SYSTEMS.md](02-SYSTEMS.md) - Complete list of all systems
- [03-STORAGE.md](03-STORAGE.md) - Database schema and config structure
- [05-API.md](05-API.md) - Interface layer (CLI, API, Svelte webapp)
- [06-BUILD-PLAN.md](06-MIGRATION-PLAN.md) - What to build, existing projects as reference
