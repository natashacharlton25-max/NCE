# {{System Name}} — System Spec

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Methodology Stage: 2 of 5
Component Type: System
---

## 1. Identity

| Field | Value |
|---|---|
| **Name** | {{system}} |
| **Type** | System |
| **Worker Grouping** | own Worker / `platform` Worker |
| **Worker Name (if own)** | {{worker-name}} |
| **FileTree-v2 reference** | `FileTree-v2.md` → `{{system}}/` |

## 2. Purpose

(One paragraph stating why this system exists and what it owns.)

## 3. System Scope — What This System Owns

- …
- …

## 4. Output Form

| Output Form | Yes/No | Notes |
|---|---|---|
| JSON | | |
| Rendered artefact (PDF/DOCX/Markdown/HTML for non-Astro) | | |
| Rendered email HTML | | |
| Library entry (D1 write via `library/Writer`) | | |
| Asset reference (R2) | | |
| Metadata only | | |

## 5. Output Destination

| Destination | Form | Notes |
|---|---|---|
| Internal system: {{name}} | JSON / metadata / library / asset | |
| Astro (downstream renderer) | JSON | Web pages only |
| External provider (via integrations/) | rendered / JSON / binary | |
| Human (via review/ system) | rendered / JSON | |

## 6. Library Ownership (if applicable)

| Library | D1 Binding | Type (core / derived / generated / reference) | Write Owner Module |
|---|---|---|---|
| {{name}} | `{{BINDING_NAME}}` | | |

If none: state "None — this system does not own any D1 libraries."

## 7. Subsystem Inventory

| Subsystem | Purpose (one line) | Output Form | Storage Touch | LOC est. | Size Band |
|---|---|---|---|---|---|
| {{Subsystem}} | | JSON / rendered / library / metadata | none / D1 / R2 / KV / DO | ~{{n}} | Low <1500 / Medium 1500–2000 / High >2000 |

## 8. System-Wide Rules

Non-negotiables that apply across all subsystems of this system:
- …
- …

Examples:
- "All library access goes via `library/Librarian` (no direct D1 from this system's subsystems)."
- "No rendered web output (JSON only — Astro renders web)."
- "All external API calls go through `resilience/` patterns."

## 9. Inputs (system-level)

| Input | Source System | Binding Type | TS Type | Notes |
|---|---|---|---|---|
| | | service / D1 / R2 / KV / DO / Queue | | |

## 10. Outputs (system-level)

| Output | Destination System | Form | Binding Type | TS Type | Notes |
|---|---|---|---|---|---|
| | | JSON / rendered / library / metadata | | | |

## 11. Dependencies

| Dependency | Type | Binding | Reason |
|---|---|---|---|
| | Hard / Soft / Build / Runtime / External | service / D1 / etc. | |

### Forbidden Dependencies

- …
- (Apply output-boundary rule: e.g. JSON-emitters MUST NOT depend on `renderers/`.)

## 12. Worker Section (for own-Worker systems)

If `Worker Grouping = own Worker`:

| Field | Value |
|---|---|
| **wrangler.toml location** | `wrangler/{{worker-name}}.toml` |
| **Worker name** | `{{worker-name}}` |
| **Routes** | (list public routes if any) |
| **D1 bindings** | (binding name → database) |
| **R2 bindings** | (binding name → bucket) |
| **KV bindings** | (binding name → namespace) |
| **DO bindings** | (binding name → DO class) |
| **Service bindings** (to other Workers) | (binding name → target Worker) |
| **Worker secrets** | (`SECRET_NAME` per env) |
| **CPU budget per request** | <{{n}}ms p99 |
| **Memory budget per request** | <{{n}} MB p99 |
| **Cold-start budget** | <{{n}}ms |

If `Worker Grouping = platform`:
- This system shares the `platform` Worker with services + system + state + library.
- Worker-level rules are in `NCE-V2/specs/platform/WORKER.md` (Stage 3 output).

## 13. LOC Total Estimate

| Subsystem | Est. LOC | Size Band |
|---|---|---|
| | ~{{n}} | Low / Medium / High |

**System total runtime LOC:** ~{{n}}
**Largest expected file:** ~{{n}} LOC

(Apply TS verbosity multiplier 1.3–1.5× when porting Python-era estimates. Exclude `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments.)

## 14. Open Questions / Risks

| Item | Severity | Owner | Notes |
|---|---|---|---|
| | Low / Medium / High | | |

## 15. Hardening Self-Check (mandatory before APPROVED status)

| Pass | Check | Status |
|---|---|---|
| Completeness | All 14 sections above have content (no placeholders) | ☐ |
| Completeness | All subsystems listed in inventory | ☐ |
| Ambiguity | No vague language ("should", "might", "usually") | ☐ |
| Ambiguity | Output form unambiguous | ☐ |
| Implementability | Could a developer scaffold this system from this doc alone? | ☐ |
| Implementability | Library ownership claims align with library specs | ☐ |
| Test coverage | Per-subsystem testing intent stated | ☐ |
| Output-boundary | No boundary violations | ☐ |
| Library access | All D1 touches go via `library/Librarian` | ☐ |

If any check ☐ unchecked → revise; do not mark Status: APPROVED.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Spec author | Claude | {{date}} |
| Spec reviewer | Human | |

---
