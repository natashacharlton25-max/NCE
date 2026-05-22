# PHASE 30: API SURFACE CONSOLIDATION

---
Phase: 30
Name: API Surface Consolidation
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are consolidating all API definitions from component FUNCTIONS.md files into a unified API specification.

This is **extraction and consolidation** — NOT design. The specs define what exists.

---

## CRITICAL RULE

**Do NOT infer API behaviour beyond what is explicitly stated in FUNCTIONS.md.**

- If behaviour is unclear → flag as a gap
- If details are missing → note them, do NOT fill in
- If something seems implied but isn't written → it doesn't exist

---

## TASK

1. Gather all FUNCTIONS.md files from component specs
2. Extract all public function/endpoint definitions
3. Consolidate into master API specification
4. Check for conflicts — if found, STOP and roll back
5. Verify completeness
6. Get approval

---

## STEP 1: Gather FUNCTIONS.md Files

List every component that exposes APIs:

```
{{system-a}}/spec/FUNCTIONS.md
{{system-a}}/{{subsystem-1}}/spec/FUNCTIONS.md
{{system-b}}/spec/FUNCTIONS.md
...
```

Also gather:
- `standards/API-STANDARDS.md` from Phase 26
- `standards/ERROR-CODES.md` from Phase 26

---

## STEP 2: Extract Definitions

From each FUNCTIONS.md, extract ONLY what is explicitly documented:

- Function/endpoint name
- HTTP method (if REST)
- Path/route
- Parameters (with types)
- Request body schema
- Response schema
- Error codes
- Authentication requirements

**Do NOT add:**
- Assumed parameters
- Implied validation
- Guessed error cases
- Undocumented behaviour

---

## STEP 3: Consolidate

**Grouping:** By domain/resource

```
/api/auth/...     ← Auth system endpoints
/api/content/...  ← Content system endpoints
/api/users/...    ← Users system endpoints
```

**For each endpoint, document:**
- Full path
- Method
- Request format
- Response format
- Errors
- Auth requirements

---

## STEP 4: Conflict Detection

Check for:

| Conflict Type | Example | Action |
|---------------|---------|--------|
| Path collision | Two components use `/api/users` | STOP |
| Method collision | Both define `GET /api/items` differently | STOP |
| Type mismatch | Same field, different types | STOP |
| Error code collision | Same code, different meanings | STOP |

**If ANY conflict found:**
1. Document in PRECODE-DECISION-NOTES.md
2. STOP Phase 30
3. Roll back to relevant spec in 0c
4. Fix at source
5. Resume after fix is approved

---

## STEP 5: Verify Completeness

Check:
- [ ] Every component's public functions represented
- [ ] All error codes documented
- [ ] All request/response types defined
- [ ] All auth requirements noted
- [ ] No gaps flagged without resolution

---

## STEP 6: Get Approval

Present to user:
1. API-SURFACE.md (complete API specification)
2. Any gaps or unclear items flagged

Wait for APPROVE or REVISE.

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| FUNCTIONS.md files | `{{component}}/spec/FUNCTIONS.md` | Source definitions |
| API-STANDARDS.md | `standards/API-STANDARDS.md` | Conventions |
| ERROR-CODES.md | `standards/ERROR-CODES.md` | Error definitions |

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| API-SURFACE.md | Project root | Master API specification |

---

## TEMPLATES

- API-SURFACE-TEMPLATE.md

---

## RULES

- Extract only — do NOT invent endpoints
- Do NOT infer behaviour — only document explicit specs
- Flag gaps, don't fill them
- STOP on ANY conflict
- Never fix forward

---
