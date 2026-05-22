# STACK-PROPOSAL.md

**Status:** Draft Complete – Awaiting Review
**Type:** Pre-Pass-0 artefact (input to Stack Pass 0 assessment)
**Generated:** 2026-05-18
**Scope:** Proposed runtime, language, storage, and deployment mapping for NCEMPIRE

---

## Purpose of this Document

This document **proposes** how NCEMPIRE's locked architecture (31 systems, ~195 subsystems, documented across Project-Intent.md, system specs, and 17 completed Pass 0 coverage reviews) maps onto a concrete runtime and toolchain.

It is **not a decision**. It is the input that Stack Pass 0 will assess for sufficiency against NCEMPIRE's architectural requirements.

All recommendations below are **non-binding options** presented for review. Where the right answer is genuinely ambiguous or the architecture is unclear, that is flagged as an open question rather than chosen unilaterally.

---

## Confirmed Constraints (from prior decisions)

- **Language:** TypeScript on Cloudflare Workers (confirmed)
- **Methodology:** Strict PASS 0–4 applied to stack decisions (confirmed)
- **Heavy compute approach:** Workers as much as possible, fallback to external only as last resort (confirmed)
- **Project boundary:** NCEMPIRE ends at validated content in storage; the Astro website platform consumes separately and is out of scope (confirmed)
- **Project-Intent.md non-negotiables:** Architecture before implementation; documentation drives implementation; no module > ~1500 LOC; no hidden decision logic; no silent AI behaviour; no unbounded modules

---

## 1. Runtime Model

### 1.1 — Compute primitives proposed

| NCEMPIRE need | Cloudflare primitive | Rationale |
|---|---|---|
| HTTP-triggered work (API endpoints, on-demand generation) | **Workers** (fetch handler) | Standard request/response, edge-distributed, sub-ms cold start |
| Scheduled work (batch jobs, periodic checks, cleanup) | **Workers Cron Triggers** | Built-in, free tier, no separate scheduler service |
| Inter-system async work (queue-driven orchestration, retries) | **Cloudflare Queues** | Native producer/consumer, retry with backoff, dead-letter queues |
| Long-running stateful workflows (multi-stage generation pipelines) | **Durable Objects** + Queues | Stateful coordination, single-threaded per object, hibernation when idle |
| Heavy/long compute that exceeds Worker CPU limits | **Workers Paid plan** (5min CPU max) + Queue-batched processing | Most heavy work splits cleanly across queue stages |
| Genuinely unsuitable workloads (flagged below) | **External compute** (last resort) | Acknowledged but minimised |

### 1.2 — Worker CPU limits and what they imply

- **Free tier:** 10ms CPU per invocation. Acceptable for routing, validation, lightweight transformations. Insufficient for full generation pipelines.
- **Paid tier:** Up to 5 minutes CPU per invocation (configurable). Covers most NCEMPIRE work including multi-step AI orchestration where waiting on AI APIs doesn't count as CPU.
- **Implication:** NCEMPIRE requires the paid plan ($5/month minimum). This is consistent with the system's scope and ambition. Free tier is not viable for production NCEMPIRE.

### 1.3 — Where Durable Objects are warranted

Durable Objects are proposed for stateful coordination, not for general compute. Specifically:

- **Orchestrator** (orchestration system) — coordinating multi-stage generation pipelines with stateful progress
- **StateManager** (state system) — long-lived state per brand/job/session
- **RateLimiter** (orchestration) — per-tenant or per-provider rate state
- **HumanHandoffManager** (resilience) — workflows paused awaiting human input
- **ReviewQueue** (review) — queued items with state per reviewer

Subsystems that are stateless (transformations, validators, generators called per-request) should be **plain Workers**, not Durable Objects. Durable Objects have higher cost and latency than plain Workers and should not be the default.

### 1.4 — Open question

> **OQ-RT-1:** Some systems (e.g. ai/OptimizationLoop, content/ContentLifecycleManager) may need long-running background processes that don't fit cleanly into request/cron/queue triggers. Workers Cron + Queues should cover this in practice, but specific subsystems should be flagged during Pass 0 if they require continuous background execution.

