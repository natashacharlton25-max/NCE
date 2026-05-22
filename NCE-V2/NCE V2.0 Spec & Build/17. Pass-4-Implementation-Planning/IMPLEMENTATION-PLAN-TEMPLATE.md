# Implementation Plan

## Project
{{Project Name}}

---

## Summary

| Metric | Value |
|--------|-------|
| Total Components | {{n}} |
| Total Files | {{n}} |
| Total Estimated LOC | ~{{n}} |
| Total Tasks | {{n}} |
| Estimated Duration | {{time}} |

---

## File Structure

### {{System Name}}

| File | Purpose | Est. LOC | Risk |
|------|---------|----------|------|
| `{{filename}}` | {{purpose}} | ~{{n}} | Low / Medium / High |
| `{{filename}}` | {{purpose}} | ~{{n}} | Low / Medium / High |

**System Total:** ~{{n}} LOC

#### {{Subsystem Name}}

| File | Purpose | Est. LOC | Risk |
|------|---------|----------|------|
| `{{filename}}` | {{purpose}} | ~{{n}} | Low / Medium / High |

**Subsystem Total:** ~{{n}} LOC

---

### integration-{{Provider}}

#### {{Service Name}}

| File | Purpose | Est. LOC | Risk |
|------|---------|----------|------|
| `{{filename}}` | {{purpose}} | ~{{n}} | Low / Medium / High |

**Service Total:** ~{{n}} LOC

---

## Size Verification

| Component | Est. LOC | Status | Action |
|-----------|----------|--------|--------|
| {{component}} | ~{{n}} | ✅ OK / ⚠️ Monitor / ❌ Over | {{action if needed}} |

### Components Requiring Attention

| Component | Est. LOC | Issue | Resolution |
|-----------|----------|-------|------------|
| {{or None}} | | | |

---

## Task Breakdown

### Phase 1: Foundation
(Tasks with no dependencies)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| 1 | {{task}} | {{component}} | {{n}} | None |
| 2 | {{task}} | {{component}} | {{n}} | None |

### Phase 2: Core
(Tasks depending on Phase 1)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| 3 | {{task}} | {{component}} | {{n}} | Task 1, 2 |
| 4 | {{task}} | {{component}} | {{n}} | Task 1 |

### Phase 3: Integration
(Tasks depending on Phase 2)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| 5 | {{task}} | {{component}} | {{n}} | Task 3, 4 |

### Phase 4: Polish
(Final tasks)

| # | Task | Component | Est. Hours | Dependencies |
|---|------|-----------|------------|--------------|
| 6 | {{task}} | {{component}} | {{n}} | Task 5 |

---

## Task Sequence

```
(Visual task sequence)

[Task 1] ──┬──▶ [Task 3] ──┬──▶ [Task 5] ──▶ [Task 6]
           │               │
[Task 2] ──┘    [Task 4] ──┘
```

---

## Parallel Streams

| Stream | Tasks | Owner |
|--------|-------|-------|
| Stream A | 1, 3, 5 | |
| Stream B | 2, 4 | |

---

## Milestones

| Milestone | Tasks Complete | Deliverable | Target |
|-----------|----------------|-------------|--------|
| M1: Foundation | 1, 2 | Core components exist | |
| M2: Core Complete | 3, 4 | Main functionality works | |
| M3: Integration | 5 | External services connected | |
| M4: Ready | 6 | Ready for testing | |

---

## Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| {{risk}} | Low / Medium / High | Low / Medium / High | {{mitigation}} |

---

## Assumptions

- {{assumption}}
- {{assumption}}

---

## Open Items

| Item | Owner | Due |
|------|-------|-----|
| {{item}} | | |

---

## Notes
(Clarifications, context, or considerations.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 17 (Pass 4)
---
