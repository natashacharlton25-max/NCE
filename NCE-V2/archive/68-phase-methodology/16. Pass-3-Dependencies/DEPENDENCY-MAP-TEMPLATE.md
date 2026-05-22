# Dependency Map

## Project
NCE-V2 (TypeScript on Cloudflare Workers)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Worker Topology** | `platform` Worker (services/system/state/library) + 23 per-system Workers |
| **Output-Boundary Audit Complete?** | Yes / No |
| **Library Access Audit Complete?** | Yes / No |
| **No Circular Dependencies?** | Yes / Cycles found — see below |

---

## Components

### Systems

| # | System | Dependencies | Binding Type | Type |
|---|--------|--------------|--------------|------|
| 1 | {{system}} | {{deps or None}} | service / D1 / R2 / KV / DO / Queue / in-Worker | Hard / Soft |

### Subsystems

| # | Parent | Subsystem | Dependencies | Binding Type | Type |
|---|--------|-----------|--------------|--------------|------|
| 1 | {{system}} | {{subsystem}} | {{deps}} | | Hard / Soft |

### External Integrations

| # | Provider | Service | Dependencies | Direction | Type |
|---|----------|---------|--------------|-----------|------|
| 1 | {{provider}} | {{service}} | {{deps}} | Inbound / Outbound / Renderer | External |

---

## Dependency Graph

```
(Visual representation — ASCII)
```

---

## Dependency Matrix

|  | System A | System B | System C | integrations |
|--|----------|----------|----------|--------------|
| **System A** | — | | | |
| **System B** | ✓ | — | ✓ | ✓ |

(✓ = row depends on column)

---

## Edge Type Breakdown

| Edge | Source → Destination | Binding Type | Dependency Type | Form Crossing |
|------|----------------------|--------------|------------------|---------------|
| 1 | {{src}} → {{dest}} | service / D1 / R2 / KV / DO / Queue / in-Worker | Hard / Soft / Build / Runtime / External | JSON / rendered / library entry / metadata / binary |

---

## Cycle Analysis

### Circular Dependencies Found

| Cycle | Components | Severity | Resolution |
|-------|------------|----------|------------|
| {{or None}} | | Critical / Major / Minor | Restart at Pass 1 / Pass 2 per PASS-RESTART-RULES.md |

### Forbidden Dependencies Violated

| Component | Forbidden | Actual | Resolution |
|-----------|-----------|--------|------------|
| {{or None}} | | | |

### Output-Boundary Violations

| Source → Destination | Issue | Resolution |
|----------------------|-------|------------|
| {{or None}} | e.g. JSON-emitter passing rendered HTML | Flag for grounding revision |

### Library Access Bypasses

| Source → Destination | Issue | Resolution |
|----------------------|-------|------------|
| {{or None}} | Direct D1 binding from outside `library/` | Re-route through `library/Librarian` |

---

## Implementation Order

### Leaf Nodes (Build First)
(Components with no dependencies — can be built immediately. `platform` Worker constituents typically here.)

1. {{component}}
2. {{component}}

### Build Order

| Order | Component | Worker | Depends On | Can Start After |
|-------|-----------|--------|------------|-----------------|
| 1 | {{component}} | platform | None | Immediately |
| 2 | {{component}} | {{own}} | {{deps}} | Order 1 complete |

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

| Provider | Service | Direction | Required By | Setup Status |
|----------|---------|-----------|-------------|--------------|
| {{provider}} | {{service}} | In / Out / Renderer | {{components}} | Configured / Setup Required |

---

## Shared Dependencies

(Dependencies used by 2+ components — candidates for `lib/` utilities)

| Dependency | Used By (Count) | Components | Type | Should be in lib/? |
|------------|-----------------|------------|------|---------------------|
| {{dependency}} | {{n}} | {{list}} | Utility / Infrastructure / Integration | Yes / No |

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
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 16 (Pass 3)
---
