# `platform` Worker — Multi-System Worker Spec

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Methodology Stage: 3 of 5
Worker Type: Multi-system (groups 4 systems)
---

## 1. Why grouped

The `platform` Worker bundles four foundation systems into one Worker:

| System | Role |
|---|---|
| `services` | Foundational services (API key management, auth, database handler, sanitisation, ingestion, prompt building, research tools, vector store) |
| `system` | System-level utilities (failure handler, logger, parsers, builders, garbage collector) |
| `state` | Context + state management |
| `library` | Data-access layer (Librarian, FileManager, Writer, Archivist, CacheHandler) |

These four share a Worker because:

- They form the foundation tier — every other system depends on them
- In-Worker imports are cheaper than cross-Worker service bindings (sub-microsecond vs sub-millisecond)
- They share many bindings (D1, R2, KV) so consolidating bindings is simpler
- They're tightly coupled in practice: `library/Librarian` calls `services/DatabaseHandler`; `system/Logger` is used by everyone

All other 23 NCE-V2 systems get their own Worker (per Stage 2 system specs).

---

## 2. Module structure

```
src/platform/
├── index.ts                          # Worker entry point (fetch handler)
├── router.ts                         # Routes requests to system handlers
│
├── services/
│   ├── APIKeyManager.ts
│   ├── AuthHandler.ts
│   ├── ContentMetadataManager.ts
│   ├── DatabaseHandler.ts
│   ├── DataSanitizer.ts
│   ├── GeneratedContentCatalog.ts
│   ├── IngestionEngine.ts
│   ├── PromptBuilder.ts
│   ├── PromptCondenser.ts
│   ├── PythonRunner.ts               # [FLAG] OQ-PY-1 — disposition unresolved
│   ├── ResearchTools.ts
│   └── VectorStore.ts
│
├── system/
│   ├── FailureHandler.ts
│   ├── Logger.ts
│   ├── Parsers.ts
│   ├── Builders.ts
│   └── GarbageCollector.ts
│
├── state/
│   ├── ContextAssembler.ts
│   ├── StateManager.ts
│   └── StateTransitionValidator.ts
│
└── library/
    ├── Librarian.ts
    ├── FileManager.ts
    ├── Writer.ts
    ├── Archivist.ts
    └── CacheHandler.ts
```

In-system imports use direct ESM imports (no service binding overhead):

```typescript
import { Librarian } from "./library/Librarian";
import { Logger } from "./system/Logger";
```

External Workers reach the `platform` Worker via service binding (declared in their `wrangler.toml`):

```toml
[[services]]
binding = "PLATFORM"
service = "platform"
```

---

## 3. wrangler.toml

`wrangler/platform.toml`:

```toml
name = "platform"
main = "src/platform/index.ts"
compatibility_date = "2026-01-01"

# D1 bindings — ALL library D1 databases live here
[[d1_databases]]
binding = "BRANDS_DB"
database_name = "brands-prod"
database_id = "{{from wrangler d1 create}}"

[[d1_databases]]
binding = "AUDIENCES_DB"
database_name = "audiences-prod"
database_id = "{{...}}"

# (repeat for every library — populated from Stage 2 library specs)

# R2 bindings — shared object stores accessed by services/
[[r2_buckets]]
binding = "ASSETS_BUCKET"
bucket_name = "assets-prod"
# (assets/ Worker writes to this bucket; platform reads via library refs)

# KV bindings
[[kv_namespaces]]
binding = "CACHE_KV"
id = "{{...}}"

# Vectorize binding
[[vectorize]]
binding = "EMBEDDINGS"
index_name = "embeddings-prod"

# Per-env overrides
[env.staging]
[env.dev]
```

Each environment has its own `wrangler.toml` overrides for binding `database_id`, `id`, etc.

---

## 4. Worker secrets

Foundational secrets (set via `wrangler secret put --env <env>`):