---

## 2. Storage Model

### 2.1 — Storage primitives proposed

| Data class | Cloudflare primitive | Rationale |
|---|---|---|
| Structured library data (brands, voices, tones, audiences, themes, outlines, templates, frameworks) | **D1** (SQLite at edge) | Original spec assumed SQLite; D1 is the direct equivalent on Workers |
| Audit log, decision timeline, human override ledger | **D1** | Relational, queryable, joins to library data |
| Generated content artefacts (final JSON, validated outputs) | **R2** | Object storage, append-only friendly, no egress cost, edge-served |
| Caches (resolved prompts, rendered intermediates, validation results) | **Workers KV** | Eventually consistent, fast reads, TTL-based |
| Hot config (feature flags, rate limit windows, secrets references) | **Workers KV** or **Workers Secrets** | KV for config; Secrets for credentials |
| File-system-style paths in original spec (`/data/`, `/repos/`) | **R2** with key namespacing | Maps to object storage with prefix-based "directories" |
| Temporary work-in-progress per job | **Durable Object SQLite storage** | Co-located with the workflow that owns it |

### 2.2 — D1 specifics

- **D1 is SQLite-on-edge.** All `better-sqlite3` semantics from the original spec map cleanly, with these caveats:
  - D1 queries are async (not synchronous like better-sqlite3)
  - D1 has its own query language compatibility — virtually all standard SQL works
  - Write throughput is bounded by D1's underlying replication; for write-heavy workloads, consider Durable Object SQLite as an alternative

- **Database-per-domain vs database-shared:** NCEMPIRE has many distinct domain libraries (brands, content, templates, etc.). Two structures possible:
  - **Single shared D1 with namespaced tables** — simpler, fewer connection bindings, joins across domains are natural
  - **D1 per major domain group** (e.g. brand-d1, content-d1, audit-d1) — better isolation, harder to join across

- **Proposed default:** Single shared D1 for v1, with the option to split if specific domains show contention or operational pain. Pass 0 should validate this against the access system's brand-permission boundaries.

### 2.3 — R2 specifics

- **Append-only writes** are first-class in R2 — write to versioned keys, never overwrite. Maps directly to NCEMPIRE's audit-heavy, history-preserving architecture.
- **No egress cost** matters for downstream Astro consumption — NCEMPIRE writes content to R2, the Astro project reads it without inter-platform egress charges.
- **Key namespacing convention** proposed: `{system}/{subsystem}/{brand}/{type}/{id}/{version}` — e.g. `content/Content/mtb/article/article-123/v3`. Pass 0 should assess whether this namespacing covers all needs.

### 2.4 — KV specifics

- **KV is eventually consistent across global edge.** Acceptable for caches and config; **not acceptable** for primary state, audit data, or anything where stale reads cause correctness issues.
- **Hot path for rate limiting** — KV with TTL is the standard pattern. RateLimiter (orchestration) should use KV-backed counters, not D1.
- **Configuration distribution** — feature flags (orchestration/FeatureFlags) live in KV for fast global reads.

### 2.5 — Open questions

> **OQ-STORE-1:** Original spec uses file-system paths for some libraries. R2 mapping is mechanical, but specific file-system-coupling assumptions in existing Pass 0 documents need to be reviewed during Stack Pass 0 to confirm no implicit dependencies on local filesystem semantics.

> **OQ-STORE-2:** Vector store for embeddings (services/VectorStore) — D1 has experimental vector support, Cloudflare Vectorize is the dedicated service. Recommendation: **Vectorize** for production embedding workloads. Pass 0 should confirm.

---

## 3. Inter-System Communication

### 3.1 — Communication patterns proposed

| Pattern | When used | Mechanism |
|---|---|---|
| Synchronous request/response within a system | Internal subsystem calls | Direct TypeScript function calls / module imports |
| Synchronous request/response across systems | Brand check during content generation, etc. | Service Bindings (Worker-to-Worker, no HTTP overhead) |
| Asynchronous request across systems | Trigger downstream generation, audit emission, fan-out | Queues |
| Event broadcast (multiple consumers) | Brand change → trigger consistency check + alignment recheck + drift recompute | Queue per consumer, or fan-out via a dispatcher Worker |
| Long-running coordinated workflow | Multi-stage generation (theme → outline → section → paragraph) | Durable Object orchestrator + Queues for each stage |

