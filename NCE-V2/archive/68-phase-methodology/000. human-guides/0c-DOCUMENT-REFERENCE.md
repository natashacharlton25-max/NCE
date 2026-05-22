# Section 0c: Complete Document Reference

---
**Section:** 0c Full Specs
**Phases:** 20-28
**Location:** `0. Admin/0c. Full Specs/` + Phase folders
**Audit:** ⭐ MANDATORY after Phase 28
---

## Admin Folder Documents

Location: `0. Admin/0c. Full Specs/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-fullspecs.md** | Instructions for Claude. Defines Claude's role, processing order, rules for spec writing. | Claude reads this |
| **Master-FullSpecs-Guide 20to28.md** | Detailed guide for all spec phases. Explains each phase, lists all templates, shows folder structure. | Reference for both |
| **FULLSPECS-PROGRESS-TEMPLATE.md** | Template to track progress through spec phases. Copy and fill in as you complete. | You fill this in |
| **FULLSPECS-DECISION-NOTES-TEMPLATE.md** | Template to log decisions made during spec writing. | You fill this in |

---

## Phase-by-Phase Documents

### Phase 20: Pre-Spec Notes

**Folder:** `20. Pre-Spec-Notes/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-20-PROMPT.md** | Instructions for Claude. Tells Claude to prepare context notes before writing specs. | Give to Claude |
| **PRE-SPEC-NOTES-TEMPLATE.md** | Template for internal components - captures key decisions, boundaries, behaviors to spec. | Claude creates one per system/subsystem |
| **PRE-SPEC-NOTES-EXTERNAL-TEMPLATE.md** | Template for external integrations - captures provider details, our needs, constraints. | Claude creates one per integration |
| **PRE-SPEC-NOTES-CHECKLIST.md** | Checklist to verify all pre-spec notes are complete. | Claude/you verify |

**Output:** `PRE-SPEC-NOTES.md` (one per component, goes in component folder)

---

### Phase 21: Internal Component Specs

**Folder:** `21. Internal-Component-Specs/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-21-PROMPT.md** | Instructions for Claude. Tells Claude to create full specs for each system and subsystem. | Give to Claude |
| **INDEX-TEMPLATE.md** | Quick reference - status, version, navigation links, build notes. | Claude creates INDEX.md |
| **OVERVIEW-TEMPLATE.md** | Component identity - what it is, what it does, scope, boundaries, guarantees. | Claude creates OVERVIEW.md |
| **FUNCTIONS-TEMPLATE.md** | Public API - all functions with signatures, parameters, return values. | Claude creates FUNCTIONS.md |
| **TYPES-TEMPLATE.md** | Data structures - all types, schemas, enums used by this component. | Claude creates TYPES.md |
| **BEHAVIOUR-TEMPLATE.md** | Process flows - state machines, workflows, sequences. | Claude creates BEHAVIOUR.md |
| **ERRORS-TEMPLATE.md** | Error handling - error codes, categories, recovery actions. | Claude creates ERRORS.md |
| **CONFIG-TEMPLATE.md** | Configuration - settings, options, defaults, constraints. | Claude creates CONFIG.md |
| **STORAGE-TEMPLATE.md** | Data storage - database tables, schemas, queries (or N/A). | Claude creates STORAGE.md |
| **DECISIONS-TEMPLATE.md** | Design rationale - why things are done this way. | Claude creates DECISIONS.md |
| **EXAMPLES-TEMPLATE.md** | Usage examples - request/response pairs, code samples. | Claude creates EXAMPLES.md |

**Output (per system/subsystem):** 10 files in `{{component}}/spec/`

---

### Phase 22: External Integration Specs

**Folder:** `22. External-Integration-Specs/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-22-PROMPT.md** | Instructions for Claude. Tells Claude to create full specs for each external integration. | Give to Claude |
| **INDEX-EXTERNAL-TEMPLATE.md** | Quick reference for integration. | Claude creates INDEX.md |
| **OVERVIEW-EXTERNAL-TEMPLATE.md** | What this integration does, why we need it. | Claude creates OVERVIEW.md |
| **PROVIDER-TEMPLATE.md** | Vendor information - company, SLAs, support, pricing. | Claude creates PROVIDER.md |
| **API-SURFACE-TEMPLATE.md** | External API we consume - endpoints, authentication, limits. | Claude creates API-SURFACE.md |
| **OUR-WRAPPER-TEMPLATE.md** | Our abstraction layer - how we wrap the external API. | Claude creates OUR-WRAPPER.md |
| **FIELD-MAPPING-TEMPLATE.md** | Data translation - external fields ↔ our fields. | Claude creates FIELD-MAPPING.md |
| **CONSTRAINTS-TEMPLATE.md** | Limits - rate limits, quotas, restrictions. | Claude creates CONSTRAINTS.md |
| **FAILURE-MODES-TEMPLATE.md** | What can go wrong - timeouts, errors, outages. | Claude creates FAILURE-MODES.md |
| **ERRORS-EXTERNAL-TEMPLATE.md** | Provider's error codes and our handling. | Claude creates ERRORS.md |
| **EXAMPLES-TEMPLATE.md** | Request/response examples. | Claude creates EXAMPLES.md |

