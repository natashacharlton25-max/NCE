# Project Glossary

---
Project: {{project_name}}
Version: {{version}}
Last Updated: {{timestamp}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

Defines domain terms, technical terms, and acronyms used throughout the project. Use these exact terms in specs and code for consistency.

---

## How to Use This Glossary

1. **When writing specs:** Use terms exactly as defined here
2. **When reading specs:** Reference this glossary for unfamiliar terms
3. **When adding new terms:** Follow the template at the bottom

---

## Domain Terms

### A

#### Active
**Definition:** An entity that is currently in use and fully operational.
**Context:** User accounts, projects, subscriptions
**Opposite:** Archived, Inactive
**See also:** Status

#### Archive
**Definition:** To soft-delete an entity, removing it from active use but preserving data.
**Context:** Users can archive projects they no longer need.
**Verb form:** Archive (v), Archived (adj)
**See also:** Soft Delete

---

### B

#### Batch
**Definition:** A group of items processed together in a single operation.
**Context:** Batch create, batch update operations.
**Related:** Bulk operation

---

### C

#### CRUD
**Definition:** Create, Read, Update, Delete — the four basic operations on data.
**Context:** Standard operations every entity supports.

#### Cursor
**Definition:** An opaque token used for pagination, pointing to a position in a result set.
**Context:** `nextCursor` in list responses.
**See also:** Pagination

---

### D

#### Dependency
**Definition:** A component that another component relies on to function.
**Context:** "User system has a dependency on auth system."
**Types:** Hard dependency (required), Soft dependency (optional)

#### Draft
**Definition:** An entity state indicating incomplete or not yet published.
**Context:** Projects start in draft status.
**Opposite:** Active, Published

---

### E

#### Entity
**Definition:** A domain object with a unique identity that persists over time.
**Context:** User, Project, Task are entities.
**Characteristics:** Has ID, has lifecycle, can be referenced

#### Entity ID
**Definition:** A unique identifier for an entity, formatted as `{prefix}_{identifier}`.
**Example:** `usr_abc123`, `prj_def456`
**See also:** ID Prefix

---

### F

#### Fan-in
**Definition:** The number of components that depend on a given component.
**Context:** High fan-in means many things break if this component breaks.
**Opposite:** Fan-out

#### Fan-out
**Definition:** The number of components a given component depends on.
**Context:** High fan-out means this component is fragile (many failure points).
**Opposite:** Fan-in

---

### H

#### Hard Delete
**Definition:** Permanently removing data from storage with no recovery option.
**Context:** Used for compliance or when soft delete isn't appropriate.
**Opposite:** Soft Delete
**See also:** Archive

#### Health Check
**Definition:** An operation that verifies a component is functioning correctly.
**Context:** `checkHealth()` returns component status.

---

### I

#### Idempotent
**Definition:** An operation that produces the same result whether executed once or multiple times.
**Context:** PUT requests should be idempotent.
**Example:** Setting status to "active" is idempotent.

#### Integration
**Definition:** A component that connects to an external third-party service.
**Context:** Stripe integration, email integration.
**Naming:** `integration-{provider}-{service}`

---

### L

#### Library
**Definition:** Shared utility code with no business logic or state.
**Context:** lib-validation, lib-datetime
**Characteristics:** Pure functions, no dependencies on systems
**Naming:** `lib-{name}`

---

### O

#### Offset
**Definition:** The number of items to skip in pagination.
**Context:** `?offset=20` skips the first 20 results.
**See also:** Pagination, Cursor

---

### P

#### Pagination
**Definition:** Dividing large result sets into smaller pages.
**Context:** List operations return paginated results.
**Methods:** Offset-based, Cursor-based
**See also:** Cursor, Offset

#### Pass
**Definition:** A phase in the development methodology where specific work is completed.
**Context:** Pass 0: Coverage, Pass 1: Contracts, etc.

#### Prefix
**Definition:** A short identifier at the start of an error code or entity ID.
**Context:** `USR` for user system, `usr` for user IDs.

---

### R

#### Result
**Definition:** A wrapper type containing either success data or error details.
**Context:** All functions return `Result<T>`.
**Shape:** `{ success: true, data: T }` or `{ success: false, error: ErrorDetail }`

#### Retry
**Definition:** Attempting an operation again after failure.
**Context:** Retryable errors can be retried with backoff.
**See also:** Backoff, Transient Error

---

### S

#### Soft Delete
**Definition:** Marking an entity as deleted without removing data, typically by setting `deletedAt`.
**Context:** Soft-deleted users can be restored.
**Opposite:** Hard Delete
**See also:** Archive

#### Status
**Definition:** The current state of an entity in its lifecycle.
**Context:** User status: draft, active, archived.
**See also:** State Machine

#### Subsystem
**Definition:** A component within a system that handles a specific responsibility.
**Context:** Auth subsystem within user system.
**Naming:** Folder under parent system

#### System
**Definition:** A top-level component that owns a major responsibility area.
**Context:** User system, project system.
**Characteristics:** Can have subsystems, ~1500 LOC limit

---

### T

#### Timestamp
**Definition:** A point in time, stored as ISO 8601 string in UTC.
**Format:** `YYYY-MM-DDTHH:mm:ss.sssZ`
**Example:** `2024-01-15T10:30:00.000Z`

#### Transient Error
**Definition:** A temporary error that may succeed if retried.
**Context:** Network timeouts, rate limits.
**Opposite:** Permanent Error
**See also:** Retry

---

### V

#### Validation
**Definition:** Checking that input data meets requirements before processing.
**Context:** Validate email format, required fields.
**Types:** Format validation, business validation

---

## Technical Terms

### API Terms

| Term | Definition |
|------|------------|
| Endpoint | A specific URL path that accepts requests |
| Payload | The data sent in a request or response body |
| Query parameter | Parameters passed in the URL after `?` |
| Request body | JSON data sent with POST/PUT/PATCH requests |
| Response | Data returned from an API call |

### Database Terms

| Term | Definition |
|------|------------|
| Migration | A versioned change to database schema |
| Index | A database structure for faster queries |
| Transaction | A group of operations that succeed or fail together |
| Query | A request to read data from the database |

### Error Handling Terms

| Term | Definition |
|------|------------|
| Error code | Unique identifier for an error type (e.g., `USR_001`) |
| Error message | Human-readable description of the error |
| Stack trace | The call stack at the time of error |
| Trace ID | Unique identifier for tracking a request |

---

## Acronyms

| Acronym | Expansion | Definition |
|---------|-----------|------------|
| API | Application Programming Interface | Interface for software communication |
| CRUD | Create, Read, Update, Delete | Basic data operations |
| DTO | Data Transfer Object | Object for transferring data between layers |
| E2E | End-to-End | Testing the complete flow |
| ID | Identifier | Unique reference for an entity |
| I/O | Input/Output | Data transfer operations |
| JSON | JavaScript Object Notation | Data format |
| LOC | Lines of Code | Measure of code size |
| PR | Pull Request | Request to merge code changes |
| REST | Representational State Transfer | API architectural style |
| SDK | Software Development Kit | Tools for building with an API |
| TTL | Time to Live | Duration before expiration |
| URL | Uniform Resource Locator | Web address |
| UTC | Coordinated Universal Time | Time standard |
| UUID | Universally Unique Identifier | 128-bit identifier |

---

## Synonyms and Preferred Terms

| Avoid | Use Instead | Reason |
|-------|-------------|--------|
| Record | Entity | Consistency |
| Item | Entity | Consistency |
| Object | Entity (for domain) or Object (for JS) | Clarity |
| Remove | Delete | Consistency |
| Fetch | Get | Consistency |
| Error code | Error code (not "error type") | Consistency |
| Deactivate | Archive | Project convention |

---

## Adding New Terms

### Template

```markdown
#### Term Name
**Definition:** Clear, concise definition.
**Context:** When/where this term is used.
**Example:** Concrete example if helpful.
**See also:** Related terms
```

### Checklist for New Terms

- [ ] Term is used in multiple places
- [ ] Definition is precise and unambiguous
- [ ] Context explains when it applies
- [ ] Added to correct alphabetical position
- [ ] Cross-references added

---

## Notes

- Use glossary terms consistently in all specs
- When in doubt, check this glossary
- Propose new terms via PR

---
Phase: 26 (Project-Wide Specs)
Generated: {{timestamp}}
---
