# Dependency Map

## Project
{{Project Name}}

---

## Components

### Systems

| # | System | Dependencies | Type |
|---|--------|--------------|------|
| 1 | {{system}} | {{deps or None}} | Hard / Soft |

### Subsystems

| # | Parent | Subsystem | Dependencies | Type |
|---|--------|-----------|--------------|------|
| 1 | {{system}} | {{subsystem}} | {{deps}} | Hard / Soft |

### External Integrations

| # | Provider | Service | Dependencies | Type |
|---|----------|---------|--------------|------|
| 1 | {{provider}} | {{service}} | {{deps}} | External |

---

## Dependency Graph

```
(Visual representation — ASCII or description)

Example:
┌─────────────┐
│  System A   │
└──────┬──────┘
       │
       ▼
┌─────────────┐     ┌─────────────┐
│  System B   │────▶│  System C   │
└──────┬──────┘     └─────────────┘
       │
       ▼
┌─────────────┐
│ integration │
│   google    │
└─────────────┘
```

---

## Dependency Matrix

|  | System A | System B | System C | integration-google |
|--|----------|----------|----------|-------------------|
| **System A** | — | | | |
| **System B** | ✓ | — | ✓ | ✓ |
| **System C** | | | — | |
| **integration-google** | | | | — |

(✓ = row depends on column)

---

## Cycle Analysis

### Circular Dependencies Found

| Cycle | Components | Severity | Resolution |
|-------|------------|----------|------------|
| {{or None}} | | Critical / Major / Minor | |

### Forbidden Dependencies Violated

| Component | Forbidden | Actual | Resolution |
|-----------|-----------|--------|------------|
| {{or None}} | | | |

---

## Implementation Order

### Leaf Nodes (Build First)
(Components with no dependencies — can be built immediately)

1. {{component}}
2. {{component}}

### Build Order

| Order | Component | Depends On | Can Start After |
|-------|-----------|------------|-----------------|
| 1 | {{component}} | None | Immediately |
| 2 | {{component}} | {{deps}} | Order 1 complete |
| 3 | {{component}} | {{deps}} | Order 2 complete |

### Parallel Groups
(Components that can be built simultaneously)

| Group | Components | Can Start After |
|-------|------------|-----------------|
| 1 | {{a}}, {{b}} | Immediately |
| 2 | {{c}}, {{d}} | Group 1 complete |

---

## Critical Path

The longest dependency chain that determines minimum build time:

```
{{component}} → {{component}} → {{component}} → {{component}}
```

**Critical Path Length:** {{n}} components

---

## External Dependencies

| Provider | Service | Required By | Availability |
|----------|---------|-------------|--------------|
| {{provider}} | {{service}} | {{components}} | Available / Setup Required |

---

## Shared Dependencies

(Dependencies used by 2+ components — candidates for shared libraries)

### Shared Dependency Summary

| Dependency | Used By (Count) | Components | Type |
|------------|-----------------|------------|------|
| {{dependency}} | {{n}} | {{list}} | Utility / Infrastructure / Integration |

### Shared Dependency Details

| Dependency | Components | Pattern | Notes |
|------------|------------|---------|-------|
| Validation | System A, B, C | Same validation logic | Candidate for /libs/ |
| Logging | All | Universal | Infrastructure |
| Config | All | Universal | Infrastructure |
| Error Handling | System A, B | Common error patterns | Review in 0d |
| {{other}} | {{list}} | {{pattern}} | {{notes}} |

### Analysis Notes

(Patterns observed, recommendations for 0d Shared Dependency Analysis)

- 
- 

---

## Risks

| Risk | Impact | Mitigation |
|------|--------|------------|
| {{risk}} | High / Medium / Low | {{mitigation}} |

---

## Notes
(Clarifications, assumptions, or context.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 16 (Pass 3)
---
