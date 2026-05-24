# NCE-V2 — Project-Wide Spec

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Methodology Stage: 3 of 5
---

## Purpose

This document is the project-wide go/no-go for implementation. It consolidates cross-cutting concerns from all Stage 2 component specs into one place. After approval, the spec is locked. Implementation (Stage 4) proceeds against this doc.

---

## 1. API Surface

Every public Worker fetch handler + every cross-Worker service-binding interface.

### Public HTTP Endpoints (Worker fetch handlers)

| Worker | Method | Path | Auth | Request Type | Response Type | Notes |
|---|---|---|---|---|---|---|
| {{worker}} | GET / POST / PUT / DELETE | `/path/:param` | Public / Authenticated | `{{TS type}}` | `{{TS type}}` | |

### Cross-Worker Service Bindings

| Caller Worker | Target Worker | Function | Input Type | Output Type | Reason |
|---|---|---|---|---|---|
| | | | | | |

### Webhooks Received (from external providers)

| Endpoint | From Provider | Auth | Payload Type | Handler |
|---|---|---|---|---|
| | | | | `integrations/WebhookReceiver.{{method}}` |

### Response Envelope (canonical)

```typescript
type ApiResponse<T> =
  | { ok: true; data: T }
  | { ok: false; error: { code: string; message: string; details?: unknown } };
```

All HTTP responses use this envelope.

---

## 2. Error Codes Registry

Every error code used anywhere in NCE-V2. Format: `{SYSTEM}_{CATEGORY}_{SPECIFIC}`.

| Code | Category | HTTP Status | Owner Component | Recoverable | resilience/ Pattern |
|---|---|---|---|---|---|
| `BRAND_VALIDATION_INVALID_ID` | Validation | 400 | `brand/BrandManager` | No | N/A |
| `LIBRARY_DEPENDENCY_NOT_FOUND` | Dependency | 404 | `library/Librarian` | No | N/A |
| `EMAILIT_EXTERNAL_RATE_LIMITED` | External | 503 | `integrations/EmailitIntegration` | Yes | RateLimiter |
| `ASSETS_WORKER_MEMORY_EXCEEDED` | Worker | 500 | `assets/` | No | EscalationRouter |
| `UNKNOWN_UNEXPECTED_ERROR` | Unknown | 500 | (any) | No | EscalationRouter |

