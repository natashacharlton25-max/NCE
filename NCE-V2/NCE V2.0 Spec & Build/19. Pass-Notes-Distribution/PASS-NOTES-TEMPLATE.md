# {{Component Name}} — Pass Notes

Consolidated insights from Pass 0-4 for this component.

---

## Component Identity

| Field | Value |
|-------|-------|
| **Name** | {{name}} |
| **Type** | System / Subsystem / External Integration |
| **Parent** | {{parent or N/A}} |
| **Location** | `{{path}}` |

---

## From Pass 0 — Coverage

### Coverage Verdict
- **Status:** ✅ COVERED / ⚠️ PARTIAL / ❌ UNCOVERED
- **Risk Level:** Low / Medium / High
- **Est. LOC:** ~{{number}}

### Gaps Identified
| Gap | Impact | Notes |
|-----|--------|-------|
| {{gap or "None identified"}} | | |

### Risks Noted
| Risk | Likelihood | Impact | Notes |
|------|------------|--------|-------|
| {{risk or "None identified"}} | | | |

---

## From Pass 0d — Internal Integrations

### This Component Sends To:
| Destination | Data/Purpose | Direction |
|-------------|--------------|-----------|
| {{system or "None"}} | | One-way / Bi-directional |

### This Component Receives From:
| Source | Data/Purpose | Direction |
|--------|--------------|-----------|
| {{system or "None"}} | | One-way / Bi-directional |

---

## From Pass 3 — Dependencies

### This Component Depends On:
| Dependency | Type | Why |
|------------|------|-----|
| {{dep or "None"}} | Hard / Soft | |

### Components That Depend On This:
| Component | Why |
|-----------|-----|
| {{comp or "None"}} | |

### Shared Dependencies Used:
| Shared Dep | Type | Notes |
|------------|------|-------|
| {{dep or "None"}} | Utility / Infrastructure | |

### Dependency Notes:
- Build order position: {{n}} of {{total}}
- Can be built after: {{dependencies complete}}
- Blocks: {{what this blocks}}

---

## From Pass 4 — Implementation Plan

### Files for This Component:
| File | Purpose | Est. LOC | Risk |
|------|---------|----------|------|
| {{file}} | {{purpose}} | ~{{n}} | Low / Medium / High |

**Component Total:** ~{{n}} LOC

### Tasks for This Component:
| # | Task | Est. Hours | Dependencies |
|---|------|------------|--------------|
| {{n}} | {{task}} | {{h}} | {{deps}} |

### Implementation Notes:
- Phase: {{which implementation phase}}
- Parallel group: {{if applicable}}
- Milestone: {{which milestone}}

---

## Relevant Decisions

(From PASS-DECISION-NOTES.md)

| Date | Decision | Impact on This Component |
|------|----------|--------------------------|
| {{date or "None"}} | | |

---

## Open Questions

(Unresolved items that affect this component)

| Question | Source | Impact |
|----------|--------|--------|
| {{question or "None"}} | Pass {{n}} | |

---

## Summary for Full Specs

**Key Context:**
- {{1-2 sentence summary of what Full Specs needs to know}}

**Watch Out For:**
- {{any specific concerns or constraints}}

**Ready for Full Specs:** Yes / No — {{reason if no}}

---
Generated: {{timestamp}}
Source: Pass 0-4 Distribution (Phase 19)
---
