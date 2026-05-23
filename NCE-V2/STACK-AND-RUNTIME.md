# STACK-AND-RUNTIME.md

> **Updates 2026-05-23 (Stage 1 frame-lock alignment):**
> - System count corrected from "31 systems" to **27 systems** (reflects FileTree-v2.md collapses: colours/typography/visual absorbed into brand/; image/ai-image absorbed into assets/; svg/ moved to lib/svg/ utilities; library/ added as new system).
> - LOC band updated from 1500 to **2000** per runtime file (with TS verbosity multiplier 1.3–1.5× over Python; excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments).
> - Methodology reference: this document is the operational reference for the 5-stage reduced methodology (see `NCE-V2/docs/REDUCED-METHODOLOGY.md`), which superseded the 68-phase methodology now archived at `NCE-V2/archive/68-phase-methodology/`.
> - Library access pattern: all library reads via `library/Librarian` (D1 access mediated through the library system; no direct D1 binding from outside library/).
> - Output-boundary rule: NCE-V2 emits JSON for web (rendered by Astro consumer); NCE-V2 emits final rendered artefacts for PDF/DOCX/email/embedded marks.

**Status:** Approved (2026-05-23, post-Stage 1 alignment)
**Type:** Operational reference (not a Pass; runtime/infrastructure scaffolding doc)
**Generated:** 2026-05-18
**Last updated:** 2026-05-23
**Scope:** The concrete stack, files, and Cloudflare setup needed to run NCE-V2 (formerly NCEMPIRE)

---

## Purpose of this Document

This document describes **the concrete runtime, files, configuration, and Cloudflare setup** needed to deploy NCEMPIRE. It is the operational reference a developer (or you, or Claude Code) opens when they need to know: *what files exist, what services are enabled, where things go, and what runs where.*

It is **not**:

- A Pass document (no pass methodology applied — this is infrastructure reference)
- An architectural decision about subsystems (those are locked in existing system specs)
- A revision of STACK-PROPOSAL (which remains in its own review cycle)
- A per-subsystem implementation spec (that lives elsewhere)

It **is**:

- The shape of the project on disk
- The shape of the project on Cloudflare
- The minimum viable scaffold (day-one starting state)
- The recipe for adding a system or Worker to the project
- The reference for what each config file does and what's required in it

Where it touches Pass-deferred questions (Worker grouping, queue topology, storage details), it presents the documented current direction without overriding Pass 0 conclusions. Anything that would otherwise pre-decide architecture is flagged.

---

## Confirmed Choices

These are inputs to this document, not decisions it makes:

| Choice | Value | Confirmed |
|---|---|---|
| Language | TypeScript on Cloudflare Workers | Yes |
| Heavy-compute approach | Workers as much as possible; external only as last resort | Yes |
| NCEMPIRE boundary | Ends at validated content in storage; Astro consumes separately | Yes |
| Cloudflare account structure | Separate account per system (NCEMPIRE has its own; brand websites separate) | Yes |
| Repo structure | Single npm package, multiple Workers, Service Bindings between them | Yes |
| Worker granularity | Grouped foundational Workers + per-system Workers for the rest | Yes |
| Wrangler config format | `wrangler.jsonc` | Yes |
| Methodology rigour | Architecture-before-implementation, pass-based, file-size as design signal | Yes |

---

## 1. Stack Summary

```
┌────────────────────────────────────────────────────────────────────────┐
│  Cloudflare Account: NCEMPIRE                                          │
│                                                                        │
│  ┌──────────────────────┐  ┌──────────────────────┐                   │
│  │  Workers (compute)   │  │  Durable Objects     │                   │
│  │  - platform-worker   │  │  - stateful coord.   │                   │
│  │  - ai-worker         │  │  - rate limiting     │                   │
│  │  - brand-worker      │  │  - per-tenant state  │                   │
│  │  - content-worker    │  │                      │                   │
│  │  - …per-system…      │  │                      │                   │
│  └──────────────────────┘  └──────────────────────┘                   │
│                                                                        │
│  ┌──────────────────────┐  ┌──────────────────────┐                   │
│  │  Queues (async)      │  │  Cron Triggers       │                   │
│  │  - generation stages │  │  - scheduled tasks   │                   │
│  │  - audit events      │  │  - cleanup, drift    │                   │
│  │  - human handoff     │  │                      │                   │
│  └──────────────────────┘  └──────────────────────┘                   │
│                                                                        │
│  ┌──────────────────────┐  ┌──────────────────────┐                   │
│  │  D1 (SQLite)         │  │  R2 (object store)   │                   │
│  │  - library DBs       │  │  - draft staging     │                   │
│  │  - audit, registry   │  │  - content artefacts │                   │
│  └──────────────────────┘  └──────────────────────┘                   │
│                                                                        │
│  ┌──────────────────────┐  ┌──────────────────────┐                   │
│  │  KV (cache/config)   │  │  Vectorize           │                   │
│  │  - feature flags     │  │  - embeddings        │                   │
│  │  - rate limit cnt.   │  │                      │                   │
│  └──────────────────────┘  └──────────────────────┘                   │
│                                                                        │
│  ┌──────────────────────┐  ┌──────────────────────┐                   │
│  │  AI Gateway          │  │  Browser Rendering   │                   │
│  │  - LLM proxy/logs    │  │  - PDF via headless  │                   │
│  └──────────────────────┘  └──────────────────────┘                   │
│                                                                        │
│  Workers Secrets · Service Bindings · Logpush                          │
└────────────────────────────────────────────────────────────────────────┘
                                  │
                                  │ writes validated content
                                  ▼
                          R2 (consumed by Astro)
                                  │
                                  ▼
┌────────────────────────────────────────────────────────────────────────┐
│  Cloudflare Account: Brand Websites (SEPARATE, OUT OF NCEMPIRE SCOPE) │
└────────────────────────────────────────────────────────────────────────┘
```

