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

### Build Order

| Order | Worker | Depends On | Parallel-safe with |
|---|---|---|---|
| 1 | platform | (none — foundation) | (none — must be first) |
| 2 | resilience | platform | observability |
| 2 | observability | platform | resilience |
| 3 | access | platform, resilience | (all of 3 in parallel) |
| 3 | ai | platform, resilience, observability | other order-3 Workers |
| ... | | | |

### Foundation Milestone

`platform` Worker deployed + library D1 migrations applied + smoke tests pass = M0 (foundation complete).

### Subsequent Milestones

| Milestone | Workers complete |
|---|---|
| M0 | platform |
| M1 | platform, resilience, observability, audit, versioning, state, system |
| M2 | + access, brand, ai, content, library-owning systems |
| M3 | + assets, document-templates, documents, marks, social, email, template |
| M4 | + integrations, renderers, publishing, review, verification, checks, orchestration, website |
| **M5 — Ready for testing** | all 27 systems |

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
