# Service Architecture Map

> **Purpose:** Overview of all services, their responsibilities, and how they connect.
> **Created:** 2026-01-16
> **Status:** Planning Document

---

## Service Layers

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           HIGH-LEVEL SERVICES                                │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                         LIBRARIAN CEO                                  │  │
│  │                    (Single Entry Point)                                │  │
│  │                                                                        │  │
│  │  • Routes requests to correct library worker                          │  │
│  │  • Checks permissions via rules                                       │  │
│  │  • Has registry of all library apps                                   │  │
│  │  • Manages caching                                                    │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                    │                                         │
│          ┌─────────────────────────┼─────────────────────────┐              │
│          │                         │                         │              │
│          ▼                         ▼                         ▼              │
│  ┌───────────────┐        ┌───────────────┐        ┌───────────────┐       │
│  │ BRAND LIBRARY │        │ TONE LIBRARY  │        │COLOUR LIBRARY │       │
│  │    WORKER     │        │    WORKER     │        │    WORKER     │       │
│  │               │        │               │        │               │       │
│  │ Fetches from  │        │ Fetches from  │        │ Fetches from  │       │
│  │ brand-library │        │ tone-library  │        │colour-library │       │
│  │ app           │        │ app           │        │ app           │       │
│  └───────────────┘        └───────────────┘        └───────────────┘       │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           PROCESSING LAYER                                   │
│                                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  ARCHIVIST  │  │   PARSERS   │  │  FETCHERS   │  │  BUILDERS   │        │
│  │             │  │             │  │             │  │             │        │
│  │ • Validate  │  │ • Transform │  │ • External  │  │ • Create    │        │
│  │ • Catalog   │  │ • Convert   │  │ • HTTP/API  │  │ • Structure │        │
│  │ • Route     │  │ • Validate  │  │ • Downloads │  │ • Assemble  │        │
│  │             │  │             │  │             │  │             │        │
│  │ Uses:       │  │ Uses:       │  │ Uses:       │  │ Uses:       │        │
│  │ Writer      │  │ (none)      │  │ (external)  │  │ Writer      │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           SYSTEM SERVICES                                    │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────┐      │
│  │                           FAILSYS                                  │      │
│  │                                                                    │      │
│  │  • Central failure handling for ALL services                       │      │
│  │  • Holds ALL retry rules and logic                                │      │
│  │  • Pattern detection across system                                 │      │
│  │  • Can halt system on critical failures                           │      │
│  │  • Routes retries back to correct service                         │      │
│  └───────────────────────────────────────────────────────────────────┘      │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────┐      │
│  │                           WRITER                                   │      │
│  │                     (System data writes)                           │      │
│  │                                                                    │      │
│  │  • Write system data to disk   • Atomic writes                    │      │
│  │  • Used by: Archivist, Librarian                                  │      │
│  │  • NOT: Builder (mkdir), Logger (own logs)                        │      │
│  └───────────────────────────────────────────────────────────────────┘      │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────┐      │
│  │                           LOGGER                                   │      │
│  │                                                                    │      │
│  │  • Central logging for ALL services                               │      │
│  │  • Temp file for fast writes (24h)                                │      │
│  │  • SQLite database for queryable history                          │      │
│  │  • FailSys uses Logger for pattern detection                      │      │
│  └───────────────────────────────────────────────────────────────────┘      │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘

Note: File READING uses Node's fs directly. No separate Reader service needed.
      Librarian handles library reads with caching/matching.
      Other services read files directly with fs/promises.