Everything inside the NCEMPIRE account is what this document covers. The brand websites account is out of scope; it consumes from R2 and is operated independently.

---

## 2. Cloudflare Account and Environment Setup

### 2.1 — Accounts

**One Cloudflare account per system.** NCEMPIRE has its own account. Brand websites are in their own account(s). This document covers only the NCEMPIRE account.

For NCEMPIRE itself, environments are managed via Wrangler's `--env` flag (Cloudflare's recommended pattern), not via separate accounts:

| Environment | Purpose | Wrangler invocation |
|---|---|---|
| `dev` | Local development; pushes are manual and rare | `wrangler dev` (local) or `wrangler deploy --env dev` |
| `staging` | Integration testing; receives main-branch pushes | `wrangler deploy --env staging` |
| `prod` | Production | `wrangler deploy --env prod` |

All three environments live in the same NCEMPIRE Cloudflare account. Each environment has its own:

- Worker names (auto-suffixed: `platform-worker-dev`, `platform-worker-staging`, `platform-worker-prod`)
- D1 database bindings (pointing at separate D1 databases per environment)
- R2 bucket bindings (separate buckets per environment)
- KV namespace bindings (separate namespaces per environment)
- Queue bindings (separate queues per environment)
- Secrets (set per environment via `wrangler secret put --env <env>`)

### 2.2 — Account-level setup checklist (one-time)

| Step | Action |
|---|---|
| 1 | Create Cloudflare account for NCEMPIRE (free tier OK initially) |
| 2 | Upgrade to Workers Paid plan ($5/month minimum) — required for Queues, longer CPU times, more requests |
| 3 | Enable Workers, D1, R2, KV, Queues, Durable Objects, Vectorize, AI Gateway, Browser Rendering as needed |
| 4 | Set spend cap on the account to prevent surprise overages |
| 5 | Create API token for CI/CD with scoped permissions (Workers: Edit; D1: Edit; R2: Edit; etc.) |
| 6 | Configure Cloudflare Tunnel if local LLM routing is required (deferred per OQ-AI-2) |
| 7 | Set up Logpush to R2 if long-term log retention is needed (optional) |

### 2.3 — Cost overview

| Service | Free tier | Paid (NCEMPIRE plan) | NCEMPIRE expected use |
|---|---|---|---|
| Workers | 100k requests/day | $5/mo + 10M requests + 30M CPU-ms | Workers Paid required |
| D1 | 5GB / 5M reads / 100k writes daily | Included in Workers Paid | Adequate at expected scale |
| R2 | 10GB storage, 1M Class A ops/mo | Pay-per-use beyond | Modest cost |
| KV | 100k reads/day, 1k writes/day | $0.50/M reads, $5/M writes | Light usage |
| Queues | Not available on free | Included in Workers Paid | Required |
| Durable Objects | Limited on free | Included | Required for stateful coord |
| Vectorize | 30M queried vector dimensions free | Pay-per-use | TBD by EmbeddingService |
| AI Gateway | 100k logs/month free | 1M logs included in Workers Paid | Recommended |
| Browser Rendering | Limited free tier | Pay-per-use | For PDF rendering |
| Logpush | Free 10M records/month | $0.05/M beyond | Optional |

Realistic monthly platform cost: **£5–15/month** for development and modest production use. Scales with content production volume, not user traffic (which is on Astro, not NCEMPIRE).

> **OQ-COST-1** (from previous review): Consolidated cost ceiling and monitoring still needs a Pass 0 home. CostBudgetManager covers AI cost specifically, not platform cost.

---

## 3. Cloudflare Services Enabled

Each service used by NCEMPIRE, why it's used, and the day-one setup required.

### 3.1 — Workers

**What:** Edge-distributed TypeScript runtime. Where all NCEMPIRE compute runs.

**Why:** Native fetch-based runtime, V8 isolates, edge-distributed, sub-ms cold start, paid plan supports 5-minute CPU per invocation.

**Setup:** No account-level setup; created per-Worker via `wrangler deploy`.

### 3.2 — Durable Objects

**What:** Stateful, single-threaded objects with their own SQLite storage. Per-object hibernation.

**Why:** Stateful coordination for systems whose Pass 0 indicates state ownership: rate limiting per tenant/provider, orchestrator workflows mid-flight, review queue state, human handoff state. Specific assignments deferred to per-system Pass 1.

**Setup:** Declared inside Wrangler config of the Worker that owns them. No separate account-level configuration.

### 3.3 — D1

**What:** SQLite at the edge. Per-database, queryable, transactional.

**Why:** NCEMPIRE's library data (brands, themes, etc.) was originally specified for SQLite. D1 is the direct equivalent. Also the right home for audit, decision timeline, library registry.

**Setup:**

```bash
wrangler d1 create ncempire-registry-dev
wrangler d1 create ncempire-brands-dev
# …one create per library, per environment
```

Each `d1 create` returns a database ID that goes into the relevant Worker's `wrangler.jsonc` binding.

