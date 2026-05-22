# Master Full Specs Guide — Phases 20-28

---
Version: v1.0.0
Section: 0c. Full Specs
Purpose: Complete workflow guide for the Full Specs phase
---

## Overview

The Full Specs phase transforms Pass phase outputs into implementation-ready specifications.

**Input:** PASS-NOTES.md per component (from Phase 19)
**Output:** Complete spec files for every component

---

## Prerequisites

Before starting Full Specs, verify:

- [ ] Phase 18 (Post-Pass Checklist) is APPROVED
- [ ] Phase 19 (Pass Notes Distribution) is COMPLETE
- [ ] Every component has PASS-NOTES.md in its folder
- [ ] No blocking issues from Pass phases

---

## Phase Summary

| Phase | Name | Output | Per Component? |
|-------|------|--------|----------------|
| 20 | Pre-Spec Notes | PRE-SPEC-NOTES.md | Yes |
| 21 | Internal Component Specs | 10 spec files | Yes (systems/subsystems) |
| 22 | External Integration Specs | 10+ spec files | Yes (integrations) |
| 23 | Library Specs | 6+ spec files | Yes (libraries) |
| 24 | Repo Specs | REPO-SPEC.md | No (project-wide) |
| 25 | Post-Spec Analysis | 5 analysis files | No (project-wide) |
| 26 | Project-Wide Specs | 6+ standard files | No (project-wide) |
| 27 | Hardening | 6 review files | No (project-wide) |
| 28 | Full-Spec Checklist | Final verification | No (project-wide) |

---

## Phase 20: Pre-Spec Notes

**Purpose:** Structure the PASS-NOTES.md content into a format ready for spec writing.

**Input:** 
- `{{component}}/PASS-NOTES.md`

**Output:** 
- `{{component}}/PRE-SPEC-NOTES.md`

**Process:**
1. Read PASS-NOTES.md
2. Fill in all 12 sections of PRE-SPEC-NOTES template
3. Identify gaps, questions, concerns
4. Get approval

**Files:**
- `20. Pre-Spec-Notes/PHASE-20-PROMPT.md`
- `20. Pre-Spec-Notes/PRE-SPEC-NOTES-TEMPLATE.md`
- `20. Pre-Spec-Notes/PRE-SPEC-NOTES-EXTERNAL-TEMPLATE.md` (for integrations)
- `20. Pre-Spec-Notes/PRE-SPEC-NOTES-CHECKLIST.md`

**Order:** Systems (A-Z) → their subsystems → external integrations

---

## Phase 21: Internal Component Specs

**Purpose:** Create complete specification files for each system and subsystem.

**Input:**
- `{{component}}/PRE-SPEC-NOTES.md`
- `{{component}}/CONTRACT.md`
- `{{component}}/SYSTEM.md` or `SUBSYSTEM.md`

**Output:** 10 files in `{{component}}/spec/`

| File | Purpose |
|------|---------|
| INDEX.md | Quick reference, status, navigation |
| OVERVIEW.md | Identity, scope, boundaries, guarantees |
| FUNCTIONS.md | Public API with full signatures |
| TYPES.md | All data structures and schemas |
| BEHAVIOUR.md | Process flows, state machines |
| ERRORS.md | Error codes, categories, recovery |
| CONFIG.md | Configuration options |
| STORAGE.md | Database schema, queries (or N/A) |
| DECISIONS.md | Design rationale |
| EXAMPLES.md | Full request/response examples |

**Process:**
1. Create `spec/` folder in component directory
2. Create INDEX.md first (sets context)
3. Create remaining files using templates
4. Get approval before moving to next component

**Files:**
- `21. Internal-Component-Specs/PHASE-21-PROMPT.md`
- `21. Internal-Component-Specs/INDEX-TEMPLATE.md`
- `21. Internal-Component-Specs/OVERVIEW-TEMPLATE.md`
- `21. Internal-Component-Specs/FUNCTIONS-TEMPLATE.md`
- `21. Internal-Component-Specs/TYPES-TEMPLATE.md`
- `21. Internal-Component-Specs/BEHAVIOUR-TEMPLATE.md`
- `21. Internal-Component-Specs/ERRORS-TEMPLATE.md`
- `21. Internal-Component-Specs/CONFIG-TEMPLATE.md`
- `21. Internal-Component-Specs/STORAGE-TEMPLATE.md`
- `21. Internal-Component-Specs/DECISIONS-TEMPLATE.md`
- `21. Internal-Component-Specs/EXAMPLES-TEMPLATE.md`

**Order:** 
```
System-A/spec/ → System-A/subsystem-1/spec/ → System-A/subsystem-2/spec/
System-B/spec/ → System-B/subsystem-1/spec/ → ...
```

---

## Phase 22: External Integration Specs

**Purpose:** Create specifications for each external service integration.

**Input:**
- `integration-{{provider}}/{{service}}/PRE-SPEC-NOTES.md`
- `integration-{{provider}}/{{service}}/EXTERNAL-INTEGRATION.md`

**Output:** 10+ files in `integration-{{provider}}/{{service}}/spec/`

