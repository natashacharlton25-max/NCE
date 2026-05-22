# 0c Full Specs Audit Checklist

---
Section: 0c Full Specs (Phases 20-28)
Purpose: Verification checklist for external auditor
Based On: Existing MASTER-CHECKLIST-TEMPLATE.md and HARDENING-SUMMARY-TEMPLATE.md
---

## Phase 20: Pre-Spec Notes

- [ ] PRE-SPEC-NOTES.md created for each component
- [ ] Context from 0b (PASS-NOTES.md) incorporated
- [ ] Component boundaries clear
- [ ] Dependencies identified

## Phase 21: Internal Component Specs

For **each system and subsystem**, verify:

### Required Files (10 per component)
- [ ] INDEX.md - Component overview and navigation
- [ ] OVERVIEW.md - Purpose, responsibilities, boundaries
- [ ] FUNCTIONS.md - All operations with signatures
- [ ] TYPES.md - Type definitions and interfaces
- [ ] BEHAVIOUR.md - Business logic and workflows
- [ ] ERRORS.md - Error conditions and handling
- [ ] CONFIG.md - Configuration options
- [ ] STORAGE.md - Data persistence requirements
- [ ] DECISIONS.md - Design decisions and rationale
- [ ] EXAMPLES.md - Usage examples

### Content Quality
- [ ] Functions have clear input/output types
- [ ] Error cases are enumerated
- [ ] Business rules are explicit
- [ ] Data schemas are defined
- [ ] No placeholder text remains

## Phase 22: External Integration Specs

For **each external integration**, verify:

### Required Files
- [ ] INDEX.md - Integration overview
- [ ] OVERVIEW.md - What this integration does
- [ ] API-SURFACE.md - External API endpoints used
- [ ] FIELD-MAPPING.md - Our data ↔ their data
- [ ] OUR-WRAPPER.md - Our abstraction layer
- [ ] CONSTRAINTS.md - Rate limits, quotas, limitations
- [ ] ERRORS.md - Error handling for external failures
- [ ] FAILURE-MODES.md - What happens when they're down
- [ ] EXAMPLES.md - Integration examples

## Phase 23: Library Specs (if applicable)

For **each shared library**, verify:

- [ ] INDEX.md
- [ ] OVERVIEW.md
- [ ] FUNCTIONS.md
- [ ] TYPES.md
- [ ] ERRORS.md
- [ ] EXAMPLES.md

## Phase 24: Repo Specs

- [ ] REPO-SPEC.md exists
- [ ] Folder structure defined
- [ ] File organization clear
- [ ] Module boundaries documented

## Phase 25: Post-Spec Analysis

- [ ] GAP-ANALYSIS.md - Missing items identified and addressed
- [ ] PATTERN-ANALYSIS.md - Common patterns documented
- [ ] DEPENDENCY-ANALYSIS.md - Component dependencies mapped
- [ ] TYPE-ANALYSIS.md - Type consistency verified
- [ ] ERROR-CODE-ANALYSIS.md - Error codes are unique and complete

### Gap Analysis Quality
- [ ] All identified gaps were addressed
- [ ] No "TBD" items remaining
- [ ] Cross-references verified

## Phase 26: Project-Wide Specs

- [ ] ERROR-CODES.md - Complete error code registry
- [ ] SCHEMAS.md - Data schemas consolidated
- [ ] TYPES.md - Shared types consolidated
- [ ] API-STANDARDS.md - API conventions documented
- [ ] SECURITY-STANDARDS.md - Security requirements documented
- [ ] CONVENTIONS.md - Naming and coding conventions
- [ ] GLOSSARY.md - Project terminology defined

### Project-Wide Consistency
- [ ] Error codes are unique (no duplicates)
- [ ] Types match component definitions
- [ ] Schemas match storage specs
- [ ] Standards are achievable

## Phase 27: Hardening

### COMPLETENESS-REVIEW.md
- [ ] Review completed
- [ ] All components verified
- [ ] Completeness score recorded
- [ ] **Score ≥ 95%**

### AMBIGUITY-REVIEW.md
- [ ] Review completed
- [ ] Ambiguities identified
- [ ] All ambiguities resolved
- [ ] **0 unresolved ambiguities**

### IMPLEMENTABILITY-REVIEW.md
- [ ] Review completed
- [ ] All specs are implementable
- [ ] No impossible requirements
- [ ] **0 blocking issues**

### TEST-COVERAGE-REVIEW.md
- [ ] Review completed
- [ ] All specs have testable criteria
- [ ] Test approach is clear

### HARDENING-SUMMARY.md
- [ ] Summary complete
- [ ] Metrics documented
- [ ] Sign-off recorded

## Phase 28: Final Gate

### MASTER-CHECKLIST.md
- [ ] All items checked
- [ ] No unchecked required items
- [ ] Human approval recorded

### SPEC-MANIFEST.md
- [ ] All spec files listed
- [ ] File counts match actual files
- [ ] Locations are accurate

### IMPLEMENTATION-HANDOFF.md
- [ ] Handoff document complete
- [ ] All 0d inputs identified
- [ ] No blocking issues listed

---

## Cross-File Consistency Checks

### Types
- [ ] Types in component TYPES.md match project-wide TYPES.md
- [ ] Types in FUNCTIONS.md match TYPES.md
- [ ] No conflicting type definitions

### Error Codes
- [ ] Component ERRORS.md codes exist in ERROR-CODES.md
- [ ] Error codes are unique across all components
- [ ] Error messages are consistent

### Function Signatures
- [ ] FUNCTIONS.md signatures match CONTRACT.md from 0b
- [ ] Parameter types are defined
- [ ] Return types are defined

### Data Schemas
- [ ] STORAGE.md schemas match SCHEMAS.md
- [ ] Field names are consistent
- [ ] Data types are consistent

### Naming
- [ ] Naming conventions are followed
- [ ] Same concept uses same name everywhere
- [ ] No ambiguous abbreviations

---

## Red Flags (Automatic Critical)

If any of these are true, flag as 🔴 Critical:

- [ ] Hardening completeness below 90%
- [ ] Any unresolved ambiguities
- [ ] Any blocking implementability issues
- [ ] Missing required spec files
- [ ] MASTER-CHECKLIST has unchecked required items
- [ ] Type mismatches between components
- [ ] Error codes conflict or duplicate

---

## Consistency Delta (vs Section 0b)

Compare to 0b outputs:

- [ ] Component list matches DEPENDENCY-MAP.md
- [ ] Function signatures match CONTRACT.md
- [ ] System boundaries unchanged (or documented if changed)
- [ ] No silent changes to architecture

If changes were made:
- [ ] Changes are documented in DECISIONS.md
- [ ] Changes are justified
- [ ] Impact on other components noted

---

## Audit Exit Gate

This audit is complete when:

- [ ] All sections of this checklist reviewed
- [ ] All findings documented with severity
- [ ] Scores assigned for all categories
- [ ] Consistency Delta scored
- [ ] Verdict provided (PASS / CONDITIONAL / FAIL)
- [ ] Recommendations listed

---

*End of 0c Checklist*