> Specific database topology (one shared D1 vs one per library) is **deferred to Pass 0**, per the previous review. The day-one scaffold uses one shared `ncempire-core` D1 for the platform Worker; per-library splits can be introduced as Pass 0 verdicts warrant.

### 3.4 — R2

**What:** Object storage with no egress fees. S3-compatible API.

**Why:** Storage for: draft staging (per-library JSON drafts before promotion to D1), generated content artefacts (final validated JSON), audit log archives if Logpush is used, anything large or file-shaped.

**Setup:**

```bash
wrangler r2 bucket create ncempire-drafts-dev
wrangler r2 bucket create ncempire-content-dev
wrangler r2 bucket create ncempire-artefacts-dev
# …per environment
```

### 3.5 — KV

**What:** Eventually-consistent key-value store at the edge. Fast reads, TTL support.

**Why:** Feature flags, hot config, rate-limit counters, cache for resolved prompts. Not for primary state.

**Setup:**

```bash
wrangler kv:namespace create FLAGS --env dev
wrangler kv:namespace create CACHE --env dev
```

### 3.6 — Queues

**What:** Producer-consumer queues with retry/backoff and dead-letter support.

**Why:** Async inter-system communication for multi-stage workflows, audit event fan-out, rate-paced AI calls.

**Setup:**

```bash
wrangler queues create ncempire-generation-theme-dev
wrangler queues create ncempire-generation-outline-dev
wrangler queues create ncempire-audit-events-dev
# …one queue per workflow stage, per environment
```

Specific queue topology is deferred to orchestration system Pass 1; day-one scaffold has a `general` queue and an `audit-events` queue.

### 3.7 — Vectorize

**What:** Managed vector database for embeddings.

**Why:** Required for `services/VectorStore`. Embedding-based semantic search across NCEMPIRE content.

**Setup:**

```bash
wrangler vectorize create ncempire-embeddings-dev --dimensions=1536 --metric=cosine
```

### 3.8 — AI Gateway

**What:** Proxy/observability layer between Workers and external AI providers.

**Why:** Centralised AI call logging, caching, rate-limit visibility across providers. Optional but strongly recommended given NCEMPIRE's heavy AI usage.

**Setup:** Created in the Cloudflare dashboard under AI > AI Gateway. Provides a base URL that replaces direct provider URLs in AICaller.

### 3.9 — Browser Rendering

**What:** Headless Chromium running in Workers, for PDF/screenshot generation.

**Why:** Required for `renderers/PDFRenderer`. Generates PDFs from HTML.

**Setup:** Enabled per-account; called via Workers' Browser API.

### 3.10 — Workers Secrets

**What:** Encrypted secret storage scoped to a Worker and environment.

**Why:** API keys (AI providers, Canva, Google APIs, YouTube), any other credential.

**Setup:** Per-Worker, per-environment:

```bash
wrangler secret put ANTHROPIC_API_KEY --env dev
wrangler secret put OPENAI_API_KEY --env dev
# …per provider, per environment
```

### 3.11 — Cloudflare Tunnel (conditional)

**What:** Reverse tunnel from a local machine to Cloudflare's edge.

**Why:** Only if NCEMPIRE needs to call a local LLM (Ollama on dedicated hardware) from Workers. Per OQ-AI-2, this is deferred.

**Setup:** Install `cloudflared` on the local machine; create a tunnel; route a hostname to the local LLM port. Not part of day-one scaffold.

### 3.12 — Logpush (optional)

**What:** Stream Workers Logs to external storage.

**Why:** Long-term log retention beyond Workers' default. NCEMPIRE's audit data lives in D1 (durable by design), so Logpush is for technical logs only.

**Setup:** Configure Logpush job in Cloudflare dashboard, pointing at an R2 bucket.

---

## 4. Project Structure

Single npm package, multiple Workers organised by system. The repo lives in one Git repository, deploys multiple Workers from one `package.json`.

### 4.1 — Top-level layout