**Output (per integration):** 10+ files in `integration-{{provider}}/{{service}}/spec/`

---

### Phase 23: Library Specs

**Folder:** `23. Library-Specs/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-23-PROMPT.md** | Instructions for Claude. Tells Claude to spec any shared libraries. | Give to Claude |
| **INDEX-LIBRARY-TEMPLATE.md** | Quick reference for library. | Claude creates INDEX.md |
| **OVERVIEW-LIBRARY-TEMPLATE.md** | What this library provides. | Claude creates OVERVIEW.md |
| **FUNCTIONS-LIBRARY-TEMPLATE.md** | Public API of the library. | Claude creates FUNCTIONS.md |
| **TYPES-TEMPLATE.md** | Shared types provided by library. | Claude creates TYPES.md |
| **ERRORS-TEMPLATE.md** | Error handling in library. | Claude creates ERRORS.md |
| **EXAMPLES-TEMPLATE.md** | How to use the library. | Claude creates EXAMPLES.md |

**Output (per library):** 6+ files in `lib-{{library}}/spec/`

**Note:** This phase may be N/A if no shared libraries were identified.

---

### Phase 24: Repo Specs

**Folder:** `24. Repo-Specs/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-24-PROMPT.md** | Instructions for Claude. Tells Claude to define the repository structure. | Give to Claude |
| **REPO-SPEC-TEMPLATE.md** | Repository layout - folder structure, naming conventions, where things go. | Claude fills this |

**Output:** `REPO-SPEC.md`

---

### Phase 25: Post-Spec Analysis

**Folder:** `25. Post-Spec-Analysis/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-25-PROMPT.md** | Instructions for Claude. Tells Claude to analyze all specs for gaps and issues. | Give to Claude |
| **GAP-ANALYSIS-TEMPLATE.md** | Missing specs, incomplete sections, things that need more work. | Claude creates GAP-ANALYSIS.md |
| **DEPENDENCY-ANALYSIS-TEMPLATE.md** | Cross-component dependencies found in specs. | Claude creates DEPENDENCY-ANALYSIS.md |
| **PATTERN-ANALYSIS-TEMPLATE.md** | Repeated patterns that could be extracted into shared code. | Claude creates PATTERN-ANALYSIS.md |
| **ERROR-CODE-ANALYSIS-TEMPLATE.md** | Error code conflicts, overlaps, gaps in numbering. | Claude creates ERROR-CODE-ANALYSIS.md |
| **TYPE-ANALYSIS-TEMPLATE.md** | Type inconsistencies across specs. | Claude creates TYPE-ANALYSIS.md |

**Output:** 5 files in `/analysis/`

---

### Phase 26: Project-Wide Specs

**Folder:** `26. Project-Wide-Specs/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-26-PROMPT.md** | Instructions for Claude. Tells Claude to create project-wide standards. | Give to Claude |
| **GLOSSARY-TEMPLATE.md** | Project terminology - words and their definitions. | Claude creates GLOSSARY.md |
| **SCHEMAS-TEMPLATE.md** | Shared data schemas used across components. | Claude creates SCHEMAS.md |
| **ERROR-CODES-TEMPLATE.md** | Master list of all error codes in the project. | Claude creates ERROR-CODES.md |
| **API-STANDARDS-TEMPLATE.md** | API conventions - naming, versioning, patterns. | Claude creates API-STANDARDS.md |
| **SECURITY-STANDARDS-TEMPLATE.md** | Security requirements - auth, encryption, access control. | Claude creates SECURITY-STANDARDS.md |
| **CONVENTIONS-TEMPLATE.md** | Naming conventions, formatting standards. | Claude creates CONVENTIONS.md |
| **TYPES-TEMPLATE.md** | Shared types used across the project. | Claude creates TYPES.md |