| File | Purpose |
|------|---------|
| INDEX-EXTERNAL.md | Quick reference for integration |
| OVERVIEW-EXTERNAL.md | What this integration does |
| PROVIDER.md | Vendor information, SLAs |
| API-SURFACE.md | External API we consume |
| OUR-WRAPPER.md | Our abstraction layer |
| FIELD-MAPPING.md | External ↔ internal field mapping |
| CONSTRAINTS.md | Rate limits, quotas, restrictions |
| FAILURE-MODES.md | What can go wrong, how to handle |
| ERRORS-EXTERNAL.md | Error codes from provider |
| EXAMPLES.md | Request/response examples |

**Files:**
- `22. External-Integration-Specs/PHASE-22-PROMPT.md`
- `22. External-Integration-Specs/*-TEMPLATE.md` (11 templates)

**Order:** Providers (A-Z) → their services (A-Z)

---

## Phase 23: Library Specs

**Purpose:** Spec any shared libraries identified during Pass 3 (Dependencies).

**Input:**
- Shared dependencies from DEPENDENCY-MAP.md
- Patterns identified across components

**Output:** 6+ files in `lib-{{library}}/spec/`

| File | Purpose |
|------|---------|
| INDEX-LIBRARY.md | Quick reference |
| OVERVIEW-LIBRARY.md | What this library provides |
| FUNCTIONS-LIBRARY.md | Public API |
| TYPES.md | Shared types |
| ERRORS.md | Error handling |
| EXAMPLES.md | Usage examples |

**Note:** Libraries emerge from analysis. If no shared libraries were identified, this phase may be N/A.

**Files:**
- `23. Library-Specs/PHASE-23-PROMPT.md`
- `23. Library-Specs/*-TEMPLATE.md` (7 templates)

---

## Phase 24: Repo Specs

**Purpose:** Define the repository structure for implementation.

**Input:**
- All component specs completed
- IMPLEMENTATION-PLAN.md from Pass 4

**Output:** `{{project}}/REPO-SPEC.md`

**Contains:**
- Folder structure
- File naming conventions
- Module organization
- Build configuration locations

**Files:**
- `24. Repo-Specs/PHASE-24-PROMPT.md`
- `24. Repo-Specs/REPO-SPEC-TEMPLATE.md`

---

## Phase 25: Post-Spec Analysis

**Purpose:** Analyze completed specs for gaps, patterns, and issues.

**Input:** All spec files from phases 21-24

**Output:** 5 analysis files in `{{project}}/analysis/`

| File | Purpose |
|------|---------|
| GAP-ANALYSIS.md | Missing specs, incomplete sections |
| DEPENDENCY-ANALYSIS.md | Cross-component dependencies |
| PATTERN-ANALYSIS.md | Repeated patterns to extract |
| ERROR-CODE-ANALYSIS.md | Error code conflicts, gaps |
| TYPE-ANALYSIS.md | Type inconsistencies |

**Process:**
1. Review all specs systematically
2. Document findings
3. Create issues for gaps
4. Recommend resolutions

**Files:**
- `25. Post-Spec-Analysis/PHASE-25-PROMPT.md`
- `25. Post-Spec-Analysis/*-TEMPLATE.md` (5 templates)

---

## Phase 26: Project-Wide Specs

**Purpose:** Create project-wide standards and shared definitions.

**Input:**
- Analysis from Phase 25
- All component specs

**Output:** 6+ files in `{{project}}/standards/`

| File | Purpose |
|------|---------|
| GLOSSARY.md | Project terminology |
| SCHEMAS.md | Shared data schemas |
| ERROR-CODES.md | Master error code registry |
| API-STANDARDS.md | API conventions |
| SECURITY-STANDARDS.md | Security requirements |
| CONVENTIONS.md | Naming, formatting conventions |

**Files:**
- `26. Project-Wide-Specs/PHASE-26-PROMPT.md`
- `26. Project-Wide-Specs/*-TEMPLATE.md` (7 templates)

---

## Phase 27: Hardening

**Purpose:** Review all specs for completeness and implementability.

**Input:** All specs and standards from phases 20-26

**Output:** 6 review files in `{{project}}/hardening/`

| File | Purpose |
|------|---------|
| AMBIGUITY-REVIEW.md | Unclear or vague specs |
| COMPLETENESS-REVIEW.md | Missing information |
| TEST-COVERAGE-REVIEW.md | Testability assessment |
| IMPLEMENTABILITY-REVIEW.md | Can this be built? |
| IMPLEMENTATION-READY.md | Component readiness matrix |
| HARDENING-SUMMARY.md | Overall findings |

**Process:**
1. Systematic review of each component
2. Document issues found
3. Resolve or defer with reason
4. Sign off on readiness

**Files:**
- `27. Hardening/PHASE-27-PROMPT.md`
- `27. Hardening/*-TEMPLATE.md` (6 templates)

---

## Phase 28: Full-Spec Checklist

**Purpose:** Final verification before moving to PreCode.

**Input:** All outputs from phases 20-27

**Output:** 5 files in `{{project}}/admin/`

