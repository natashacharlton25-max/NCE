# 0c Full Specs File Manifest

---
Section: 0c Full Specs (Phases 20-28)
Purpose: Complete list of files available for audit review
---

## How to Use This Manifest

This lists all files created during Section 0c. When requesting files for audit:

1. Request specific files by name
2. Use **dependency bundles** to get related files together
3. Maximum requests: 7 files (Round 1) + 4 files (Round 2)

---

## Dependency Bundles

Request a bundle to get multiple related files without using extra requests.

| Bundle Name | Request | You Get |
|-------------|---------|---------|
| **Component Spec** | "{{component}} spec bundle" | INDEX, OVERVIEW, FUNCTIONS, TYPES, BEHAVIOUR, ERRORS, CONFIG, STORAGE |
| **Integration Spec** | "{{integration}} integration bundle" | INDEX, OVERVIEW, API-SURFACE, FIELD-MAPPING, OUR-WRAPPER, CONSTRAINTS, ERRORS, FAILURE-MODES |
| **Project Standards** | "Project standards bundle" | ERROR-CODES, SCHEMAS, TYPES, API-STANDARDS, SECURITY-STANDARDS, CONVENTIONS |
| **Quality Gate** | "Quality gate bundle" | HARDENING-SUMMARY, COMPLETENESS-REVIEW, AMBIGUITY-REVIEW, IMPLEMENTABILITY-REVIEW |
| **Analysis** | "Analysis bundle" | GAP-ANALYSIS, PATTERN-ANALYSIS, DEPENDENCY-ANALYSIS, TYPE-ANALYSIS, ERROR-CODE-ANALYSIS |
| **Final Gate** | "Final gate bundle" | MASTER-CHECKLIST, SPEC-MANIFEST, IMPLEMENTATION-HANDOFF |

---

## Per-Component Specs (Phase 21)

### Systems

Each system has these files in `{{system}}/`:

| File | Purpose |
|------|---------|
| INDEX.md | Navigation and overview |
| OVERVIEW.md | Purpose, responsibilities |
| FUNCTIONS.md | Operations and signatures |
| TYPES.md | Type definitions |
| BEHAVIOUR.md | Business logic |
| ERRORS.md | Error conditions |
| CONFIG.md | Configuration |
| STORAGE.md | Data persistence |
| DECISIONS.md | Design decisions |
| EXAMPLES.md | Usage examples |

**Systems in this project:**
| System | Location | Files |
|--------|----------|-------|
| {{system_name}} | {{path}} | 10 |
| {{system_name}} | {{path}} | 10 |

### Subsystems

Each subsystem has the same 10 files in `{{system}}/{{subsystem}}/`:

**Subsystems in this project:**
| Subsystem | Parent System | Location | Files |
|-----------|---------------|----------|-------|
| {{subsystem_name}} | {{system}} | {{path}} | 10 |
| {{subsystem_name}} | {{system}} | {{path}} | 10 |

---

## External Integration Specs (Phase 22)

Each integration has these files in `integrations/{{provider}}/`:

| File | Purpose |
|------|---------|
| INDEX.md | Navigation |
| OVERVIEW.md | Integration purpose |
| API-SURFACE.md | External endpoints used |
| FIELD-MAPPING.md | Data mapping |
| OUR-WRAPPER.md | Abstraction layer |
| CONSTRAINTS.md | Limits and quotas |
| ERRORS.md | Error handling |
| FAILURE-MODES.md | Failure scenarios |
| EXAMPLES.md | Integration examples |

**Integrations in this project:**
| Integration | Provider | Location | Files |
|-------------|----------|----------|-------|
| {{name}} | {{provider}} | {{path}} | 9 |

---

## Library Specs (Phase 23)

Each shared library has these files in `libs/{{library}}/`:

| File | Purpose |
|------|---------|
| INDEX.md | Navigation |
| OVERVIEW.md | Library purpose |
| FUNCTIONS.md | Exported functions |
| TYPES.md | Exported types |
| ERRORS.md | Error handling |
| EXAMPLES.md | Usage examples |

**Libraries in this project:**
| Library | Location | Files |
|---------|----------|-------|
| {{library_name}} | {{path}} | 6 |

---

## Repo Specs (Phase 24)

| File | Location | Purpose |
|------|----------|---------|
| REPO-SPEC.md | / | Folder structure and organization |

---

## Post-Spec Analysis (Phase 25)

| File | Location | Purpose |
|------|----------|---------|
| GAP-ANALYSIS.md | /analysis/ | Missing items identified |
| PATTERN-ANALYSIS.md | /analysis/ | Common patterns |
| DEPENDENCY-ANALYSIS.md | /analysis/ | Component dependencies |
| TYPE-ANALYSIS.md | /analysis/ | Type consistency |
| ERROR-CODE-ANALYSIS.md | /analysis/ | Error code review |

---

## Project-Wide Specs (Phase 26)

| File | Location | Purpose |
|------|----------|---------|
| ERROR-CODES.md | /project/ | All error codes |
| SCHEMAS.md | /project/ | Data schemas |
| TYPES.md | /project/ | Shared types |
| API-STANDARDS.md | /project/ | API conventions |
| SECURITY-STANDARDS.md | /project/ | Security requirements |
| CONVENTIONS.md | /project/ | Naming conventions |
| GLOSSARY.md | /project/ | Terminology |

---

## Hardening Files (Phase 27)

| File | Location | Purpose |
|------|----------|---------|
| COMPLETENESS-REVIEW.md | /hardening/ | Completeness audit |
| AMBIGUITY-REVIEW.md | /hardening/ | Ambiguity audit |
| IMPLEMENTABILITY-REVIEW.md | /hardening/ | Implementability audit |
| TEST-COVERAGE-REVIEW.md | /hardening/ | Test coverage audit |
| HARDENING-SUMMARY.md | /hardening/ | Summary with metrics |

---

## Final Gate Files (Phase 28)

| File | Location | Purpose |
|------|----------|---------|
| MASTER-CHECKLIST.md | / | Final verification |
| SPEC-MANIFEST.md | / | Complete file list |
| IMPLEMENTATION-HANDOFF.md | / | Handoff to 0d |

---

## File Count Summary

| Category | Count |
|----------|-------|
| System specs | {{systems}} × 10 = {{total}} |
| Subsystem specs | {{subsystems}} × 10 = {{total}} |
| Integration specs | {{integrations}} × 9 = {{total}} |
| Library specs | {{libraries}} × 6 = {{total}} |
| Repo specs | 1 |
| Analysis files | 5 |
| Project-wide specs | 7 |
| Hardening files | 5 |
| Gate files | 3 |
| **TOTAL** | **{{total}}** |

---

## Priority Files for Audit

Start with these for the most insight:

1. **HARDENING-SUMMARY.md** - Overall quality metrics
2. **MASTER-CHECKLIST.md** - Completion status
3. **ERROR-CODES.md** - Error handling completeness
4. **GAP-ANALYSIS.md** - Known gaps (were they addressed?)
5. **{{core_system}}/FUNCTIONS.md** - Core business logic
6. **TYPE-ANALYSIS.md** - Type consistency check

---

## Files from Previous Sections

These files from 0a/0b may be referenced but are NOT part of 0c:

| File | Section | May Be Useful For |
|------|---------|-------------------|
| PASS-NOTES.md (per component) | 0b | Original context |
| CONTRACT.md (per component) | 0b | Interface definitions |
| DEPENDENCY-MAP.md | 0b | Build order |
| PROJECT-OVERVIEW.md | 0a | Project scope |
| PROJECT-INTENTION.md | 0a | Success criteria |

---

*End of File Manifest*
