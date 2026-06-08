# Pre-Spec Notes Checklist

> **Purpose:** Ensure placeholder specs capture all essential concerns before detailed spec writing.
> **Applies to:** All module placeholders before moving to full spec phase.

---

## Overview

Pre-spec notes are lightweight documents that establish scope and dependencies. They prevent spec-writing rework by clarifying concerns upfront. Once approved, the pre-spec notes become the foundation for detailed specs.

---

## Pre-Spec Notes Template

Use this checklist to ensure your placeholder module has addressed all necessary concerns:

### 1. Core Identity
- [ ] **Module name** - Clear, matches role in ROLE-MATRIX
- [ ] **Purpose statement** - One sentence: "Generate [WHAT] using [HOW]"
- [ ] **Type** - Matches category in MODULE-SPEC-CHECKLIST (e.g., AI Services, Visual Design)
- [ ] **Status** - Set to "Placeholder" initially

### 2. Scope & Boundaries

#### What It DOES
- [ ] **Primary responsibilities** - 3-5 bullet points of core functions
- [ ] **Key operations** - Named functions or operations (generate, transform, validate, etc.)
- [ ] **Data it creates/modifies** - What outputs does it produce?

#### What It DOES NOT Do
- [ ] **Explicit non-responsibilities** - List 3-5 things adjacent modules handle (prevents scope creep)
- [ ] **Boundary markers** - "X is handled by Y module" for each non-responsibility

### 3. Data Flow

- [ ] **Input sources** - Where data comes from (other modules, config, repos, DBs)
- [ ] **Processing steps** - Numbered or diagrammed (even rough ASCII diagrams help)
- [ ] **Output destinations** - Where results go (other modules, storage, providers)
- [ ] **Data shape** - What format data takes at each step (JSON, objects, streams, etc.)

### 4. Dependencies & Relationships

#### Depends On (Modules This Calls)
- [ ] **APIKeyManager** - if using external APIs
- [ ] **RateLimiter** - if making rate-limited calls
- [ ] **FailureHandler** - if operations can fail
- [ ] **Logger** - if logging is needed
- [ ] **Validator** - if input validation needed
- [ ] **DatabaseHandler** - if accessing databases
- [ ] **FileManager** - if file operations needed
- [ ] **Other specific modules** - List any module-to-module dependencies

#### Feeds Into (Modules That Call This)
- [ ] **Direct consumers** - Which modules call this one?
- [ ] **Secondary consumers** - Which modules use output indirectly?
- [ ] **Data pipeline** - Show the flow: Module A → This Module → Module B

#### Data Dependencies
- [ ] **Repos** (git-based) - `/repos/brands/`, `/repos/prompts/`, etc.
- [ ] **Databases** - Which .db files does it access? (own or shared)
- [ ] **Config files** - Any configuration data sources?
- [ ] **External APIs** - Which providers (OpenAI, Claude, etc.) does it call?

### 5. Concern Ownership (Cross-Check Against Ownership Table)

For each concern that touches your module, document who owns it:

| Concern | Owner | How This Module Uses It | Notes |
|---------|-------|------------------------|-------|
| **Validation** | Validator | Input/output contracts | Reference Validator spec |
| **Retry logic** | FailureHandler | Emit error codes, let FailureHandler decide | Reference error codes |
| **Logging** | Logger | Call Logger API, don't implement logging | Reference Logger spec |
| **Error codes** | ERROR-CODES.md | List codes this module emits | Add new codes if needed |
| **Prompt templates** | PromptBuilder | Receive prompts, don't create them | If applicable |
| **Cost tracking** | CostTracker | Emit cost events, don't track costs | If applicable |
| **Rate limiting** | RateLimiter | Respect limits, don't enforce them | If applicable |

### 6. Error Handling

- [ ] **Error codes to emit** - List all error codes (prefix: MODULE_ERROR_TYPE)
- [ ] **Examples:**
  - `API_RATE_LIMIT_EXCEEDED` - when provider throttles
  - `VALIDATION_INPUT_INVALID` - when input fails schema check
  - `DEPENDENCY_NOT_FOUND` - when required resource missing
- [ ] **Transient vs permanent** - Which errors can be retried? Which are permanent?
- [ ] **Fallback strategy** - What happens on failure? (retry, fallback provider, skip, fail loudly?)

### 7. Concern Ownership Clarifications