```
ncempire/
├── package.json                    # Single package, all dependencies
├── tsconfig.json                   # Base TypeScript config
├── tsconfig.base.json              # Shared compiler options
├── .gitignore
├── .editorconfig
├── README.md
├── CLAUDE.md                       # AI collaboration contract (already exists)
├── Project-Intent.md               # (already exists)
├── STACK-AND-RUNTIME.md            # this document
│
├── workers/                        # One folder per Cloudflare Worker
│   ├── platform/                   # Foundational Worker: services, system, state
│   │   ├── wrangler.jsonc
│   │   ├── src/
│   │   │   └── index.ts            # Worker entry point
│   │   └── README.md
│   ├── ai/                         # ai system Worker
│   │   ├── wrangler.jsonc
│   │   ├── src/
│   │   │   └── index.ts
│   │   └── README.md
│   ├── brand/                      # brand system Worker (may need splitting; per Pass 0)
│   │   ├── wrangler.jsonc
│   │   ├── src/
│   │   │   └── index.ts
│   │   └── README.md
│   ├── content/
│   ├── orchestration/
│   ├── audit/
│   ├── access/
│   └── … per-system folders …
│
├── src/                            # Shared source code, importable by all Workers
│   ├── systems/                    # Subsystem implementations grouped by system
│   │   ├── access/
│   │   │   ├── BrandPermissionResolver.ts
│   │   │   ├── CapabilityManager.ts
│   │   │   └── RoleManager.ts
│   │   ├── ai/
│   │   │   ├── AICaller.ts
│   │   │   ├── AISystemManager.ts
│   │   │   └── … per FileTree …
│   │   ├── brand/
│   │   │   ├── core/
│   │   │   ├── identity/
│   │   │   └── … per FileTree groups …
│   │   └── … all 27 systems …
│   │
│   ├── schemas/                    # Zod schemas (one file per major type)
│   │   ├── Brand.ts
│   │   ├── Audience.ts
│   │   ├── Theme.ts
│   │   └── … per documented schema …
│   │
│   ├── shared/                     # Cross-cutting utilities
│   │   ├── types.ts
│   │   ├── errors.ts               # ERROR-CODES.md as code
│   │   ├── constants.ts            # constants.md as code
│   │   └── enums.ts                # shared enums
│   │
│   └── lib/                        # Pure helpers, no Workers dependencies
│       ├── tokenize/
│       ├── validate/
│       └── format/
│
├── migrations/                     # D1 schema migrations
│   ├── 0001_initial.sql
│   ├── 0002_brand_isolation.sql
│   └── …
│
├── scripts/                        # Maintenance scripts (run via wrangler or local)
│   ├── seed-dev.ts
│   ├── deploy-all.sh
│   └── …
│
├── specs/                          # Per-subsystem specs (the .ts spec deliverables)
│   └── … mirrors src/systems/ structure …
│
├── docs/                           # All architecture docs (existing structure)
│   ├── DOCS - Preplanning/
│   ├── DOCS - StructureDefined/
│   └── …
│
└── tests/                          # Test files (Vitest)
    ├── unit/
    └── integration/
```

### 4.2 — How Workers consume shared code

Each Worker's `src/index.ts` imports from the shared `src/` directory. The Worker is a thin entry point that wires together the modules it needs.

Example: `workers/ai/src/index.ts`:

```typescript
import { AICaller } from '../../src/systems/ai/AICaller';
import { ModelManager } from '../../src/systems/ai/ModelManager';
import { TokenManager } from '../../src/systems/ai/TokenManager';
import type { Env } from './env';

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // Worker entry: route requests, delegate to subsystems
    const aiCaller = new AICaller(env);
    // …
  },
  async queue(batch: MessageBatch, env: Env): Promise<void> {
    // Queue consumer: handle async work
  },
  async scheduled(event: ScheduledEvent, env: Env): Promise<void> {
    // Cron trigger
  },
};
```

### 4.3 — File-size rule enforcement

Project-Intent.md mandates no single file > ~2000 LOC (TypeScript runtime, with file exclusions). Two mechanisms:

1. **ESLint rule** in `.eslintrc.js`: `max-lines` configured at warning (1200) and error (1500)
2. **CI check**: `scripts/check-file-sizes.sh` fails build if any runtime `.ts` file exceeds 2000 lines (excludes `*.test.ts`, `*.debug.ts`, `__debug__/`, `*.types.ts`)

### 4.4 — Why not per-system packages

Considered: workspace-based monorepo (one `package.json` per Worker, npm/pnpm workspaces). Rejected because:

- Single package is simpler to operate at NCEMPIRE's scale
- Shared types and utilities are directly importable without workspace plumbing
- No version-sync issues between internal packages
- TypeScript's `paths` config in `tsconfig.json` gives clean import paths without workspaces

If specific systems eventually need independent versioning (unlikely), the monorepo can be migrated to workspaces without rewriting code.

---

## 5. Required Configuration Files

Each config file, what it does, what's in it, and example content.

### 5.1 — `package.json` (root)

Single package; declares all dependencies for all Workers.

```jsonc
{
  "name": "ncempire",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev:platform": "wrangler dev --config workers/platform/wrangler.jsonc",
    "dev:ai": "wrangler dev --config workers/ai/wrangler.jsonc",
    "dev:multi": "wrangler dev -c workers/platform/wrangler.jsonc -c workers/ai/wrangler.jsonc",
    "deploy:platform:staging": "wrangler deploy --config workers/platform/wrangler.jsonc --env staging",
    "deploy:all:prod": "scripts/deploy-all.sh prod",
    "typecheck": "tsc --noEmit",
    "test": "vitest run",
    "test:watch": "vitest",
    "lint": "eslint src workers",
    "format": "prettier --write 'src/**/*.ts' 'workers/**/*.ts'"
  },
  "dependencies": {
    "zod": "^3.x",
    "@anthropic-ai/sdk": "^0.x",
    "openai": "^4.x",
    "@anthropic-ai/tokenizer": "^0.x",
    "opentype.js": "^1.x",
    "docx": "^8.x"
  },
  "devDependencies": {
    "wrangler": "^3.x",
    "typescript": "^5.x",
    "@cloudflare/workers-types": "^4.x",
    "@cloudflare/vitest-pool-workers": "^0.x",
    "vitest": "^1.x",
    "eslint": "^8.x",
    "prettier": "^3.x"
  }
}
```

### 5.2 — `tsconfig.json` (root)

