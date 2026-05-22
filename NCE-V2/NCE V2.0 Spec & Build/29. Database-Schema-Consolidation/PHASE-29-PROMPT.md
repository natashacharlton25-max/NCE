# PHASE 29: DATABASE SCHEMA CONSOLIDATION

---
Phase: 29
Name: Database Schema Consolidation
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are consolidating all database definitions from component STORAGE.md files into a unified master schema.

This is **extraction and consolidation** — NOT design. The specs already define what's needed.

---

## TASK

1. Determine database type (ask user if not specified)
2. Gather all STORAGE.md files from component specs
3. Extract all table/collection definitions
4. Consolidate into master schema using naming convention
5. Check for conflicts — if found, STOP and roll back
6. Define migration strategy
7. Get approval

---

## STEP 1: Determine Database Type

**DO NOT DEFAULT.** Force a conscious decision.

Ask the user:

> What database type will this project use?
> 
> Before answering, consider:
> - Data relationships (complex = SQL, flexible = Document)
> - Query patterns (joins = SQL, denormalized reads = Document)
> - Scale requirements (horizontal = Document/KV, vertical = SQL)
> - Consistency needs (strong = SQL, eventual = Document/KV)
> 
> Options:
> 1. Relational (SQL): PostgreSQL, MySQL, SQLite, Cloudflare D1
> 2. Document (NoSQL): MongoDB, Firestore, CouchDB
> 3. Key-Value: Redis, DynamoDB, Cloudflare KV
> 4. Graph: Neo4j, Amazon Neptune
> 
> Please specify your choice with justification.

**Do NOT proceed until database type is explicitly chosen and justified.**

Log the decision in PRECODE-DECISION-NOTES.md with:
- Choice made
- Justification
- Alternatives considered
- Trade-offs accepted

Adapt schema format based on answer:
- SQL → Tables, columns, types, relationships, indexes
- Document → Collections, document schemas, indexes
- Key-Value → Key patterns, value schemas, TTL
- Graph → Nodes, relationships, properties

---

## STEP 2: Gather STORAGE.md Files

List every component that has a STORAGE.md:

```
{{system-a}}/spec/STORAGE.md
{{system-a}}/{{subsystem-1}}/spec/STORAGE.md
{{system-b}}/spec/STORAGE.md
...
```

Also gather:
- `standards/SCHEMAS.md` from Phase 26 (shared types)

---

## STEP 3: Extract Definitions

From each STORAGE.md, extract:
- Table/collection name
- Column/field definitions
- Primary keys
- Foreign keys / references
- Indexes
- Constraints

---

## STEP 4: Consolidate with Naming Convention

**Naming Rule:** `{component}_{entity}`

Examples:
- `auth_users` (from Auth system)
- `auth_sessions` (from Auth system)
- `content_posts` (from Content system)
- `content_comments` (from Content/Comments subsystem)

**Exceptions:**
- If a different name is justified, log in PRECODE-DECISION-NOTES.md
- Document the reason clearly

**Consolidation:**
- Merge all definitions into one master schema
- Define cross-component relationships
- Ensure no naming collisions
- Ensure no type conflicts

---

## STEP 5: Conflict Detection

Check for:

| Conflict Type | Example | Action |
|---------------|---------|--------|
| Naming collision | Two `users` tables | STOP |
| Type mismatch | `user_id` is string in A, number in B | STOP |
| Missing reference | A references B's table, B doesn't define it | STOP |
| Circular reference | A needs B, B needs A | STOP |

**If ANY conflict found:**
1. Document in PRECODE-DECISION-NOTES.md
2. STOP Phase 29
3. Roll back to relevant spec in 0c
4. Fix at source
5. Resume after fix is approved

---

## STEP 6: Migration Strategy

Define how schema changes will be handled:
- Migration file format
- Versioning approach
- Rollback procedures
- Data migration patterns

---

## STEP 7: Get Approval

Present to user:
1. DATABASE-SCHEMA.md (complete schema)
2. MIGRATION-STRATEGY.md (change management)
3. Any naming exceptions logged

Wait for APPROVE or REVISE.

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| STORAGE.md files | `{{component}}/spec/STORAGE.md` | Source definitions |
| SCHEMAS.md | `standards/SCHEMAS.md` | Shared types |

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| DATABASE-SCHEMA.md | Project root | Master schema |
| MIGRATION-STRATEGY.md | Project root | Change management |

---

## TEMPLATES

- DATABASE-SCHEMA-TEMPLATE.md
- MIGRATION-STRATEGY-TEMPLATE.md

---

## RULES

- Extract only — do NOT invent tables/fields
- Use `{component}_{entity}` naming
- Log ALL exceptions in decision notes
- STOP on ANY conflict
- Never fix forward

---