(Populated from every component spec's ERRORS section.)

### Categories

| Category | Meaning | Default HTTP Status |
|---|---|---|
| Validation | Invalid input | 400 |
| Dependency | Upstream / referenced entity not found / D1 timeout | 404 / 503 |
| State | Invalid state for operation | 409 |
| External | Third-party failure | 502 / 503 |
| Worker | CPU/memory limit hit | 500 |
| Unknown | Unexpected error | 500 |

---

## 3. Project-Wide Schemas

Shared TS types used across multiple components — candidates for `src/lib/types/`.

```typescript
// Universal entry status (used by every library)
export type EntryStatus = "draft" | "active" | "archived" | "flagged";

// Universal review status (for AI-generated entries)
export type ReviewStatus = "pending" | "approved" | "rejected";

// Universal envelope
export type Result<T, E = string> =
  | { ok: true; data: T }
  | { ok: false; error: E };

// Universal ID format
export type EntityId<Prefix extends string> = `${Prefix}_${string}`;

// Universal timestamp
export type ISOTimestamp = string; // ISO 8601

// (Add others as identified during Stage 2)
```

---

## 4. Conventions

(Pulled from CODING-STANDARDS.md once Stage 4 PreCode-Ready Check runs — but locked here.)

- TypeScript strict mode; no `any` without justification
- ESM only (no CommonJS — Workers don't support it)
- Result types at module boundaries
- `interface` for object shapes; `type` for unions/aliases
- File naming: PascalCase for classes (`BrandManager.ts`), camelCase for utility modules
- Types-only in `*.types.ts` (excluded from runtime LOC)
- Tests in `tests/<system>/<subsystem>.test.ts`
- Worker binding names: SCREAMING_SNAKE_CASE
- 2000 LOC per runtime file; exception requires logged justification
- Async/await everywhere; never floating promises
- `ctx.waitUntil()` for fire-and-forget work in Worker fetch handlers
- Always `await` D1 calls; D1 has no JSON column (use TEXT + `json_*()` functions)

---

## 5. Security Standards

- All Worker secrets via `env.{NAME}`; never in code or repo
- D1 queries always parameterized (`db.prepare("...?...").bind(...)`)
- R2 access: presigned URLs for client-side (where applicable), direct binding otherwise
- Input validation: every Worker fetch handler validates request against TS schema
- Output sanitisation: no PII or secrets in error messages (use opaque error codes)
- `access/` system gates every Worker's auth check
- CORS policy: per API-STANDARDS.md (consolidated here)
- Rate limiting: `resilience/RateLimiter` mandatory on outbound integrations
- Audit trail: `audit/` system logs critical operations (writes, AI decisions, human overrides)

---

## 6. Tech Stack (locked)

| Category | Choice | Version |
|---|---|---|
| Language | TypeScript | ^5.x |
| Runtime | Cloudflare Workers | (paid plan) |
| Database | D1 | (Cloudflare) |
| Object store | R2 | (Cloudflare) |
| Cache | KV | (Cloudflare) |
| State coordination | Durable Objects | (Cloudflare) |
| Embeddings | Vectorize | (Cloudflare) |
| Email send | Emailit | via `integrations/EmailitIntegration` |
| Downstream web | Astro | (consumer; separate project or sibling) |
| Package manager | pnpm | latest |
| Build | wrangler | latest |
| Test framework | vitest | latest |
| Worker testing | miniflare | latest |
| Lint + format | biome | latest |
| CI | GitHub Actions | — |

---

## 7. Repo Structure

```
nce-v2/                                  # repo root
├── README.md
├── package.json                         # workspace root
├── pnpm-workspace.yaml
├── tsconfig.base.json
├── .gitignore
├── biome.json
├── .github/                             # CI/CD workflows (Stage 5)
│
├── src/
│   ├── platform/                        # platform Worker (services + system + state + library)
│   │   ├── services/
│   │   ├── system/
│   │   ├── state/
│   │   ├── library/
│   │   └── index.ts                     # fetch handler entry
│   │
│   ├── access/                          # own Worker
│   ├── ai/                              # own Worker (large; consider sub-Workers if >2000 LOC per file)
│   ├── assets/
│   ├── audit/
│   ├── brand/                           # own Worker; 31 subsystems
│   ├── checks/
│   ├── content/
│   ├── document-templates/
│   ├── documents/
│   ├── email/
│   ├── integrations/
│   ├── marks/
│   ├── observability/
│   ├── orchestration/
│   ├── publishing/
│   ├── renderers/
│   ├── resilience/
│   ├── review/
│   ├── social/
│   ├── template/
│   ├── verification/
│   ├── versioning/
│   ├── website/
│   │
│   └── lib/                             # shared utilities (no Pass 0)
│       └── svg/
│
├── tests/                               # parallel to src/
│
├── migrations/                          # D1 migrations per library
│   ├── brands/
│   ├── audiences/
│   └── ...
│
└── wrangler/                            # per-Worker wrangler.toml
    ├── platform.toml
    ├── access.toml
    └── ...
```

---

## 8. Environment

Three tiers per Worker (local / staging / production). Per-tier:
- D1 binding `{LIBRARY}_DB-{env}` (e.g. `BRANDS_DB-staging`)
- R2 bucket `{purpose}-{env}` (e.g. `assets-prod`)
- KV namespace per env
- DO bindings per env

### Worker secrets inventory

| Secret name | Per env | Used by Worker(s) | Purpose |
|---|---|---|---|
| `GOOGLE_OAUTH_CLIENT_SECRET` | dev/staging/prod | integrations | Google OAuth |
| `EMAILIT_API_KEY` | dev/staging/prod | integrations | Emailit send |
| `PEXELS_API_KEY` | dev/staging/prod | integrations | Pexels search |
| `UNSPLASH_API_KEY` | dev/staging/prod | integrations | Unsplash search |
| `RECRAFT_API_KEY` | dev/staging/prod | integrations | Recraft generation |
| `PHOSPHOR_API_KEY` (if needed) | dev/staging/prod | integrations | Phosphor icons |
| (continue per Stage 2 integration specs) | | | |

Set via `wrangler secret put`; never in repo.

---

## 9. Dependency Map

| Source Worker | Target Worker | Binding Type | Reason | Type |
|---|---|---|---|---|
| {{worker A}} | platform | service binding | library access via Librarian | Hard |
| {{worker A}} | resilience | service binding | rate limit + retry | Soft |
| {{worker A}} | observability | service binding (fire-and-forget) | metrics + tracing | Soft |
| (etc — populated from Stage 2 component DEPENDENCIES sections) | | | | |

### Cycle Check

| Cycle | Resolution |
|---|---|
| (or "None found") | |

### Forbidden Dependencies Check

| Component | Forbidden | Violated? |
|---|---|---|
| `website/` | `renderers/` (per output-boundary rule) | ☐ |
| Any non-`library/` system | direct D1 binding | ☐ |
| Any non-`assets/` system | direct R2 write | ☐ |

---

## 10. Worker Topology

| Worker | Systems (1 or more) | Bindings | Routes | Cold-start budget |
|---|---|---|---|---|
| platform | services + system + state + library | D1 (all), R2 (some), KV, DO, Vectorize | (none direct; service-bound from others) | <100ms |
| access | access | (per access SPEC) | `/auth/*` | <100ms |
| ai | ai | (per ai SPEC) | (none direct) | <200ms |
| ... | | | | |

### Cross-Worker Call Graph

```
[platform] ←─ all 23 own-Worker systems (for library access via Librarian)
[resilience] ←─ many systems (for retry/rate-limit patterns)
[observability] ←─ all systems (fire-and-forget metrics)
[website] → [renderers] ❌ FORBIDDEN — JSON-emitter must not depend on renderers
[email] → [integrations/EmailitIntegration] ✅ (rendered HTML to send)
[assets] → R2 binding (no other Worker writes R2)
```

### Cold-Start Ordering

`platform` Worker boots first (foundation). All other Workers depend on it via service binding.

---

## 11. Implementation Plan

### Dependency Tiers (canonical — drives both Stage 2 spec order AND Stage 4 build order)

This tier list is the single source of truth for both:
- **Stage 2 component spec order** — referenced by `methodology/STAGE-2-PROMPT.md`
- **Stage 4 Worker build/deploy order** — referenced by `methodology/STAGE-4-PROMPT.md`

Within a tier, alphabetical. Tier 1 must complete before Tier 2 begins; same for each subsequent tier.

#### Tier 1 — Foundation (no internal dependencies)

| System | Rationale |
|---|---|
| `system` | Pure utilities (Logger, FailureHandler, Parsers, Builders, GarbageCollector); everyone uses these |
| `services` | Foundation services (APIKeyManager, AuthHandler, DataSanitizer, PromptBuilder, etc.); used by every Tier 2+ system that calls external APIs or builds AI prompts |
| `state` | StateManager + ContextAssembler; used by orchestration and library write-flows |
| `library` | Librarian + FileManager + Writer + Archivist + CacheHandler; every data-owning system inherits its access contract |

These 4 systems share the `platform` Worker (Stage 4 deployment grouping). At Stage 2, each gets its own SYSTEM spec.

#### Tier 2 — Cross-cutting infrastructure (depend on Tier 1; every higher-tier system depends on at least one)

| System | Rationale |
|---|---|
| `access` | BrandPermissionResolver, CapabilityManager, RoleManager — every data-owning system checks permissions through this |
| `audit` | DecisionTimeline, HumanOverrideLedger — every AI-decision and human-override path logs through this |
| `observability` | MetricsCollector, TraceLogger — every Worker reports through this |
| `resilience` | RateLimiter, RetryPolicyEngine, FallbackStrategyResolver — every external call goes through this |
| `versioning` | ChangeLog, VersionManager — every content-state-change uses this |

#### Tier 3 — Data-owning systems (own D1 libraries; depend on Tier 1+2)

| System | Rationale |
|---|---|
| `ai` | Owns prompts + model-configs libraries; provides AI capability to content + others |
| `assets` | Owns asset catalog library; R2 binary store; provides asset access to content + others |
| `brand` | Owns brands + audiences + voices + tones libraries; provides brand context to everything content-related |
| `content` | Owns themes + outlines libraries; depends on brand + ai + template |
| `social` | Owns social-templates library; depends on brand + content + template |
| `template` | Owns templates + section-specs + content-types + cognitive-types libraries; provides template structure to content + others |

#### Tier 4 — Production / output systems (depend on Tier 1+2+3)

| System | Rationale |
|---|---|
| `checks` | Content-level validation (Grammar, Language, Layout, Values); used by verification + review |
| `document-templates` | Document structural templates; consumed by documents/ |
| `documents` | Composes documents using templates, content, brand, assets, marks |
| `email` | Composes rendered email HTML; hands off to `integrations/EmailitIntegration` for send |
| `marks` | Generates logos/icons/marks (logo is brand-derived) |
| `orchestration` | Workflow coordination across systems; depends on most of Tier 1–3 |
| `publishing` | Distribution + publishing controller; final step before integrations |
| `renderers` | PDF/DOCX/Markdown/HTML rendering; consumes JSON from website + documents + email |
| `review` | Approval queue and human override path |
| `verification` | Content-level validation (Accessibility, Integrity, SemanticCoherence, Signposting, Scope) |
| `website` | Generates website page JSON (Astro consumes); must NOT depend on `renderers/` per output-boundary rule |

#### Tier 5 — External integrations (depend on everything; specced last)

| System | Rationale |
|---|---|
| `integrations` | 15 provider wrappers (Canva, Google, YouTube, Recraft, Pexels, Unsplash, Phosphor, Emailit, WebhookReceiver, WebScraper, plus the 4 Google renderer services and CanvaRenderer). All inbound binary → `assets/`/R2; inbound metadata → relevant library via `library/`; outbound rendered artefact via `resilience/` patterns. |

#### Note on `lib/` utilities

`lib/svg/` is utility code, **not a Pass 0 / Stage 2 spec target**. It's spec'd briefly as part of Stage 2's library-template work but doesn't follow the per-system spec pattern.

---

### Stage 4 Worker Build Order (derived from tier list)

Stage 4 deploys Workers in dependency-tier order. Within a tier, parallel-safe.

| Order | Worker | Contains Systems (Stage 2) | Depends On |
|---|---|---|---|
| 1 | platform | system, services, state, library (Tier 1) | (none — foundation) |
| 2 | resilience, observability, audit, versioning, access (each its own Worker) | Tier 2 | platform |
| 3 | ai, assets, brand, content, social, template (each its own Worker) | Tier 3 | platform + Tier 2 Workers as needed |
| 4 | checks, document-templates, documents, email, marks, orchestration, publishing, renderers, review, verification, website (each its own Worker) | Tier 4 | platform + Tier 2 + Tier 3 as needed |
| 5 | integrations | Tier 5 | All prior tiers |

#### Foundation Milestone

`platform` Worker deployed + library D1 migrations applied + smoke tests pass = **M0 (foundation complete)**. All other Worker deploys gated on M0.

#### Subsequent Milestones

| Milestone | Workers complete | What this enables |
|---|---|---|
| M0 | platform | Library D1 access via `library/Librarian` |
| M1 | + Tier 2 (resilience, observability, audit, versioning, access) | Cross-cutting safety net |
| M2 | + Tier 3 (ai, assets, brand, content, social, template) | Data-owning systems live; content can be produced |
| M3 | + Tier 4 (checks, document-templates, documents, email, marks, orchestration, publishing, renderers, review, verification, website) | Full output pipeline live |
| M4 | + Tier 5 (integrations) | External producers/consumers connected |
| **M5 — Ready for testing** | All 27 systems | End-to-end ready |

---

### LOC Estimate Summary

| System | Estimated LOC | Size Band |
|---|---|---|
| {{system}} | ~{{n}} | Low / Medium / High |
| ... | | |
| **Total runtime** | ~{{n}} | |

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Spec author | Claude | {{date}} |
| Technical reviewer | Human | |
| Project owner | Human | |

---

## Verdict

**Status:** GO FOR IMPLEMENTATION / NO-GO

If GO → proceed to Stage 4 (Implementation).
If NO-GO → log blockers in `NCE-V2/admin/PASS-DECISION-NOTES.md`; address; re-run Stage 3.

---
