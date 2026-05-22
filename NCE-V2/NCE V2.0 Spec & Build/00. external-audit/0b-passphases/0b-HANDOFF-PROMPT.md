# 0b Audit Handoff Prompt

---
Section: 0b PassPhases 0-4 (Phases 13-19)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 19, before external audit (RECOMMENDED)
---

## Instructions for Claude

You have completed Section 0b (PassPhases 0-4). Prepare a handoff package for external audit review.

**Note:** This audit is RECOMMENDED. Architecture decisions made here are difficult to change later.

---

## Create: 0b-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- What was accomplished in the pass phases
- Key architectural decisions made
- Overall confidence in the component structure

### 2. Self-Assessment Scores

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | All passes complete? |
| Consistency | /5 | Contracts align? |
| Accuracy | /5 | Architecture sound? |
| Methodology | /5 | Pass rules followed? |
| Quality | /5 | Documentation clear? |
| Readiness | /5 | Ready for Full Specs? |

### 3. Concerns & Uncertainties

- Areas where contracts might need refinement
- Dependencies that seem complex
- Decisions made with incomplete information

### 4. Pass Summary

**Pass 0 (Coverage):**
- Systems reviewed: {{count}}
- Subsystems reviewed: {{count}}
- Integrations reviewed: {{count}}
- Coverage issues found/resolved: {{count}}

**Pass 1 (Grounding):**
- SYSTEM.md files created: {{count}}
- ADMIN.md files created: {{count}}

**Pass 2 (Contracts):**
- CONTRACT.md files created: {{count}}
- Interfaces defined: {{count}}

**Pass 3 (Dependencies):**
- DEPENDENCY-MAP.md created: Yes/No
- Circular dependencies found: {{count}}
- Build order defined: Yes/No

**Pass 4 (Implementation Planning):**
- File estimates: {{total files}}
- LOC estimates: {{total LOC}}
- Task breakdown complete: Yes/No

### 5. Key Decisions Made

| Decision | Pass | Rationale | Impact |
|----------|------|-----------|--------|
| | | | |

### 6. Contract Summary

| Component | Contract With | Interface Type |
|-----------|---------------|----------------|
| | | |

### 7. Dependency Map Summary

```
[Paste simplified dependency diagram or list]
```

Build Order:
1. {{component}}
2. {{component}}
...

### 8. File Manifest

**Pass 0:**
| File | Status |
|------|--------|
| PASS-0-COVERAGE.md | |

**Pass 1 (per component):**
| Component | SYSTEM.md | ADMIN.md |
|-----------|-----------|----------|
| | | |

**Pass 2 (per component):**
| Component | CONTRACT.md |
|-----------|-------------|
| | |

**Pass 3:**
| File | Status |
|------|--------|
| DEPENDENCY-MAP.md | |

**Pass 4 (per component):**
| Component | IMPLEMENTATION-PLAN.md |
|-----------|------------------------|
| | |

**Gate:**
| File | Status |
|------|--------|
| POST-PASS-CHECKLIST.md | |
| PASS-NOTES.md (per component) | |

### 9. Dependency Bundles

| Bundle | Primary File | Related Files |
|--------|--------------|---------------|
| Pass Progress | PASS-PROGRESS.md | PASS-DECISION-NOTES.md |
| Contracts | All CONTRACT.md | DEPENDENCY-MAP.md |
| Dependencies | DEPENDENCY-MAP.md | All CONTRACT.md |
| Gate | POST-PASS-CHECKLIST.md | All PASS-NOTES.md |

### 10. Recommended Files for Review

1. **POST-PASS-CHECKLIST.md** - Completion status
2. **PASS-DECISION-NOTES.md** - Key decisions
3. **DEPENDENCY-MAP.md** - Architecture structure
4. **{{critical component}}/CONTRACT.md** - Key interface
5. **{{critical component}}/CONTRACT.md** - Another key interface

---

## Pre-Handoff Verification

- [ ] All 5 passes complete (0-4)
- [ ] All CONTRACT.md files created
- [ ] DEPENDENCY-MAP.md complete
- [ ] No circular dependencies
- [ ] POST-PASS-CHECKLIST.md complete
- [ ] PASS-NOTES.md distributed to each component

---

## Output Location

Save as: `0b-AUDIT-HANDOFF.md` in `00. external-audit/0b-passphases/`

---

*End of Handoff Prompt*