### 3.2 — Service Bindings note

Service Bindings let one Worker call another directly without going over HTTP. This is the right mechanism for cross-system synchronous calls in NCEMPIRE (e.g. content generation calling brand/BrandCheck). It avoids:

- HTTP serialisation overhead
- Authentication ceremony between internal callers
- External network exposure of internal endpoints

External-facing endpoints (if any) remain fetch handlers; internal endpoints are reachable only via Service Bindings.

### 3.3 — Queue topology

Proposed approach: **one queue per logical workflow stage**, not one queue per system. This keeps queues focused and consumers single-purpose. Examples:

- `generation-theme` queue → consumer: ThemeGenerator
- `generation-outline` queue → consumer: OutlineGenerator
- `generation-section` queue → consumer: TemplateEngine (per-section work)
- `validation-content` queue → consumer: ContentValidator
- `audit-events` queue → consumer: audit system writers
- `human-handoff-pending` queue → consumer: HumanHandoffManager + Notification

This is a starting topology, not a decision. Pass 0 should validate against the existing orchestration system's documented behaviour.

### 3.4 — Open question

> **OQ-COMM-1:** NCEMPIRE has a heavily-documented orchestration system (17 subsystems including Orchestrator, RequestHandler, BatchProcessor, JobSimulator). The proposed queue topology assumes Orchestrator is the entry point that schedules into the right queues. Pass 0 must confirm this matches the orchestration system's existing Pass 0 conclusions, particularly around BatchProcessor's expected behaviour.

---

## 4. AI Provider Integration

### 4.1 — AICaller mapping

The architecture documents AICaller as supporting Anthropic (primary), OpenAI (fallback), Google AI, Groq, OpenRouter, Ollama. All of these are fetch-based and Workers-compatible.

**Proposed structure:**