```jsonc
{
  "extends": "./tsconfig.base.json",
  "include": ["src/**/*", "workers/**/*", "tests/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### 5.3 — `tsconfig.base.json`

```jsonc
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ES2022",
    "moduleResolution": "Bundler",
    "lib": ["ES2022"],
    "types": ["@cloudflare/workers-types"],
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "resolveJsonModule": true,
    "paths": {
      "@systems/*": ["./src/systems/*"],
      "@schemas/*": ["./src/schemas/*"],
      "@shared/*": ["./src/shared/*"],
      "@lib/*": ["./src/lib/*"]
    }
  }
}
```

### 5.4 — `workers/<worker-name>/wrangler.jsonc`

One per Worker. Example for the `platform` Worker:

```jsonc
{
  "$schema": "node_modules/wrangler/config-schema.json",
  "name": "ncempire-platform",
  "main": "src/index.ts",
  "compatibility_date": "2026-05-14",
  "compatibility_flags": ["nodejs_compat"],

  // Per-environment overrides
  "env": {
    "dev": {
      "name": "ncempire-platform-dev",
      "vars": {
        "ENVIRONMENT": "dev",
        "LOG_LEVEL": "debug"
      },
      "d1_databases": [
        {
          "binding": "REGISTRY_DB",
          "database_name": "ncempire-registry-dev",
          "database_id": "<dev-d1-id>"
        }
      ],
      "r2_buckets": [
        {
          "binding": "DRAFTS",
          "bucket_name": "ncempire-drafts-dev"
        },
        {
          "binding": "CONTENT",
          "bucket_name": "ncempire-content-dev"
        }
      ],
      "kv_namespaces": [
        {
          "binding": "FLAGS",
          "id": "<dev-kv-id>"
        }
      ],
      "queues": {
        "producers": [
          { "binding": "AUDIT_QUEUE", "queue": "ncempire-audit-events-dev" }
        ],
        "consumers": [
          { "queue": "ncempire-general-dev", "max_batch_size": 10 }
        ]
      },
      "services": [
        { "binding": "AI_WORKER", "service": "ncempire-ai-dev" },
        { "binding": "BRAND_WORKER", "service": "ncempire-brand-dev" }
      ]
    },
    "staging": { /* …staging values… */ },
    "prod":    { /* …prod values… */ }
  }
}
```

### 5.5 — `.dev.vars` (per-Worker, gitignored)

Local development secrets. Not committed.

```
ANTHROPIC_API_KEY=sk-...
OPENAI_API_KEY=sk-...
```

Production secrets are set via `wrangler secret put`, never in `.dev.vars`.

### 5.6 — `.gitignore`

```
node_modules/
dist/
.wrangler/
.dev.vars
.dev.vars.local
*.log
.DS_Store
```

### 5.7 — `vitest.config.ts`

```typescript
import { defineWorkersConfig } from '@cloudflare/vitest-pool-workers/config';

export default defineWorkersConfig({
  test: {
    poolOptions: {
      workers: {
        wrangler: { configPath: './workers/platform/wrangler.jsonc' },
      },
    },
  },
});
```

### 5.8 — `.editorconfig`

```
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
```

### 5.9 — `.eslintrc.js`

```javascript
module.exports = {
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: './tsconfig.json',
    tsconfigRootDir: __dirname,
  },
  plugins: ['@typescript-eslint'],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/recommended-requiring-type-checking',
  ],
  rules: {
    'max-lines': ['warn', { max: 1600, skipBlankLines: true, skipComments: true }],
    'max-lines-per-function': ['warn', 100],
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/explicit-function-return-type': 'warn',
  },
};
```

### 5.10 — CI: `.github/workflows/deploy.yml`

```yaml
name: Deploy NCEMPIRE
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm run typecheck
      - run: npm run lint
      - run: npm run test
      - run: scripts/check-file-sizes.sh

  deploy-staging:
    needs: test
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: scripts/deploy-all.sh staging
        env:
          CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
```

Production deployment is manual (workflow_dispatch) to keep a human gate per the methodology.

---

## 6. Bindings and Resources

Workers connect to Cloudflare resources via **bindings** declared in `wrangler.jsonc`. Bindings appear as properties on the `env` object inside the Worker.

### 6.1 — Binding naming conventions

| Resource type | Naming pattern | Example |
|---|---|---|
| D1 database | `<DOMAIN>_DB` | `BRANDS_DB`, `AUDIT_DB`, `REGISTRY_DB` |
| R2 bucket | `<purpose>` (uppercase) | `DRAFTS`, `CONTENT`, `ARTEFACTS` |
| KV namespace | `<purpose>` (uppercase) | `FLAGS`, `CACHE`, `RATE_LIMITS` |
| Queue (producer) | `<NAME>_QUEUE` | `AUDIT_QUEUE`, `GENERATION_QUEUE` |
| Service binding | `<TARGET>_WORKER` | `AI_WORKER`, `BRAND_WORKER` |
| Durable Object | `<NAME>` (PascalCase) | `Orchestrator`, `RateLimiter` |
| Vectorize index | `<PURPOSE>_INDEX` | `EMBEDDINGS_INDEX` |

### 6.2 — Naming the resources themselves

Cloudflare resource names include environment for clarity:

```
ncempire-<resource-type>-<purpose>-<env>

Examples:
  ncempire-d1-brands-dev
  ncempire-r2-drafts-staging
  ncempire-queue-audit-prod
