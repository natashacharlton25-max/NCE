# PHASE 26: PROJECT-WIDE SPECS

---
Phase: 26
Name: Project-Wide Specs
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/26.Project-Wide-Specs/
---

## ROLE

You are creating project-wide specification documents that apply across ALL components — shared types, error codes, conventions, and standards.

---

## PURPOSE

Project-Wide Specs:
- Consolidate shared definitions in one place
- Establish project-wide conventions
- Prevent duplication across component specs
- Serve as single source of truth

---

## TASK

Create 6 project-wide specification documents:

| Document | Purpose | Template |
|----------|---------|----------|
| SCHEMAS.md | Shared types used across components | **Create using SCHEMAS-TEMPLATE.md** |
| ERROR-CODES.md | Master registry of all error codes | **Create using ERROR-CODES-TEMPLATE.md** |
| CONVENTIONS.md | Naming, formatting, patterns | **Create using CONVENTIONS-TEMPLATE.md** |
| GLOSSARY.md | Domain terms and definitions | **Create using GLOSSARY-TEMPLATE.md** |
| API-STANDARDS.md | API design standards | **Create using API-STANDARDS-TEMPLATE.md** |
| SECURITY-STANDARDS.md | Security requirements | **Create using SECURITY-STANDARDS-TEMPLATE.md** |

---

## INPUT SOURCES

| Document | Primary Input |
|----------|---------------|
| SCHEMAS.md | Phase 25 TYPE-ANALYSIS (shared type candidates) |
| ERROR-CODES.md | Phase 25 ERROR-CODE-ANALYSIS (full registry) |
| CONVENTIONS.md | Phase 24 REPO-SPEC + patterns from Phase 25 |
| GLOSSARY.md | All specs (domain terms) |
| API-STANDARDS.md | FUNCTIONS.md patterns |
| SECURITY-STANDARDS.md | Gap analysis + security considerations |

---

## OUTPUT LOCATION

```
project/
├── 0. Admin/
│   └── 0c. Full Specs/
│       └── 26.Project-Wide-Specs/
│           ├── SCHEMAS.md
│           ├── ERROR-CODES.md
│           ├── CONVENTIONS.md
│           ├── GLOSSARY.md
│           ├── API-STANDARDS.md
│           └── SECURITY-STANDARDS.md
│
└── src/
    └── shared/           ← Implementation location
        ├── types/        ← From SCHEMAS.md
        ├── errors/       ← From ERROR-CODES.md
        └── constants/    ← From CONVENTIONS.md
```

---

## PROCESS

### Step 1: SCHEMAS.md
1. Gather shared type candidates from Phase 25
2. Define canonical versions
3. Document where each is used

### Step 2: ERROR-CODES.md
1. Collect all error codes from Phase 25
2. Organize by component and category
3. Ensure uniqueness

### Step 3: CONVENTIONS.md
1. Extract patterns from Phase 25
2. Document as conventions
3. Add examples

### Step 4: GLOSSARY.md
1. Scan all specs for domain terms
2. Define each term
3. Note any synonyms

### Step 5: API-STANDARDS.md
1. Extract common API patterns
2. Document as standards
3. Provide examples

### Step 6: SECURITY-STANDARDS.md
1. Gather security requirements
2. Document standards
3. Reference compliance needs

---

## RULES

### DO:
- Reference specific components using each shared item
- Provide concrete examples
- Keep definitions precise
- Update when components change

### DO NOT:
- Duplicate full specs from components
- Include implementation details
- Make project-wide what should be local
- Let documents get stale

---

## CROSS-REFERENCES

Each project-wide doc should reference:
- Which component specs use it
- Which Phase 25 analysis identified it
- Related project-wide docs

Each component spec should reference:
- Which project-wide docs it uses
- Specific sections/items used

---

## APPROVAL GATE

Phase 26 is COMPLETE when:

- [ ] All 6 documents created
- [ ] All shared types documented
- [ ] All error codes registered
- [ ] Conventions are clear and complete
- [ ] Glossary covers domain terms
- [ ] Standards are actionable
- [ ] Human approves

---

## MAINTENANCE

| Trigger | Action |
|---------|--------|
| New component added | Update ERROR-CODES.md, possibly others |
| New shared type needed | Add to SCHEMAS.md |
| New term introduced | Add to GLOSSARY.md |
| Pattern established | Add to CONVENTIONS.md |

---

## NEXT PHASE

After Phase 26 → Phase 27 (Hardening)

---
Generated: {{timestamp}}
---