```

---

## Service Summary

| Service | Level | Responsibility | Depends On | Called By |
|---------|-------|----------------|------------|-----------|
| **Librarian** | High | Search/match/cache libraries | fs (internal) | Orchestrator, Generators |
| **Archivist** | Process | Validate, catalog, orchestrate storage | Builder, Writer | Any service outputting data |
| **Parsers** | Process | Transform/convert data | (none) | Services needing data conversion |
| **Fetchers** | Process | Get external data | external APIs | Services needing external data |
| **Builders** | Process | File paths registry, create folders | filesystem (mkdir) | Archivist, Setup, Generators |
| **FailSys** | System | Central failure handling | Logger | ALL services |
| **Writer** | System | System data writes | filesystem | Archivist, Librarian |
| **Logger** | System | Central logging, temp + SQLite | filesystem, SQLite | ALL services |

---

## Data Flow: Write Chain

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   SERVICE    │────▶│  ARCHIVIST   │────▶│   BUILDER    │────▶│   WRITER     │
│   OUTPUT     │     │              │     │              │     │              │
│              │     │ • Validates  │     │ • getPath()  │     │ • Writes to  │
│ (any service │     │ • Catalogs   │     │ • ensureDir()│     │   disk       │
│  producing   │     │              │     │              │     │              │
│  data)       │     │              │     │              │     │              │
└──────────────┘     └──────┬───────┘     └──────────────┘     └──────────────┘
                            │
                            │ If failure
                            ▼
                     ┌──────────────┐
                     │   FAILSYS    │
                     │              │
                     │ • Logs       │
                     │ • Applies    │
                     │   rules      │
                     │ • Routes     │
                     │   retry      │
                     └──────────────┘
```

**Separation of concerns:**
- **Archivist** = Validates WHAT, orchestrates flow
- **Builder** = Knows WHERE, ensures folders exist
- **Writer** = Does the actual disk write

---

## Data Flow: Read Chain

```
┌──────────────────────────────────────────────────────────────────────────┐
│                          LIBRARY READS                                    │
│                                                                           │
│  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐             │
│  │   SERVICE    │────▶│  LIBRARIAN   │────▶│   fs.read    │             │
│  │   REQUEST    │     │              │     │   (internal) │             │
│  │              │     │ • Checks     │     │              │             │
│  │ (needs       │     │   cache      │     │              │             │
│  │  library     │     │ • Searches   │     │              │             │
│  │  data)       │     │ • Matches    │     │              │             │
│  └──────────────┘     └──────────────┘     └──────────────┘             │
└──────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────┐
│                          OTHER READS                                      │
│                                                                           │
│  ┌──────────────┐     ┌──────────────┐                                   │
│  │   SERVICE    │────▶│  fs/promises │  Direct file system access        │
│  │              │     │              │  (no wrapper needed)              │
│  └──────────────┘     └──────────────┘                                   │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Individual Service Plans

| Service | Plan Document | Status |
|---------|---------------|--------|
| Librarian | [../Librarian/LIBRARIAN-ARCHITECTURE.md](../Librarian/LIBRARIAN-ARCHITECTURE.md) | Detailed |
| Writer | [WRITER-PLAN.md](./WRITER-PLAN.md) | Planning |
| Archivist | [ARCHIVIST-PLAN.md](./ARCHIVIST-PLAN.md) | Planning |
| FailSys | [FAILSYS-PLAN.md](./FAILSYS-PLAN.md) | Planning |
| Parsers | [PARSERS-PLAN.md](./PARSERS-PLAN.md) | Planning |
| Fetchers | [FETCHERS-PLAN.md](./FETCHERS-PLAN.md) | Planning |
| Builders | [BUILDERS-PLAN.md](./BUILDERS-PLAN.md) | Planning |
| Logger | [LOGGER-PLAN.md](./LOGGER-PLAN.md) | Planning |

---

## Naming Conventions

| Suffix | Meaning | Examples |
|--------|---------|----------|
| **Parser** | Transforms/validates data | ImageParser, CSSParser, DataParser |
| **Fetcher** | Gets data from external sources | ImageFetcher, APIFetcher, WebFetcher |
| **Builder** | Creates structures | FolderBuilder, PromptBuilder, ConfigBuilder |
| **Librarian** | Reads from libraries | ToneLibrarian, ValuesLibrarian |

---

## Build Order

Based on dependencies:

| Priority | Service | Reason |
|----------|---------|--------|
| 1 | Writer | Foundation - data persistence |
| 2 | Logger | System-wide - all services log through it |
| 3 | FailSys | System-wide - uses Logger for pattern detection |
| 4 | Archivist | Processing - validates before write |
| 5 | Parsers | Processing - data transformation |
| 6 | Fetchers | Processing - external data |
| 7 | Builders | Processing - structure creation |
| 8 | Librarian | High-level - already detailed |

---

*See individual plan documents for detailed specifications.*
