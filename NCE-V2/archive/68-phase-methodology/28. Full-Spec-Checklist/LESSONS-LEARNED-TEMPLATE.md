# Lessons Learned

---
Project: {{project_name}}
Version: {{version}}
Date: {{timestamp}}
---

## Purpose

Document what worked, what didn't, and improvements for future specification projects.

---

## Project Summary

| Metric | Value |
|--------|-------|
| Project Duration | {{start}} to {{end}} |
| Total Spec Files | {{n}} |
| Total Components | {{n}} |
| Major Revisions | {{n}} |

---

## What Worked Well

### Process Successes

| Area | What Worked | Impact |
|------|-------------|--------|
| **Phase Structure** | Breaking into small phases | Manageable chunks, clear progress |
| **Templates** | Standardized templates | Consistency, faster writing |
| **Coverage Reviews** | Pre-spec analysis | Caught gaps early |
| **Hardening** | Final quality pass | Reduced ambiguity |
| {{area}} | {{what}} | {{impact}} |

### Documentation Successes

| Area | What Worked | Impact |
|------|-------------|--------|
| **EXAMPLES.md** | Concrete code examples | Clear implementation guidance |
| **ERRORS.md** | Complete error mapping | No guessing during implementation |
| **INDEX.md** | Quick reference per component | Easy navigation |
| {{area}} | {{what}} | {{impact}} |

---

## What Was Challenging

### Process Challenges

| Challenge | Impact | How We Handled | Future Improvement |
|-----------|--------|----------------|-------------------|
| Context limits | Couldn't see all specs at once | Used INDEX files | Better summarization |
| Spec drift | Later specs inconsistent | Hardening pass caught it | Earlier cross-checks |
| Scope creep | New requirements during spec | Flagged for future | Stricter boundaries |
| {{challenge}} | {{impact}} | {{handling}} | {{improvement}} |

### Technical Challenges

| Challenge | Impact | How We Handled | Future Improvement |
|-----------|--------|----------------|-------------------|
| Circular dependencies | Blocked impl order | Introduced library layer | Earlier dep analysis |
| External API complexity | More specs needed | Split into services | Provider/service hierarchy |
| {{challenge}} | {{impact}} | {{handling}} | {{improvement}} |

---

## Time Analysis

### Estimated vs Actual

| Phase | Estimated | Actual | Variance |
|-------|-----------|--------|----------|
| Phase 20 | {{time}} | {{time}} | {{%}} |
| Phase 21 | {{time}} | {{time}} | {{%}} |
| Phase 22 | {{time}} | {{time}} | {{%}} |
| Phase 23-28 | {{time}} | {{time}} | {{%}} |
| **Total** | {{time}} | {{time}} | {{%}} |

---

## Process Improvements

### Recommended Changes

| Change | Rationale | Priority |
|--------|-----------|----------|
| Add frontend component specs phase | Missing coverage for UI | High |
| Add database schema phase | Storage specs insufficient | High |
| Add API route specs | Endpoint specs missing | Medium |
| Earlier provider/service split | Caught late in process | Medium |

### Template Updates Needed

| Template | Change Needed | Rationale |
|----------|---------------|-----------|
| PHASE-22-PROMPT.md | Add provider/service hierarchy | Multi-service providers |
| {{template}} | {{change}} | {{rationale}} |

### New Templates Needed

| Template | Purpose | Priority |
|----------|---------|----------|
| FRONTEND-COMPONENT-TEMPLATE.md | UI component specs | High |
| DATABASE-SCHEMA-TEMPLATE.md | Full schema design | High |
| API-ROUTES-TEMPLATE.md | Endpoint specs | Medium |
| ENVIRONMENT-TEMPLATE.md | Env config per stage | Medium |

---

## Knowledge Captured

### Key Insights

| Insight | Context |
|---------|---------|
| Provider = System, Service = Subsystem | Integration hierarchy |
| ~1500 LOC constraint drives good decomposition | Size limits |
| Error codes must be globally unique | Debugging |

### Patterns Discovered

| Pattern | Description | Reuse |
|---------|-------------|-------|
| Result<T> wrapper | Standard success/error return | All functions |
| ListResult<T> | Paginated list returns | All list functions |
| Provider SYSTEM-NOTES | Shared config for services | All integrations |

### Anti-Patterns Identified

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Generic error messages | Hard to debug | Specific error codes |
| Implicit dependencies | Circular deps | Explicit in OVERVIEW |
| Validation in multiple places | Inconsistent | Centralize in libraries |

---

## Recommendations for Next Project

### Before Starting

1. [ ] Identify all external providers upfront
2. [ ] Map provider → services hierarchy early
3. [ ] Decide on frontend/backend split
4. [ ] Define shared types first

### During Specification

1. [ ] Run dependency analysis after Phase 21
2. [ ] Cross-check types every 2 phases
3. [ ] Keep INDEX.md updated per component

### Before Implementation

1. [ ] Run full hardening pass
2. [ ] Verify all error codes registered
3. [ ] Confirm implementation order

---

## Notes

{{Any additional observations or notes}}

---
Phase: 28 (Full Spec Checklist)
Generated: {{timestamp}}
---
