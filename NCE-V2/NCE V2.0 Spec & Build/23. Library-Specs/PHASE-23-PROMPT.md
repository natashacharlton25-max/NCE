# PHASE 23: LIBRARY SPECS

---
Phase: 23
Name: Library Specs
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/23.Library-Specs/
---

## ROLE

You are writing implementation-ready specifications for shared libraries — reusable code that multiple systems or subsystems depend on.

Libraries differ from systems/subsystems:
- **Systems/Subsystems:** Own responsibilities, make decisions
- **Libraries:** Provide utilities, make NO decisions, just execute

---

## WHAT IS A LIBRARY?

Libraries are identified during Phase 25 (Post-Spec Analysis) when patterns emerge:

| Signal | Example |
|--------|---------|
| Same code in 3+ places | Date formatting, ID generation |
| Pure utility function | No side effects, no state |
| Shared data structure | Common types used everywhere |
| Cross-cutting concern | Logging, validation, error formatting |

### Library Characteristics

| Characteristic | Library | System/Subsystem |
|----------------|---------|------------------|
| Has state | No (or minimal) | Yes |
| Makes decisions | No | Yes |
| Has dependencies | Minimal | Can have many |
| Owns data | No | Yes |
| Called by | Many consumers | Specific callers |

---

## TASK

**For EACH identified library:**

1. Read the library's PRE-SPEC-NOTES.md (from Phase 20)
2. Create `spec/` folder in the library directory
3. Create spec files using the templates
4. Get approval before moving to next library

---

## SPEC FILES FOR LIBRARIES

Libraries use a SUBSET of internal component specs:

| File | Required | Notes |
|------|----------|-------|
| INDEX.md | Yes | Quick reference |
| OVERVIEW.md | Yes | Purpose, boundaries |
| FUNCTIONS.md | Yes | The main spec — all functions |
| TYPES.md | Yes | Data structures |
| BEHAVIOUR.md | Maybe | Only if stateful |
| ERRORS.md | Yes | Error codes |
| CONFIG.md | Maybe | Only if configurable |
| STORAGE.md | No | Libraries don't store data |
| DECISIONS.md | Maybe | Only if significant choices |
| EXAMPLES.md | Yes | Usage examples |

**Typical library:** INDEX, OVERVIEW, FUNCTIONS, TYPES, ERRORS, EXAMPLES (6 files)

---

## PROCESS ORDER

1. Identify libraries during Phase 25 (Post-Spec Analysis)
2. Create PRE-SPEC-NOTES.md for each library (back to Phase 20)
3. Create library specs (this phase)

**Library naming convention:** `lib-{{name}}`

Example structure:
```
project/
├── lib-validation/
│   └── spec/
│       ├── INDEX.md
│       ├── OVERVIEW.md
│       ├── FUNCTIONS.md
│       ├── TYPES.md
│       ├── ERRORS.md
│       └── EXAMPLES.md
│
├── lib-datetime/
│   └── spec/
│       └── ...
│
└── lib-ids/
    └── spec/
        └── ...
```

---

## COMMON LIBRARY TYPES

### Validation Library (`lib-validation`)
- Input validation functions
- Schema validation
- Common validation rules

### DateTime Library (`lib-datetime`)
- Date/time formatting
- Timezone handling
- Duration calculations

### ID Library (`lib-ids`)
- ID generation (UUID, slug, etc.)
- ID validation
- ID parsing

### Error Library (`lib-errors`)
- Error creation helpers
- Error formatting
- Error code constants

### String Library (`lib-strings`)
- String formatting
- Sanitization
- Truncation

### Collection Library (`lib-collections`)
- Array utilities
- Object utilities
- Map/Set helpers

---

## RULES

### DO:
- Keep libraries focused — one concern per library
- Make all functions pure when possible
- Document every parameter and return value
- Provide many examples
- Test edge cases

### DO NOT:
- Add business logic to libraries
- Make libraries depend on systems
- Store state (except caching for performance)
- Make decisions that belong to consumers
- Create circular dependencies

---

## LIBRARY DEPENDENCY RULES

| Allowed | Not Allowed |
|---------|-------------|
| Library → Library | Library → System |
| System → Library | Library → Subsystem |
| Subsystem → Library | Library → External Integration |

---

## TEMPLATES

Libraries use the same templates as internal components (Phase 21), with modifications:

- **FUNCTIONS.md:** Primary focus — comprehensive function specs
- **OVERVIEW.md:** Emphasize "no decisions, just utilities"
- **EXAMPLES.md:** More examples than typical — libraries need them

Use templates from:
- `/0. Admin/0c. Full Specs/21.Internal-Component-Specs/`

---

## APPROVAL GATE

Per library:
- [ ] All required spec files created
- [ ] INDEX.md shows all files status
- [ ] FUNCTIONS.md is comprehensive
- [ ] EXAMPLES.md covers common use cases
- [ ] No business logic in library
- [ ] Human approves

---

## END CONDITION

Phase 23 is COMPLETE when:
- [ ] All identified libraries have specs
- [ ] All specs approved by human
- [ ] No circular dependencies
- [ ] All consuming systems reference library correctly

**Next:** Phase 24 (Repo Specs)

---

## INPUTS

From Phase 25 (Post-Spec Analysis):
- List of identified libraries
- Patterns that justified extraction

From Phase 20:
- PRE-SPEC-NOTES.md for each library

---

## NOTES

- Libraries may be identified AFTER initial spec phases
- If Phase 25 identifies new libraries, return to Phase 20 → 23
- Keep libraries small — split if >500 LOC expected

---
Generated: {{timestamp}}
---