For modules with complex interactions, document ambiguities:

**Example questions to answer:**
- [ ] **Prompt ownership** - Does this module build prompts or receive them?
- [ ] **Storage ownership** - Does this module write to DB/files, or return data for others to store?
- [ ] **Styling/transformation** - Does this module apply brand styles, or does VisualBrand handle it?
- [ ] **Validation** - Does this module validate input, or does Validator do it?
- [ ] **Cost calculation** - Does this module track costs, or does CostTracker do it?
- [ ] **Async/sync** - Are operations blocking or non-blocking? Who manages job queues?
- [ ] **Caching** - Does this module cache results, or does CacheHandler?
- [ ] **Metadata** - What metadata is stored alongside outputs? Who stores it?

### 8. Architecture Considerations

- [ ] **Performance patterns** - Async, parallel, batching, streaming, etc.
- [ ] **Concurrency limits** - Per-provider? Per-user? Global?
- [ ] **Storage strategy** - Local, DB, external (S3, Cloudflare)?
- [ ] **Fallback chains** - If primary fails, what's the fallback?
- [ ] **Long-running operations** - How are timeouts handled?
- [ ] **State management** - Stateless, or does it maintain session state?

### 9. Data Schemas (Draft)

- [ ] **Input schemas** - Expected structure of input (not validation rules—just structure)
- [ ] **Output schemas** - Expected structure of output
- [ ] **Options type** - If functions take options/config, document the shape
- [ ] **Database tables** (if applicable) - Which tables does this module read/write?
- [ ] **Config structure** - What configuration does this module need?

**Note:** Don't write Zod validation here—just document the *shape* of data. Zod schemas go in `/src/schemas/` after spec approval.

### 10. Dependencies on External Systems

- [ ] **External APIs** - OpenAI, Claude, Google, etc.—which ones and for what?
- [ ] **API rate limits** - Per-provider rate limits documented?
- [ ] **Cost implications** - Expensive operations documented? (triggers cost tracking)
- [ ] **Auth/credentials** - How are API keys managed? (via APIKeyManager)
- [ ] **Latency** - Expected response times? (affects queue/async decisions)

### 11. Relationships to Adjacent Modules

Document how this module relates to similar/adjacent modules (prevents duplication):

| Adjacent Module | Relationship | Notes |
|-----------------|-------------|-------|
| ModuleA | Calls it / Called by it / Parallel concern | How they interact |
| ModuleB | Owns related concern | Who does what |

**Examples:**
- AIImageCreation relates to ImageBrowser: "AIImageCreation *generates* images; ImageBrowser *searches* existing images"
- TemplateEngine relates to TemplateFactory: "TemplateEngine *reads* templates; TemplateFactory *writes* them"

### 12. Technology-Agnostic Boundaries

- [ ] **Library names NOT here** - Don't mention "use axios" or "use better-sqlite3" (goes in TECH-STACK.md)
- [ ] **Implementation details NOT here** - Don't specify retry counts, logging levels, config file paths
- [ ] **What DOES belong** - Module purpose, dependencies, data flow, error codes, boundaries

---

## Before Approval Checklist

Once you've drafted pre-spec notes, verify:

- [ ] **All sections completed** - No blanks or TBD items
- [ ] **Concern ownership clear** - For each concern, I can name the owning module
- [ ] **Boundaries explicit** - Can list 3+ things this module does NOT do
- [ ] **Dependencies mapped** - Can draw the dependency graph
- [ ] **Error codes listed** - All error codes start with MODULE_ prefix
- [ ] **No implementation details** - No tech stack, no retry timings, no logging statements
- [ ] **Data flow is clear** - Someone could implement from this without asking "what goes where?"
- [ ] **Contradictions resolved** - No conflicts with spec writing guidelines (see MODULE-SPEC-CHECKLIST.md)

---

## Example: AIImageCreation (Complete Pre-Spec Notes)

Reference the uploaded AIImageCreation.md for a working example of well-structured pre-spec notes.

---

## After Approval → Full Spec Writing

Once pre-spec notes are approved, the detailed spec includes:

- Function signatures with full param/return types
- Process flows (numbered steps)
- Database schemas (SQLite DDL)
- Error recovery flows
- Design rationale
- Interaction diagrams

**Key difference:** Pre-spec notes are *questions answered*. Full specs are *implementation contracts*.

---

*Created: 2026-01-20*
*Status: Active*
