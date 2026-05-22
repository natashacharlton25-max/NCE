# PHASE 26: PROJECT-WIDE SPECS

---
Phase: 26
Name: Project-Wide Specs (cross-cutting conventions, error codes, schemas, security)
Location: NCE-V2/NCE V2.0 Spec & Build/26. Project-Wide-Specs/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You produce project-wide spec docs that apply across all components: API standards, conventions, error codes registry, glossary, schemas, security standards.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
3. [FileTree-v2.md](../../FileTree-v2.md)
4. Phase 25 outputs (PATTERN-ANALYSIS, TYPE-ANALYSIS, ERROR-CODE-ANALYSIS, GAP-ANALYSIS)
5. Per-component specs (Phase 21–24)

---

## TASK

Produce these project-wide docs:

### 1. API-STANDARDS.md
- Worker fetch handler patterns
- Request/response JSON shapes (e.g. `{ ok: true, data: ... }` vs `{ ok: false, error: ... }`)
- Versioning strategy
- Auth header conventions
- CORS policy
- Rate-limiting conventions (resilience/RateLimiter integration)

### 2. CONVENTIONS.md
- TypeScript style (strict mode, naming, file organization)
- Async patterns (D1 await; Worker `ctx.waitUntil()` for fire-and-forget)
- Error throwing vs returning result types
- Logging conventions (system/Logger usage)
- Module/import conventions

### 3. ERROR-CODES.md (project-wide registry)
- Aggregate all error codes from per-component ERRORS.md
- Canonical format: `{SYSTEM}_{CATEGORY}_{SPECIFIC}` (e.g. `BRAND_VALIDATION_INVALID_ID`)
- Categories: Validation / Dependency / State / External / Worker / Unknown
- Resilience/ patterns mapped to categories
- HTTP status code mapping (for Worker fetch handlers)

### 4. GLOSSARY.md
- Project-specific terms (system names, concept terms, output forms)
- TypeScript type names referenced across components
- Acronyms (D1, R2, KV, DO, LOC, etc.)

### 5. SCHEMAS.md
- Shared JSON schemas / TS interfaces used across components
- Library entry schema patterns (from LIBRARY-TEMPLATE.md v2.0.0)
- Asset metadata schema (from `assets/`)
- Common patterns (id, timestamps, version, status enums)

### 6. SECURITY-STANDARDS.md
- Worker secret bindings (no credentials in code)
- D1 query safety (parameterized queries — D1 prepare/bind)
- R2 access patterns (presigned URLs vs direct)
- Input validation requirements
- Output sanitisation (where applicable)
- access/ system patterns (Brand/Capability/Role)

---

## OUTPUT LOCATION

```
NCE-V2/specs/project-wide/
├── API-STANDARDS.md
├── CONVENTIONS.md
├── ERROR-CODES.md
├── GLOSSARY.md
├── SCHEMAS.md
└── SECURITY-STANDARDS.md
```

---

## MANDATORY RULES

- Pull from Phase 25 analysis outputs — don't re-derive
- For ERROR-CODES.md: resolve conflicts flagged in ERROR-CODE-ANALYSIS.md (with user input)
- Apply output-boundary rule
- TypeScript-first conventions (not Python)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All 6 project-wide docs created
- [ ] Error code conflicts resolved or escalated
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 27 (Hardening)

---

## TEMPLATES (enriched for NCE-V2)

- [API-STANDARDS-TEMPLATE.md](./API-STANDARDS-TEMPLATE.md)
- [CONVENTIONS-TEMPLATE.md](./CONVENTIONS-TEMPLATE.md)
- [ERROR-CODES-TEMPLATE.md](./ERROR-CODES-TEMPLATE.md)
- [GLOSSARY-TEMPLATE.md](./GLOSSARY-TEMPLATE.md)
- [SCHEMAS-TEMPLATE.md](./SCHEMAS-TEMPLATE.md)
- [SECURITY-STANDARDS-TEMPLATE.md](./SECURITY-STANDARDS-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