- **AICaller** as a single Worker (or library module bound into calling Workers) that exposes a unified interface
- **Provider adapters** as separate modules, one per provider, each handling that provider's API quirks
- **AI Gateway** (Cloudflare's product) optionally fronted before the provider call for logging, caching, and rate limit visibility — adds value without adding lock-in
- **Failure classification** routes through resilience/FailureClassifier rather than being inside AICaller itself

### 4.2 — Tokeniser concern

Original spec assumes Python tiktoken. Workers cannot run tiktoken natively. Options:

- **`@anthropic-ai/tokenizer`** — official JS tokeniser for Claude models (Workers-compatible)
- **`gpt-tokenizer`** or **`tiktoken-node-wrapper`** WASM build — for OpenAI models
- **Approximate count** for non-Claude providers without official tokenisers, with a safety margin

Each provider adapter handles its own tokenisation. The TokenManager subsystem (ai system) coordinates budgets but doesn't tokenise itself.

### 4.3 — Open questions

> **OQ-AI-1:** AI Gateway has free-tier log limits (100k/month). For NCEMPIRE's scale, paid plan covers 1M/month. Confirm budget acceptance during Pass 0.

> **OQ-AI-2:** Local LLM support (Ollama) requires the local server to be reachable from Workers. Cloudflare Tunnel is the standard answer. Pass 0 should assess whether NCEMPIRE's deployment scenarios include local LLM routing or treat it as out-of-scope.

---

## 5. Observability and Audit

### 5.1 — Observability proposed

| Concern | Mechanism |
|---|---|
| Worker invocation logs | **Workers Logs** (built-in, retention varies by plan) |
| Cross-Worker tracing | **Workers Logs** with correlation IDs threaded through requests |
| Long-term log retention | **Logpush** to R2 (paid feature, $0.05/M records over 10M/month) |
| Metrics aggregation | **Custom D1 tables** for NCEMPIRE-specific metrics (cost, quality scores, etc.) |
| AI request logging | **AI Gateway** (optional but recommended) |
| Real-time monitoring | **Workers dashboard** for technical metrics; **D1 queries** for business metrics |

### 5.2 — Audit-specific concerns

NCEMPIRE has a dedicated audit system (AIUsageAttribution, DecisionTimeline, HumanOverrideLedger). These are not generic observability — they're authoritative records of decisions made.

**Proposed:**
- All audit records written to dedicated D1 tables (not log files)
- Writes are append-only by design — no UPDATE/DELETE of audit rows
- Each record carries the correlation ID linking it to the originating request
- Audit table schema is locked by the audit system's own Pass 3 spec (not by stack decisions)

### 5.3 — Open question

> **OQ-OBS-1:** Workers Logs retention is limited. For audit records, retention is determined by the audit system's policy, not by log retention. Pass 0 should confirm audit data lives in D1 (durable) rather than in logs (rolling).

---

## 6. Secrets and Configuration

### 6.1 — Secrets

- **Workers Secrets** for credentials: AI provider API keys, integration tokens (Canva, Google, YouTube), Stripe (if/when added), database connection details if any external DB is used
- **Per-environment bindings:** separate secret sets for dev/staging/prod; misfire in dev cannot publish to prod
- **APIKeyManager** (services/APIKeyManager) reads from Workers Secrets, never from D1 or KV

### 6.2 — Configuration

- **Static config** (model lists, token limits, error codes) lives in TypeScript constants files, version-controlled
- **Runtime config** (feature flags, brand-specific overrides) lives in KV, hot-readable
- **Tenant config** lives in D1 (brand records, audience records, etc.)

### 6.3 — Environments

Proposed: **three environments minimum** — `dev`, `staging`, `prod`. Each is a separate Cloudflare account or separate worker namespace within an account. Wrangler config supports environment-scoped bindings cleanly.

---

## 7. Development and Deployment

### 7.1 — Toolchain proposed

| Concern | Tool |
|---|---|
| Language | TypeScript 5.x (strict mode) |
| Runtime | Cloudflare Workers (V8 isolates) |
| Build/bundle | Wrangler (Cloudflare's official tool, uses esbuild internally) |
| Local dev | `wrangler dev` (full local Workers emulation including D1, R2, KV, Queues) |
| Schema validation | **Zod** for runtime schemas matching TypeScript types; **D1 migrations** for database schema |
| Testing | **Vitest** with `@cloudflare/vitest-pool-workers` for Workers-aware unit tests |
| CI/CD | GitHub Actions calling Wrangler for deploy on merge to main (or manual deploy for staged rollouts) |
| Monorepo structure | Yes — one repo, multiple Workers as packages |

### 7.2 — Repo structure proposed

```
ncempire/
├── packages/
│   ├── ai/                    # ai system Worker
│   ├── brand/                 # brand system Worker (or multiple per group)
│   ├── content/               # content system Worker
│   ├── orchestration/         # orchestration system Worker
│   ├── ...                    # one package per system
│   └── shared/
│       ├── schemas/           # Zod schemas shared across systems
│       ├── types/             # TypeScript types
│       └── utils/             # cross-cutting helpers
├── migrations/                # D1 schema migrations
├── wrangler.toml              # per-environment config
└── ...
```

This is a starting structure, not a decision. Pass 0 should assess whether one Worker per system is the right granularity, or whether some systems should be split further (e.g. brand into one Worker per group) or some merged (e.g. multiple small systems sharing a Worker).

### 7.3 — File size discipline

Project-Intent.md mandates no single build file > ~1500 LOC. TypeScript's import system makes this enforceable. Static linting can flag files approaching the limit. Pass 0 should consider whether this rule needs an enforcement mechanism in CI.

### 7.4 — Open question

> **OQ-DEV-1:** Wrangler supports monorepo with workspaces, but the right granularity (one Worker per system vs sharing Workers) depends on inter-system traffic patterns. Pass 0 should review the existing 17 Pass 0 documents for cross-system call frequency to inform this.

---

## 8. What Does Not Fit Workers Cleanly

Per the "Workers as much as possible, fallback to external only as last resort" principle, this section flags **honest constraints**, not preferences.

### 8.1 — PDF rendering (renderers/PDFRenderer)

- **Workers approach:** Cloudflare Browser Rendering (Workers Browser API) — headless Chromium running in Workers, can produce PDFs from HTML
- **Status:** Available, paid feature. Suitable for most PDF generation if HTML rendering produces the desired output.
- **Limit:** Heavy formatting (precise pagination, complex multi-column layouts) may require an alternative. For NCEMPIRE's scope (therapeutic content, workbooks, structured documents), Browser Rendering should suffice.
- **Recommendation:** Browser Rendering first; flag for review if specific document types prove unsuitable.

### 8.2 — DOCX, Google Docs, Sheets, Slides rendering

- **DOCX:** `docx` npm library (pure JS) runs in Workers. No external compute needed.
- **Google Docs/Sheets/Slides:** Google APIs are HTTP-based. Workers call them directly. The rendering happens on Google's side, not yours.
- **Recommendation:** Pure Workers for all of these.

### 8.3 — Font-to-path conversion (marks/FontToPathConverter)

- **Workers approach:** `opentype.js` or `fontkit` — both work in Workers
- **Status:** Viable
- **Recommendation:** Workers, with `opentype.js` as the proposed library.

### 8.4 — SVG operations (svg/* system)

- **Workers approach:** `@svgdotjs/svg.js` server-side, or direct DOM-string manipulation
- **Status:** Viable for the documented operations (boolean ops, transforms, path building, text parsing, optimisation)
- **Recommendation:** Workers.

### 8.5 — Image processing (image/AssetOptimizer)

- **Workers approach:** Cloudflare Images (dedicated product) for transformation/optimisation; for in-Workers manipulation, `@cloudflare/workers-types` Image API
- **Status:** Available; Cloudflare Images is the recommended path for transformation
- **Recommendation:** Cloudflare Images for transformations; Workers for orchestration.

### 8.6 — AI image generation (ai-image system)

- **Workers approach:** External API calls (OpenAI, Stability, Anthropic vision, Replicate, etc.)
- **Status:** HTTP-based, Workers-native
- **Recommendation:** Workers calling external providers via the AICaller pattern.

### 8.7 — Workers Browser Rendering vs full external compute

Browser Rendering is paid and has its own quotas. For very heavy rendering loads, an external service (e.g. a containerised Puppeteer service on Fly.io or similar) might be more economical at scale. **For NCEMPIRE's expected scale, Browser Rendering is recommended.** Revisit if Browser Rendering costs become significant.

### 8.8 — Summary

**Everything in NCEMPIRE's documented scope can run on Cloudflare's platform.** No system requires external compute as a fundamental constraint. Some systems use paid Cloudflare products (Browser Rendering, Images) rather than free Workers compute, but these stay within the Cloudflare ecosystem.

This is consistent with the "Workers as much as possible" principle.

---

## 9. External Dependencies

### 9.1 — Required external services

| Service | Purpose | Workers compatibility |
|---|---|---|
| AI providers (Anthropic, OpenAI, Google AI, etc.) | LLM calls | Native fetch |
| Canva API | Canva integration | HTTP, Workers-native |
| Google APIs (Docs, Sheets, Slides, Drive, etc.) | Document rendering and integrations | HTTP, Workers-native |
| YouTube API | YouTube integration | HTTP, Workers-native |
| Local LLM (Ollama, optional) | Future, for privacy-sensitive work | Requires Cloudflare Tunnel |

### 9.2 — Optional external services

None proposed at this stage. Pass 0 should flag any system that genuinely cannot be served by Cloudflare + existing external APIs.

### 9.3 — Open question

> **OQ-EXT-1:** Existing Pass 0 documents may have flagged external dependencies not listed here. Stack Pass 0 should cross-reference each system's Pass 0 against this list.

---

## 10. Known Constraints, Risks, and Open Questions

### 10.1 — Constraints

- **Workers CPU limits:** 5 minutes max on paid plan. Some pipelines must split across queue stages.
- **Workers memory limits:** 128MB per invocation. Sufficient for almost all NCEMPIRE work but flagged for any system processing large in-memory structures.
- **Request body size:** Plan-dependent. Free/Pro: 100MB. Acceptable for NCEMPIRE.
- **Subrequests per invocation:** 50 (free) / 1000 (paid). Most NCEMPIRE workflows fit within paid limits.
- **D1 throughput:** Bounded by underlying replication. Heavy write workloads may need to be sharded or use Durable Object SQLite.
- **Queue throughput:** Standard limits apply (10K msg/sec sustained per queue). Sufficient for NCEMPIRE's scale.

### 10.2 — Risks

- **Tooling maturity for Python-spec-to-TypeScript translation.** Original specs are language-agnostic at the contract level, but Python-specific patterns (decorators, dataclasses, etc.) may need TypeScript-idiomatic replacements. Pass 0 should sample one or two systems to assess.
- **D1 vs Durable Object SQLite decision.** Wrong choice causes operational pain. Default to D1; switch specific subsystems to Durable Object SQLite if write contention or co-location warrants.
- **AI provider availability and rate limits.** AICaller's fallback chain must be tested under realistic failure conditions. Resilience system handles this; Stack Pass 0 should confirm the providers chosen in the spec are all current and not deprecated.
- **Cost predictability.** Workers paid plan is $5/month minimum, with usage-based overages. For NCEMPIRE's expected scale, this should remain modest, but cost monitoring (ai/CostBudgetManager, observability/CostMonitor) becomes load-bearing.

### 10.3 — Consolidated open questions for Pass 0

| ID | Question |
|---|---|
| OQ-RT-1 | Systems requiring continuous background processes — flag during Pass 0 |
| OQ-STORE-1 | File-system-path assumptions in existing Pass 0 docs need review |
| OQ-STORE-2 | Vector store recommendation: Vectorize confirmed? |
| OQ-COMM-1 | Queue topology vs orchestration system's existing Pass 0 conclusions |
| OQ-AI-1 | AI Gateway paid tier budget acceptance |
| OQ-AI-2 | Local LLM (Ollama) routing in scope or out? |
| OQ-OBS-1 | Audit retention policy lives in audit system, not stack |
| OQ-DEV-1 | Worker granularity (one per system?) needs cross-system traffic data |
| OQ-EXT-1 | Cross-reference external deps against existing Pass 0 docs |

---

## 11. What This Proposal Does Not Decide

To honour the CLAUDE.md contract, this document **explicitly does not decide**:

- The exact D1 schema (lives in each system's Pass 3 spec)
- The exact queue names, retention, and DLQ policies (Stack Pass 2/3)
- The exact Worker boundaries (one-per-system vs shared) — flagged as OQ-DEV-1
- Whether to use AI Gateway (flagged as OQ-AI-1)
- Whether local LLM support is in scope (flagged as OQ-AI-2)
- Production cost ceilings (lives in CostBudgetManager's spec)
- Deployment cadence and release process (Stack Pass 2/3)
- The 9 open questions above

These remain for Pass 0 to assess and subsequent passes to specify.

---

## 12. Summary

This proposal recommends:

- **TypeScript on Cloudflare Workers** as the runtime
- **D1 + R2 + KV + Durable Objects** for storage, mapped per concern
- **Queues for async inter-system communication; Service Bindings for sync**
- **Fetch-based AI provider integration via AICaller pattern**
- **D1-backed audit; Workers Logs + optional Logpush for observability**
- **Wrangler-managed monorepo, one Worker per system as starting granularity**
- **Cloudflare-native services (Browser Rendering, Images) for heavy compute, with external compute reserved for genuine misfits (none identified at this stage)**

It flags **9 open questions** for Stack Pass 0 to resolve, and explicitly defers decisions that belong to later passes.

---

### Review & Clarification Needed
- Does this proposal align with NCEMPIRE's project intent and existing system contracts?
- Are there any corrections or refinements required before Stack Pass 0 begins?
- Are the 9 flagged open questions the right ones, or are there missing concerns Pass 0 should also address?
- May Stack Pass 0 proceed against this proposal, or should this proposal be revised first?
---