```

### 6.3 — Day-one bindings checklist

For each Worker, on first deploy:

1. Run `wrangler types` to generate `worker-configuration.d.ts` from `wrangler.jsonc`
2. The generated `Env` type provides type-safety for all bindings
3. Import in Worker: `import type { Env } from './worker-configuration'` (or wherever generated)
4. Use bindings via `env.BINDING_NAME`

---

## 7. Secrets and Environment Variables

### 7.1 — What's a secret vs what's a var

| Type | Where | Use for |
|---|---|---|
| Secret | `wrangler secret put` / `.dev.vars` | API keys, tokens, anything sensitive |
| Var | `vars` block in `wrangler.jsonc` | Non-sensitive config: log levels, feature flags, env names |

### 7.2 — Required secrets

| Name | Used by | Source |
|---|---|---|
| `ANTHROPIC_API_KEY` | ai-worker (AICaller) | https://console.anthropic.com |
| `OPENAI_API_KEY` | ai-worker (AICaller) | https://platform.openai.com |
| `GOOGLE_AI_API_KEY` | ai-worker (AICaller, optional) | https://aistudio.google.com |
| `GROQ_API_KEY` | ai-worker (AICaller, optional) | https://console.groq.com |
| `OPENROUTER_API_KEY` | ai-worker (AICaller, optional) | https://openrouter.ai |
| `CANVA_CLIENT_ID` | integrations-worker | Canva developer portal |
| `CANVA_CLIENT_SECRET` | integrations-worker | Canva developer portal |
| `GOOGLE_OAUTH_CLIENT_ID` | integrations-worker | Google Cloud Console |
| `GOOGLE_OAUTH_CLIENT_SECRET` | integrations-worker | Google Cloud Console |
| `YOUTUBE_API_KEY` | integrations-worker | Google Cloud Console |
| `AI_GATEWAY_TOKEN` | ai-worker | Cloudflare AI Gateway (if used) |

### 7.3 — Setting secrets

Per-Worker, per-environment:

```bash
# For each Worker that needs it
cd workers/ai
wrangler secret put ANTHROPIC_API_KEY --env dev
wrangler secret put ANTHROPIC_API_KEY --env staging
wrangler secret put ANTHROPIC_API_KEY --env prod
```

### 7.4 — Secret rotation

Manual via `wrangler secret put`. Rotation policy belongs to Pass 0 (OQ-SEC-1 from previous review).

---

## 8. Local Development

### 8.1 — Single-Worker dev

```bash
cd ncempire
npm install
npm run dev:platform
```

`wrangler dev` runs the Worker locally with:

- Full D1 emulation (local SQLite)
- Full R2 emulation (local filesystem)
- Full KV emulation
- Full Queues emulation
- Full Durable Objects emulation

### 8.2 — Multi-Worker dev (Service Bindings)

```bash
wrangler dev -c workers/platform/wrangler.jsonc -c workers/ai/wrangler.jsonc -c workers/brand/wrangler.jsonc
```

The first config is the primary (exposed over HTTP). The others are reachable via Service Bindings from the primary.

### 8.3 — Practical reality of dev loop at 27 systems

The honest answer: running all 31 Worker configs locally at once is slow and may exceed local resource limits. Practical patterns:

- **Most days**: run only the Workers you're actively working on (`-c` flags for those)
- **Integration testing**: run a foundational subset (platform, ai, brand, content, orchestration)
- **Full system**: deployed to `dev` environment in actual Cloudflare; test against that

> **OQ-DEV-2** (from previous review): Local dev loop reality for 31 Workers is a real concern. Document this and accept the trade.

### 8.4 — D1 local development

Local D1 lives at `.wrangler/state/v3/d1/`. Migrations applied locally via:

```bash
wrangler d1 migrations apply REGISTRY_DB --local
wrangler d1 migrations apply REGISTRY_DB --remote --env dev
```

### 8.5 — Seeding dev data

```bash
npm run seed:dev
```

`scripts/seed-dev.ts` populates local D1 with starter fixtures (test brand, test audience, test theme) for local development.

---

## 9. Deployment

### 9.1 — Single-Worker deploy

```bash
wrangler deploy --config workers/<worker-name>/wrangler.jsonc --env <env>
```

### 9.2 — All-Workers deploy

```bash
scripts/deploy-all.sh staging
```

Script iterates over `workers/*/wrangler.jsonc` and deploys each in dependency order (platform first, then dependents).

### 9.3 — Deploy order

Service Bindings mean Worker A binding to Worker B requires Worker B to exist first. Day-one deploy order:

1. `platform` (foundational, no Service Bindings inward)
2. `audit`, `access`, `state` (foundational dependents)
3. `ai` (depended on by most others)
4. `brand`, `content`, `template` (mid-tier)
5. `orchestration`, `resilience`, `review` (cross-cutting)
6. `email`, `social`, `documents`, `website`, etc. (leaf)

Exact order encoded in `scripts/deploy-all.sh`.

### 9.4 — D1 migrations

Applied before Worker deploy:

```bash
wrangler d1 migrations apply REGISTRY_DB --remote --env staging
# … per database …
```

Migration files live in `migrations/` and are versioned. The methodology mandates migrations are append-only — no destructive migrations without explicit human approval per CLAUDE.md.

### 9.5 — Rolling back

Per CLAUDE.md, append-only design throughout. Rollback is "deploy the previous version":

```bash
git checkout <previous-tag>
scripts/deploy-all.sh prod
```

Cloudflare retains the previous Worker version for 24 hours, accessible via instant rollback in the dashboard, as a faster path.

### 9.6 — CI/CD

`.github/workflows/deploy.yml` triggers on push to `main`:

- Typecheck, lint, test, file-size check on every push
- Auto-deploy to staging on merge to main
- Production deploy is manual (workflow_dispatch)

---

## 10. Minimum Viable Scaffold (Day One)

The smallest possible NCEMPIRE that exists on Cloudflare. No subsystems yet — just the bones.

### 10.1 — Files that must exist

```
ncempire/
├── package.json
├── tsconfig.json
├── tsconfig.base.json
├── .gitignore
├── .editorconfig
├── .eslintrc.js
├── vitest.config.ts
├── README.md
├── CLAUDE.md                       # already exists
├── Project-Intent.md               # already exists
├── STACK-AND-RUNTIME.md            # this document
│
├── workers/
│   └── platform/
│       ├── wrangler.jsonc
│       ├── src/
│       │   └── index.ts            # bare "Hello NCEMPIRE" worker
│       └── README.md
│
├── src/
│   ├── shared/
│   │   ├── types.ts
│   │   ├── errors.ts               # placeholder for ERROR-CODES.md content
│   │   └── enums.ts                # shared enums from constants.md
│   └── schemas/
│       └── .gitkeep                # waiting for first schema
│
├── migrations/
│   └── 0001_initial.sql            # creates _index table for library registry
│
├── scripts/
│   ├── deploy-all.sh
│   ├── check-file-sizes.sh
│   └── seed-dev.ts
│
├── tests/
│   └── .gitkeep
│
└── .github/
    └── workflows/
        └── deploy.yml
```

### 10.2 — Cloudflare resources that must exist on day one

| Resource | Name | Purpose |
|---|---|---|
| Account | NCEMPIRE | Hosts everything |
| Workers Paid plan | (subscription) | Required for Queues, longer CPU |
| D1 database | `ncempire-registry-dev` | Library registry, first migrations |
| R2 bucket | `ncempire-drafts-dev` | Draft staging area |
| KV namespace | `ncempire-flags-dev` | Feature flags |

That's enough to deploy the platform Worker and confirm everything is connected. No actual NCEMPIRE work runs yet.

### 10.3 — Day-one platform Worker (`workers/platform/src/index.ts`)

```typescript
export interface Env {
  REGISTRY_DB: D1Database;
  DRAFTS: R2Bucket;
  FLAGS: KVNamespace;
  ENVIRONMENT: string;
  LOG_LEVEL: string;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    return new Response(
      JSON.stringify({
        status: 'ok',
        environment: env.ENVIRONMENT,
        message: 'NCEMPIRE platform Worker scaffold. No subsystems deployed yet.',
      }),
      { headers: { 'Content-Type': 'application/json' } }
    );
  },
};
```

That's the entire day-one deploy. Confirms the account, account-level resources, Worker, bindings, and deployment pipeline all work end-to-end before any subsystem code is written.

### 10.4 — Day-one verification

```bash
# Local: scaffold runs locally
npm run dev:platform
curl http://localhost:8787
# → {"status":"ok","environment":"dev","message":"…scaffold…"}

# Deploy to Cloudflare dev:
npm run deploy:platform:dev
curl https://ncempire-platform-dev.<account-subdomain>.workers.dev
# → {"status":"ok","environment":"dev","message":"…scaffold…"}
```

If both work, the scaffold is correct. Subsystems can now be added.

---

## 11. Adding a New System

The recipe for adding the next system to the project (once scaffold exists).

### 11.1 — Steps

1. **Confirm the system's Pass 0 is complete.** Per CLAUDE.md, no implementation work before Pass 0 verdict. If Pass 0 isn't done, stop and complete it.
2. **Create the Worker folder**: `workers/<system-name>/`
3. **Create `wrangler.jsonc`** in that folder, with appropriate bindings (D1 if owns a library; Service Bindings to upstream Workers; etc.)
4. **Create `src/index.ts`** as the Worker entry point
5. **Add subsystem implementations** under `src/systems/<system-name>/<SubsystemName>.ts`
6. **Add or update Zod schemas** in `src/schemas/` as needed
7. **Add D1 migrations** in `migrations/` if the system owns a library
8. **Add the Worker to deploy script** (`scripts/deploy-all.sh`) in correct dependency order
9. **Add the Service Binding** to upstream Workers' `wrangler.jsonc` if other Workers will call this one
10. **Write tests** in `tests/`
11. **Deploy to dev**, verify, commit, push, CI deploys to staging

### 11.2 — Worker creation checklist

For each new Worker:

- [ ] Folder created at `workers/<name>/`
- [ ] `wrangler.jsonc` with env-scoped bindings
- [ ] `src/index.ts` with `fetch` / `queue` / `scheduled` handlers as needed
- [ ] D1 databases created via `wrangler d1 create` (per env) if owned
- [ ] R2 buckets created via `wrangler r2 bucket create` (per env) if owned
- [ ] Queues created via `wrangler queues create` (per env) if owned
- [ ] Secrets set via `wrangler secret put` per env if needed
- [ ] Migrations applied if D1 schema needed
- [ ] Service Bindings declared in callers' configs
- [ ] Added to `scripts/deploy-all.sh`
- [ ] Tests in `tests/`
- [ ] README.md in the Worker folder explaining its scope

### 11.3 — Subsystem implementation pattern

For each subsystem (one TypeScript file per `.py` in FileTree):

```typescript
// src/systems/<system>/<SubsystemName>.ts

import type { Env } from '@shared/types';
import { type SchemaName } from '@schemas/SchemaName';

/**
 * <SubsystemName>
 *
 * Responsibility: <one-sentence purpose from Pass 0>
 * Owns: <what this subsystem owns>
 * Does not own: <what it doesn't>
 * Dependencies: <upstream subsystems>
 * Used by: <downstream consumers>
 */