| File | Purpose |
|------|---------|
| SPEC-MANIFEST.md | Complete list of all spec files |
| MASTER-CHECKLIST.md | Verification checklist |
| PROGRESS-TRACKER.md | Phase completion status |
| IMPLEMENTATION-HANDOFF.md | What implementers need to know |
| LESSONS-LEARNED.md | Notes for future projects |

**Checklist includes:**
- [ ] All systems have complete specs
- [ ] All subsystems have complete specs
- [ ] All integrations have complete specs
- [ ] All libraries have complete specs
- [ ] Repo structure defined
- [ ] Analysis complete, issues resolved
- [ ] Standards documented
- [ ] Hardening reviews passed

**Files:**
- `28. Full-Spec-Checklist/PHASE-28-PROMPT.md`
- `28. Full-Spec-Checklist/*-TEMPLATE.md` (5 templates)

---

## Folder Structure

After Full Specs is complete:

```
{{project}}/
│
├── 0. Admin/
│   └── 0c. Full Specs/
│       ├── Claude-fullspecs.md
│       └── Master-FullSpecs-Guide 20to28.md
│
├── {{system}}/
│   ├── PASS-NOTES.md
│   ├── SYSTEM.md
│   ├── CONTRACT.md
│   ├── ADMIN.md
│   ├── PRE-SPEC-NOTES.md          ← Phase 20
│   └── spec/                       ← Phase 21
│       ├── INDEX.md
│       ├── OVERVIEW.md
│       ├── FUNCTIONS.md
│       ├── TYPES.md
│       ├── BEHAVIOUR.md
│       ├── ERRORS.md
│       ├── CONFIG.md
│       ├── STORAGE.md
│       ├── DECISIONS.md
│       └── EXAMPLES.md
│
├── {{system}}/{{subsystem}}/
│   ├── PASS-NOTES.md
│   ├── SUBSYSTEM.md
│   ├── CONTRACT.md
│   ├── ADMIN.md
│   ├── PRE-SPEC-NOTES.md
│   └── spec/
│       └── (same 10 files)
│
├── integration-{{provider}}/{{service}}/
│   ├── PASS-NOTES.md
│   ├── EXTERNAL-INTEGRATION.md
│   ├── PRE-SPEC-NOTES.md
│   └── spec/                       ← Phase 22
│       └── (10+ external spec files)
│
├── lib-{{library}}/                ← Phase 23 (if applicable)
│   └── spec/
│       └── (6+ library spec files)
│
├── REPO-SPEC.md                    ← Phase 24
│
├── analysis/                       ← Phase 25
│   ├── GAP-ANALYSIS.md
│   ├── DEPENDENCY-ANALYSIS.md
│   ├── PATTERN-ANALYSIS.md
│   ├── ERROR-CODE-ANALYSIS.md
│   └── TYPE-ANALYSIS.md
│
├── standards/                      ← Phase 26
│   ├── GLOSSARY.md
│   ├── SCHEMAS.md
│   ├── ERROR-CODES.md
│   ├── API-STANDARDS.md
│   ├── SECURITY-STANDARDS.md
│   └── CONVENTIONS.md
│
├── hardening/                      ← Phase 27
│   ├── AMBIGUITY-REVIEW.md
│   ├── COMPLETENESS-REVIEW.md
│   ├── TEST-COVERAGE-REVIEW.md
│   ├── IMPLEMENTABILITY-REVIEW.md
│   ├── IMPLEMENTATION-READY.md
│   └── HARDENING-SUMMARY.md
│
└── admin/                          ← Phase 28
    ├── SPEC-MANIFEST.md
    ├── MASTER-CHECKLIST.md
    ├── PROGRESS-TRACKER.md
    ├── IMPLEMENTATION-HANDOFF.md
    └── LESSONS-LEARNED.md
```

---

## Completion Criteria

0c Full Specs is COMPLETE when:

1. **Phase 28 checklist passed**
2. **All components speced:**
   - Every system has `spec/` folder with 10 files
   - Every subsystem has `spec/` folder with 10 files
   - Every integration has `spec/` folder with 10+ files
   - Every library has `spec/` folder with 6+ files
3. **Project-wide docs complete:**
   - Repo structure defined
   - Standards documented
   - Analysis complete
4. **Hardening passed:**
   - No critical issues unresolved
   - All components marked implementation-ready
5. **User approval received**

---

## Next Phase

After 0c Full Specs → **0d. PreCode**

0d will:
- Consolidate database schema from all STORAGE.md files
- Consolidate API surface from all FUNCTIONS.md files
- Set up repo structure
- Configure tech stack
- Establish coding standards
- Verify ready for implementation

---

## Quick Reference

| Need | Go To |
|------|-------|
| Claude instructions | Claude-fullspecs.md |
| Phase 20 prompt | `20. Pre-Spec-Notes/PHASE-20-PROMPT.md` |
| Phase 21 templates | `21. Internal-Component-Specs/*.md` |
| Phase 22 templates | `22. External-Integration-Specs/*.md` |
| Error if issue found | Add to INDEX.md "Build Notes" or STOP if major |

---