| Secret | Purpose |
|---|---|
| `INTERNAL_API_KEY` | Inter-Worker auth between own-Workers and platform |
| `MASTER_ENCRYPTION_KEY` | For services/DataSanitizer encryption needs |
| (others per services/ and library/ specs) | |

---

## 5. Routes

The `platform` Worker exposes:

| Path | Method | Purpose | Caller |
|---|---|---|---|
| (typically none direct) | — | platform is service-bound only | other Workers |

`platform` typically does NOT expose public HTTP routes. Other Workers expose public routes and service-bind to platform for library access, etc.

---

## 6. Cold-start considerations

- `platform` boots first; all other Workers depend on it via service binding
- D1 connections are lazy; first query may add ~50ms
- Module load: keep `src/platform/index.ts` minimal — lazy-load subsystems

```typescript
// src/platform/index.ts
export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    const url = new URL(request.url);
    const [_, system, ...path] = url.pathname.split("/");
    
    // Dynamic import per system for cold-start optimization
    switch (system) {
      case "library":
        const { handleLibraryRequest } = await import("./library/index");
        return handleLibraryRequest(request, env, ctx);
      // ... per system
      default:
        return new Response("Not Found", { status: 404 });
    }
  },
};
```

(This is one pattern; alternative is eager imports if cold-start budget allows.)

---

## 7. Cross-Worker contract

How other Workers call platform:

```typescript
// In some other Worker, e.g. brand Worker
declare global {
  interface Env {
    PLATFORM: Fetcher;  // service binding from wrangler.toml
  }
}

async function readBrand(env: Env, id: string) {
  const response = await env.PLATFORM.fetch("https://platform/library/brands/" + id, {
    headers: { "X-Internal-Auth": env.INTERNAL_API_KEY },
  });
  return await response.json();
}
```

All library access from external Workers goes through `platform/library/Librarian` (HTTP-style service binding).

In-Worker access (within platform itself) uses direct TS imports — no service binding overhead.

---

## 8. CPU + memory budget

| Limit | Per-request budget | Notes |
|---|---|---|
| CPU | <100ms p99 | Foundation tier — must be fast |
| Memory | <30 MB p99 | Well under 128 MB |
| Cold-start | <100ms | First module load |

If `platform` Worker exceeds these → consider splitting (e.g. extracting `library/` into its own Worker). Track in `NCE-V2/admin/PASS-DECISION-NOTES.md`.

---

## 9. Coordination patterns within platform

The 4 systems coordinate without service bindings (since they share runtime):

```typescript
// library/Librarian uses services/DatabaseHandler directly
import { DatabaseHandler } from "../services/DatabaseHandler";

export class Librarian {
  constructor(private db: DatabaseHandler) {}
  
  async find(query: Query): Promise<Result<Entry[]>> {
    // Direct call, no service binding
    return this.db.query(query);
  }
}
```

Order of initialization (for stateful subsystems):
1. `system/Logger` (no deps)
2. `system/FailureHandler` (depends on Logger)
3. `services/DatabaseHandler` (depends on D1 bindings)
4. `library/Librarian`, `Writer`, etc. (depend on DatabaseHandler)
5. `state/StateManager` (depends on library/ for persistence)

---

## 10. Hardening Self-Check (mandatory before APPROVED status)

| Check | Status |
|---|---|
| All 4 systems' subsystems listed in module structure | ☐ |
| All D1 bindings consolidated here (no D1 binding in other Workers) | ☐ |
| Cold-start budget feasible (subsystems lazy-loaded if needed) | ☐ |
| Cross-Worker contract documented for external callers | ☐ |
| No business logic in `platform` Worker entry point — only routing | ☐ |
| `library/Librarian` accessible by every other Worker | ☐ |
| Worker secrets inventoried | ☐ |
| CPU+memory budget within Worker limits | ☐ |
| Coordination order documented | ☐ |

If any ☐ unchecked → revise; do not mark Status: APPROVED.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Spec author | Claude | {{date}} |
| Technical reviewer | Human | |

---