export class SubsystemName {
  constructor(private readonly env: Env) {}

  // …methods following the per-subsystem spec…
}
```

Each subsystem's per-file spec (the spec template work, deferred) defines its exact interface. This document doesn't drill into subsystem-level structure.

---

## 12. Cost and Capacity Overview

### 12.1 — Realistic monthly cost

| Component | Cost (£/mo, modest production use) |
|---|---|
| Workers Paid (base) | £4 |
| D1 (within free allowances) | £0 |
| R2 (a few GB) | £0–1 |
| KV (light) | £0 |
| Queues (within paid allowance) | £0 |
| Durable Objects (light state) | £0–1 |
| Vectorize (modest embeddings) | £0–2 |
| AI Gateway (within paid allowance) | £0 |
| Browser Rendering (modest PDF gen) | £0–3 |
| Logpush (optional) | £0–2 |
| **Total expected** | **£5–15** |

### 12.2 — Cost monitoring

Cloudflare dashboard provides per-service usage. Set account spend cap as a safety net.

> **OQ-COST-1** (deferred to Pass 0): Programmatic cost monitoring and ceilings. Manual dashboard checks sufficient for early stages.

---

## 13. Open Questions and Known Gaps

Carried forward from STACK-PROPOSAL review and not resolved by this document:

| ID | Question | Owner |
|---|---|---|
| OQ-PY-1 | PythonRunner resolution (drop/external/absorb) | Services Pass 0 |
| OQ-RT-1 | Continuous background processes for any specific subsystem | Pass 0 per system |
| OQ-STORE-1 | File-system path assumptions in existing Pass 0 docs | Stack Pass 0 |
| OQ-STORE-2 | Vectorize confirmed for VectorStore? | AI Pass 1 |
| OQ-COMM-1 | Queue topology vs orchestration system Pass 0 | Orchestration Pass 1 |
| OQ-AI-1 | AI Gateway adoption | AI Pass 1 |
| OQ-AI-2 | Local LLM (Ollama) routing | Future |
| OQ-OBS-1 | Audit retention | Audit Pass 0 |
| OQ-DEV-1 | Worker granularity per system | Per-system Pass 1 |
| OQ-DEV-2 | Local dev loop at 27-Worker scale | Documented in §8.3 |
| OQ-CRON-1 | Cron Trigger limits per system | Per-system Pass 1 |
| OQ-SEC-1 | Secret rotation policy | Services Pass 0 |
| OQ-COST-1 | Consolidated cost monitoring | Observability Pass 0 |
| OQ-TENANT-1 | Brand isolation at storage layer | Access + Storage Pass 0 |
| OQ-MIGR-1 | D1 schema migration strategy at scale | Pass 2 |
| OQ-DR-1 | D1 backup / disaster recovery / RPO/RTO | Pass 0 |

This document describes the **mechanism**; the open questions are about **policy and specific assignments** that belong in Pass-level documents.

---

## 14. What This Document Does Not Decide

- Which subsystems live in which Worker (deferred to per-system Pass 1)
- Exact queue names, retention, DLQ policies (Pass 2)
- D1 database splits per library vs shared (Pass 0)
- Cron schedules per subsystem (Pass 1)
- Failure recovery policies per system (resilience Pass 1)
- API surface of any individual Worker (per-system Pass 2/3)
- PythonRunner resolution (OQ-PY-1, blocking)

These remain Pass-managed concerns. This document is the substrate they run on.

---

## 15. Summary

NCEMPIRE on Cloudflare:

- **One Cloudflare account** for NCEMPIRE; brand websites separate.
- **Single npm package, multiple Workers** organised by system; foundational systems grouped into a `platform` Worker; per-system Workers for the rest.
- **TypeScript 5.x** on Cloudflare Workers, **wrangler.jsonc** for config.
- **D1** for library data and audit; **R2** for drafts and artefacts; **KV** for cache and feature flags; **Queues** for async; **Durable Objects** for stateful coordination; **Vectorize** for embeddings.
- **AI Gateway** in front of LLM providers; **Browser Rendering** for PDF.
- **Single shared D1 (`ncempire-registry`)** day-one; per-library splits when Pass 0 verdicts warrant.
- **dev / staging / prod** environments within the NCEMPIRE account, managed via Wrangler env flag.
- **Day-one scaffold** is a single platform Worker, three Cloudflare resources, no subsystems. Confirms the pipe is connected before subsystem work begins.
- **Adding systems** follows a documented recipe; each requires its Pass 0 complete first.
- **Cost** approximately £5–15/month at modest production scale.
- **16 open questions** carried forward to their appropriate Pass owners.

This document is the operational reference for *how it runs*, not *what runs*. The architecture (what runs) is locked in existing system docs; this is the substrate they deploy onto.

---

### Review & Clarification Needed
- Does this document accurately reflect how you want NCEMPIRE to run on Cloudflare?
- Are the day-one scaffold contents (§10) the right minimum, or is anything missing/over?
- Is the "add a new system" recipe (§11) sufficient, or does it need more detail?
- Are there services or configs needed that I haven't included?
- Should anything currently deferred to Pass be brought into this document instead?
- May this document be accepted as the operational reference, with the open questions in §13 routed to their Pass owners?

---

*Created: 2026-05-18*
*Type: Operational reference, not a Pass document*
*Status: Draft Complete – Awaiting Review*