**Output:** 6+ files in `/standards/`

---

### Phase 27: Hardening

**Folder:** `27. Hardening/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-27-PROMPT.md** | Instructions for Claude. Tells Claude to stress-test specs for problems. | Give to Claude |
| **AMBIGUITY-REVIEW-TEMPLATE.md** | Unclear or vague specs that need clarification. | Claude creates AMBIGUITY-REVIEW.md |
| **COMPLETENESS-REVIEW-TEMPLATE.md** | Missing information, incomplete sections. | Claude creates COMPLETENESS-REVIEW.md |
| **TEST-COVERAGE-REVIEW-TEMPLATE.md** | Can each spec be tested? What tests are needed? | Claude creates TEST-COVERAGE-REVIEW.md |
| **IMPLEMENTABILITY-REVIEW-TEMPLATE.md** | Can this actually be built? Any blockers? | Claude creates IMPLEMENTABILITY-REVIEW.md |
| **IMPLEMENTATION-READY-TEMPLATE.md** | Matrix showing which components are ready. | Claude creates IMPLEMENTATION-READY.md |
| **HARDENING-SUMMARY-TEMPLATE.md** | Overall findings and issues to address. | Claude creates HARDENING-SUMMARY.md |

**Output:** 6 files in `/hardening/`

---

### Phase 28: Full-Spec Checklist

**Folder:** `28. Full-Spec-Checklist/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-28-PROMPT.md** | Instructions for Claude. Tells Claude to verify all specs are complete. | Give to Claude |
| **SPEC-MANIFEST-TEMPLATE.md** | Complete list of all spec files created. | Claude creates SPEC-MANIFEST.md |
| **MASTER-CHECKLIST-TEMPLATE.md** | Final verification - all systems? All subsystems? All integrations? | Claude creates MASTER-CHECKLIST.md |
| **PROGRESS-TRACKER-TEMPLATE.md** | Phase completion status. | Claude creates PROGRESS-TRACKER.md |
| **IMPLEMENTATION-HANDOFF-TEMPLATE.md** | What implementers need to know before coding. | Claude creates IMPLEMENTATION-HANDOFF.md |
| **LESSONS-LEARNED-TEMPLATE.md** | Notes for future projects. | Claude creates LESSONS-LEARNED.md |

**Output:** 5 files in `/admin/`

---

## Order to Run Section 0c

```
Step 1:  Read Claude-fullspecs.md (understand section)
Step 2:  Create FULLSPECS-PROGRESS.md from template
Step 3:  Phase 20 - Create PRE-SPEC-NOTES.md for all components
Step 4:  Phase 21 - Create 10 spec files per system/subsystem
         (Do systems first, then their subsystems, alphabetically)
Step 5:  Phase 22 - Create 10+ spec files per external integration
Step 6:  Phase 23 - Create library specs (if applicable)
Step 7:  Phase 24 - Create REPO-SPEC.md
Step 8:  Phase 25 - Create 5 analysis files
Step 9:  Phase 26 - Create 6+ project-wide standard files
Step 10: Phase 27 - Create 6 hardening review files
Step 11: Phase 28 - Create 5 verification files
Step 12: ⭐ MANDATORY: Run 0c audit before proceeding!
Step 13: Address audit findings
Step 14: Proceed to Section 0d
```

---

## Spec Files Per Component

### Internal Component (System/Subsystem) — 10 files
| File | What It Contains |
|------|------------------|
| INDEX.md | Quick reference, status, links |
| OVERVIEW.md | What it is, what it does |
| FUNCTIONS.md | Public API |
| TYPES.md | Data structures |
| BEHAVIOUR.md | Process flows |
| ERRORS.md | Error handling |
| CONFIG.md | Configuration |
| STORAGE.md | Database (or N/A) |
| DECISIONS.md | Design rationale |
| EXAMPLES.md | Usage examples |

### External Integration — 10+ files
| File | What It Contains |
|------|------------------|
| INDEX.md | Quick reference |
| OVERVIEW.md | Integration purpose |
| PROVIDER.md | Vendor info |
| API-SURFACE.md | External API |
| OUR-WRAPPER.md | Our abstraction |
| FIELD-MAPPING.md | Data translation |
| CONSTRAINTS.md | Limits |
| FAILURE-MODES.md | What can fail |
| ERRORS.md | Error codes |
| EXAMPLES.md | Examples |

---

*Give PHASE-XX-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
