# Technology Stack

> **Purpose:** Central reference for technology choices across all modules
> **For:** Implementation, consistency, onboarding
> **Version:** 1.0.0

---

## Runtime Environment

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **Language** | TypeScript 5.x | Type safety, IDE support, catches errors early |
| **Runtime** | Node.js 20+ | LTS, native fetch, ES modules |
| **Module System** | ES Modules | Modern standard, tree-shaking |
| **Build** | TBD | To be decided during implementation |

---

## Database

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **Engine** | SQLite | Local-first, no server needed, portable |
| **Library** | better-sqlite3 | Synchronous (simpler), fast, well-maintained |
| **FTS** | SQLite FTS5 | Built-in, no external search service |
| **Migrations** | Custom scripts | Simple forward-only migrations in `/migrations/` |

### Database Files

| Database | Module Owner | Contents |
|----------|--------------|----------|
| `themes.db` | ThemeGenerator | Themes, truths, transformations |
| `outlines.db` | OutlineGenerator | Section outlines, truth strategies |
| `templates.db` | TemplateEngine | Template definitions |
| `jobs.db` | Orchestrator | Job queue, history |
| `cache.db` | CacheHandler | Cached AI responses |

---

## AI Integration

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **Primary Provider** | Anthropic (Claude) | Quality, consistency |
| **SDK** | @anthropic-ai/sdk | Official SDK |
| **Fallback Provider** | OpenAI | Backup if Anthropic unavailable |
| **Model Selection** | Per-task | Haiku for simple, Sonnet for complex |

### Model Usage Guidelines

| Task Type | Recommended Model | Notes |
|-----------|-------------------|-------|
| Theme generation | claude-3-sonnet | Needs nuance for truths/angles |
| Outline planning | claude-3-sonnet | Needs coherence across sections |
| Section content | claude-3-haiku | Faster, cheaper, guided by outline |
| Validation/parsing | claude-3-haiku | Simple extraction tasks |

---

## File System

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **File Operations** | Node.js fs/promises | Native, no dependencies |
| **Path Handling** | Node.js path | Cross-platform |
| **JSON** | Native JSON.parse/stringify | Standard |

### Directory Structure

```
/data/              ← SQLite databases
/repos/             ← JSON data files (brands, audiences, etc.)
/migrations/        ← Database migration scripts
/output/            ← Generated content
/temp/              ← Temporary files (auto-cleanup)
/logs/              ← Log files
```

---

## Validation

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **Runtime Validation** | Zod | TypeScript-first, excellent inference |
| **JSON Schemas** | Zod-to-JSON-Schema | Generate from Zod for documentation |

### Schema Location

```
/schemas/           ← Generated JSON Schema files (for docs/external tools)
/src/schemas/       ← Zod schemas (source of truth)
```

---

## External Integrations

| Service | Library | Purpose |
|---------|---------|---------|
| Canva | @canva/design-api | Document rendering |
| Google Slides | googleapis | Presentation rendering |
| Google Docs | googleapis | Document rendering |
| Unsplash | unsplash-js | Stock images |
| YouTube | googleapis | Video integration |

---

## Utilities

| Purpose | Choice | Notes |
|---------|--------|-------|
| Hashing | Node.js crypto | Native, for structureHash etc. |
| UUID | crypto.randomUUID() | Native in Node 20+ |
| Date/Time | Native Date + ISO strings | No moment/dayjs needed |
| HTTP | Native fetch | Built into Node 20+ |

---

## Development Tools

| Purpose | Tool | Notes |
|---------|------|-------|
| **Testing** | TBD | To be decided (Vitest likely) |
| **Linting** | ESLint | TypeScript rules |
| **Formatting** | Prettier | Consistent code style |
| **Type Checking** | tsc --noEmit | Part of CI |

---

## Logging

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **Library** | TBD | To be defined in Logger spec |
| **Format** | Structured JSON | Parseable, filterable |
| **Levels** | debug, info, warn, error | Standard levels |
| **Output** | File + Console | Configurable per environment |

> **Note:** Logging patterns to be fully specified in Logger module spec.

---

## Error Handling

| Component | Reference |
|-----------|-----------|
| **Error Codes** | See [ERROR-CODES.md](ERROR-CODES.md) |
| **Recovery Strategies** | See FailureHandler spec (when written) |

---

## Concurrency Model

**Current:** Single-user system

| Scenario | Behavior |
|----------|----------|
| Manual import during auto-generate | Auto-generate has priority; manual waits |
| Multiple auto-generate jobs | Queued via Scheduler |
| Template update during content generation | Update waits until generation completes |

> **Future:** Multi-user concurrency layer to be added if needed.

---

## Constraints

| Constraint | Limit | Reason |
|------------|-------|--------|
| Max file size | 10MB default | Prevent runaway writes |
| Max AI response | Model-dependent | Token limits |
| Cache TTL | Varies by type | See module configs |
| Concurrent AI calls | 3 default | Rate limit protection |

---

## Not Using

| Technology | Why Not |
|------------|---------|
| PostgreSQL/MySQL | Overkill for single-user, adds server dependency |
| Redis | SQLite cache sufficient for now |
| Docker | Not needed for local-first single-user |
| GraphQL | REST-style internal APIs simpler |
| ORM (Prisma/TypeORM) | Raw SQL with better-sqlite3 is sufficient |

---

## Version Compatibility

| Component | Minimum Version |
|-----------|-----------------|
| Node.js | 20.0.0 |
| TypeScript | 5.0.0 |
| SQLite | 3.35.0 (for returning clause) |

---

*Created: 2026-01-19*
*Version: 1.0.0*
