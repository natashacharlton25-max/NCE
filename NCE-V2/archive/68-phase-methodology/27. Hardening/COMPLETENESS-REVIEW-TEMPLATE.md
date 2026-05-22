# Completeness Review

---
Phase: 27 (Hardening)
Review: 1 of 4 — Completeness
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Verify all spec files have all required sections filled with meaningful content.

---

## Summary

| Metric | Count | Percentage |
|--------|-------|------------|
| Components reviewed | {{n}} | 100% |
| Fully complete | {{n}} | {{%}} |
| Partially complete | {{n}} | {{%}} |
| Incomplete | {{n}} | {{%}} |

**Overall Completeness:** {{%}}

---

## Review Criteria

### Required per Component Type

| File | System | Subsystem | Integration | Library |
|------|--------|-----------|-------------|---------|
| INDEX.md | ✅ | ✅ | ✅ | ✅ |
| OVERVIEW.md | ✅ | ✅ | ✅ | ✅ |
| FUNCTIONS.md | ✅ | ✅ | OUR-WRAPPER.md | ✅ |
| TYPES.md | ✅ | ✅ | FIELD-MAPPING.md | ✅ |
| BEHAVIOUR.md | ✅ | ✅ | FAILURE-MODES.md | Optional |
| ERRORS.md | ✅ | ✅ | ✅ | ✅ |
| CONFIG.md | ✅ | ✅ | CONSTRAINTS.md | Optional |
| STORAGE.md | If needed | If needed | N/A | N/A |
| DECISIONS.md | ✅ | ✅ | N/A | Optional |
| EXAMPLES.md | ✅ | ✅ | ✅ | ✅ |
| API-SURFACE.md | N/A | N/A | ✅ | N/A |

---

## Systems Review

### {{System Name}}

| File | Status | Missing Sections | Notes |
|------|--------|------------------|-------|
| INDEX.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| OVERVIEW.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| FUNCTIONS.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| TYPES.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| BEHAVIOUR.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| ERRORS.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| CONFIG.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| STORAGE.md | ✅ / ⚠️ / ❌ / N/A | {{list or "None"}} | |
| DECISIONS.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |
| EXAMPLES.md | ✅ / ⚠️ / ❌ | {{list or "None"}} | |

**System Status:** Complete / Partial / Incomplete

**Missing Items:**
1. {{specific missing item}}
2. {{specific missing item}}

---

### {{Next System}}

(Repeat for each system)

---

## Subsystems Review

### {{Parent System}} / {{Subsystem Name}}

| File | Status | Missing Sections | Notes |
|------|--------|------------------|-------|
| INDEX.md | ✅ / ⚠️ / ❌ | | |
| OVERVIEW.md | ✅ / ⚠️ / ❌ | | |
| FUNCTIONS.md | ✅ / ⚠️ / ❌ | | |
| TYPES.md | ✅ / ⚠️ / ❌ | | |
| BEHAVIOUR.md | ✅ / ⚠️ / ❌ | | |
| ERRORS.md | ✅ / ⚠️ / ❌ | | |
| CONFIG.md | ✅ / ⚠️ / ❌ | | |
| STORAGE.md | ✅ / ⚠️ / ❌ / N/A | | |
| DECISIONS.md | ✅ / ⚠️ / ❌ | | |
| EXAMPLES.md | ✅ / ⚠️ / ❌ | | |

**Subsystem Status:** Complete / Partial / Incomplete

---

## Integrations Review

### {{Provider}} / {{Service}}

| File | Status | Missing Sections | Notes |
|------|--------|------------------|-------|
| INDEX.md | ✅ / ⚠️ / ❌ | | |
| OVERVIEW.md | ✅ / ⚠️ / ❌ | | |
| API-SURFACE.md | ✅ / ⚠️ / ❌ | | |
| OUR-WRAPPER.md | ✅ / ⚠️ / ❌ | | |
| FIELD-MAPPING.md | ✅ / ⚠️ / ❌ | | |
| ERRORS.md | ✅ / ⚠️ / ❌ | | |
| CONSTRAINTS.md | ✅ / ⚠️ / ❌ | | |
| FAILURE-MODES.md | ✅ / ⚠️ / ❌ | | |
| EXAMPLES.md | ✅ / ⚠️ / ❌ | | |

**Integration Status:** Complete / Partial / Incomplete

---

## Libraries Review

### {{Library Name}}

| File | Status | Missing Sections | Notes |
|------|--------|------------------|-------|
| INDEX.md | ✅ / ⚠️ / ❌ | | |
| OVERVIEW.md | ✅ / ⚠️ / ❌ | | |
| FUNCTIONS.md | ✅ / ⚠️ / ❌ | | |
| TYPES.md | ✅ / ⚠️ / ❌ | | |
| ERRORS.md | ✅ / ⚠️ / ❌ | | |
| EXAMPLES.md | ✅ / ⚠️ / ❌ | | |

**Library Status:** Complete / Partial / Incomplete

---

## Project-Wide Docs Review

| Document | Status | Missing Sections |
|----------|--------|------------------|
| SCHEMAS.md | ✅ / ⚠️ / ❌ | |
| ERROR-CODES.md | ✅ / ⚠️ / ❌ | |
| CONVENTIONS.md | ✅ / ⚠️ / ❌ | |
| GLOSSARY.md | ✅ / ⚠️ / ❌ | |
| API-STANDARDS.md | ✅ / ⚠️ / ❌ | |
| SECURITY-STANDARDS.md | ✅ / ⚠️ / ❌ | |

---

## Section-Level Completeness

### Most Commonly Missing

| Section | Missing In | Priority |
|---------|------------|----------|
| Error codes per function | {{n}} components | High |
| Edge case examples | {{n}} components | Medium |
| Config defaults | {{n}} components | Medium |
| State machine diagrams | {{n}} components | Low |

---

## Action Items

### Critical (Blocking)

| ID | Component | Missing | Effort | Owner |
|----|-----------|---------|--------|-------|
| C-001 | {{component}} | {{what}} | {{time}} | |
| C-002 | {{component}} | {{what}} | {{time}} | |

### High Priority

| ID | Component | Missing | Effort | Owner |
|----|-----------|---------|--------|-------|
| H-001 | {{component}} | {{what}} | {{time}} | |

### Medium Priority

| ID | Component | Missing | Effort | Owner |
|----|-----------|---------|--------|-------|
| M-001 | {{component}} | {{what}} | {{time}} | |

### Low Priority

| ID | Component | Missing | Effort | Owner |
|----|-----------|---------|--------|-------|
| L-001 | {{component}} | {{what}} | {{time}} | |

---

## Sign-off

- [ ] All systems reviewed
- [ ] All subsystems reviewed
- [ ] All integrations reviewed
- [ ] All libraries reviewed
- [ ] Project-wide docs reviewed
- [ ] Action items prioritized

**Reviewer:** Claude
**Date:** {{timestamp}}

---
Phase: 27 (Hardening)
Generated: {{timestamp}}
---
