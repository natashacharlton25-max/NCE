# System Coverage Review — versioning

## System Role Summary

The versioning system tracks changes, manages versions, and maintains audit trails for a **specific, bounded set of artefacts**: templates, prompts, and rules. It provides controlled evolution (version graphs, compatibility, rollback via VersionManager) and human-readable change history with attribution (ChangeLog). Its stated scope is deliberately narrow — it is **not** a global version-control authority over every domain object in the platform.

---

## Coverage Assessment

### Covered Responsibilities

| Responsibility | Covered By |
|---|---|
| Version graphs / version storage for templates, prompts, rules | VersionManager |
| Rollback of templates, prompts, rules to a prior version | VersionManager |
| Compatibility rules between versions | VersionManager |
| Human-readable history of *why* changes happened | ChangeLog |
| Attribution of changes (who changed what) | ChangeLog |
| Change narratives supporting audits | ChangeLog |

### Partially Covered Responsibilities

- **"Maintain audit trails" (from the system Intent).** ChangeLog records change narratives + attribution, but the Intent verb "audit trail" overlaps the dedicated `audit/` system (DecisionTimeline, HumanOverrideLedger, AIUsageAttribution). It is unclear whether versioning's ChangeLog *is* the audit trail for template/prompt/rule changes, or merely *feeds* the audit system. The line between "change history" (versioning) and "decision/audit trail" (audit) is not drawn in any locked artefact.
- **Diff / version comparison.** Rollback implies the ability to compute and present differences between versions, but no responsibility for diffing is stated for VersionManager (sibling per-domain managers such as TemplateVersionManager explicitly list "version comparison"; versioning/VersionManager does not). Likely-implied, not stated.
- **Retention / pruning of old versions.** Version graphs grow unbounded; whether versioning prunes, or delegates lifecycle to `system/GarbageCollector` / `system/Archivist`, is unspecified.

### Uncovered Responsibilities

- **Central / cross-domain versioning authority.** The boundary hint asks whether versioning is "versioning across the whole system." It is **not** — and nothing in versioning claims to be. There is no subsystem that registers, indexes, or governs the per-domain version managers (brand, document-templates, marks, template). If a single source of truth for "what is versioned and how" is wanted, it is currently uncovered. (Flagged as a boundary decision below, not an automatic gap — the narrow scope may be intentional.)
- **Schema / data migration versioning.** Owned elsewhere (`orchestration/MigrationHandler` — "data migrations with versioning, rollback support"). Correctly out of scope for versioning, noted to confirm the boundary holds.

---

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|---|---|---|---|---|
| ChangeLog | Sufficient (for its narrow scope) | Recording change narratives + attribution for templates/prompts/rules is a distinct, bounded responsibility. Risk is *boundary*, not capability — it overlaps audit/DecisionTimeline and the per-domain brand/tracking/BrandChangeLog, not that it is too small to do its own job. | ~250–400 | Low |
| VersionManager | Sufficient for the *named* scope; questionable if scope is meant to be platform-wide | Version graphs + compatibility + rollback for three artefact types is a coherent unit. If the unstated expectation is that it also orchestrates/registers the four existing per-domain managers, it is underpowered for that and would need a registry/coordinator responsibility it does not currently have. | ~400–700 | Low |

The system is small (2 subsystems). For its **stated** scope (templates/prompts/rules only) the two subsystems are sufficient and the split (history-of-why vs version-state) is clean and non-overlapping internally. The underpowered concern is **conditional on intent**: it only bites if "versioning across the whole system" is the real requirement.

---

## Missing Capability Areas

- **Version registry / authority index** — *only if* versioning is intended to be the central authority. No subsystem governs or even enumerates the per-domain version managers. Impact: without it, "versioning" is a federated pattern with four+ independent implementations and no shared contract. Est. ~200–400 LOC if added. **Decision required — do not assume.**
- **Diff / comparison surface** — implied by rollback, not owned by any versioning subsystem. ~150–300 LOC if it lands here rather than in callers.

If the narrow scope is confirmed as intentional, the answer to "Missing Capability Areas" is effectively **None identified** within scope.

---

## Boundary & Classification Issues

- **Central-vs-per-domain versioning (the headline overlap).** Four per-domain version managers already exist and are Pass-0 KEEP/Approved with overlap marked "None":
  - `brand/core/BrandVersionManager` (brand profiles; sibling `brand/tracking/BrandChangeLog` logs the changes)
  - `document-templates/TemplateVersionManager` (template versions, history, rollback, comparison)
  - `marks/MarkVersioning` (marks/logos versioning, rollback, history)
  - `template/TemplateLibraryManager` (template versions v1→v2→v3, drift, archive, rollback)
  As currently documented, these domains are the authority for their own versioning; `versioning/` is **not** their parent. This is a structural ruling that should be made explicit: either (a) versioning/ is a *pattern provider / shared library* the per-domain managers conform to, (b) versioning/ is the *central authority* and the per-domain ones become thin adapters (large restructure — they are already approved), or (c) they are *independent by design* and versioning/ owns only the template/prompt/rule artefacts that have no domain home. The repo currently implies (c) by silence. **Flag, do not resolve.**
- **ChangeLog ↔ audit/DecisionTimeline.** Both reconstruct "who did what and why." DecisionTimeline is the decision/audit-trail authority (compliance, GDPR/AI-Act, actor attribution). ChangeLog is change-narrative for versioned artefacts. The Intent word "audit trails" in versioning blurs this. Per CLAUDE.md, audit/decision concerns are not versioning's to own — confirm ChangeLog *feeds* audit rather than *is* the audit trail.
- **ChangeLog ↔ brand/tracking/BrandChangeLog (naming + scope collision).** Same noun, different scope. BrandChangeLog's Pass0 sibling-overlap is "None" within brand. Cross-system, the two ChangeLogs need an explicit scope line (brand-change-log = brand profile changes; versioning ChangeLog = template/prompt/rule changes) or a shared contract.
- **VersionManager ↔ system/Archivist.** Archivist's own description claims "archival storage, **versioning**, and retrieval of historical content and data." The word "versioning" appears in two systems' self-descriptions. Boundary needed: Archivist = cold storage/retention of historical records; versioning = active version graph + rollback of live artefacts.
- **VersionManager ↔ orchestration/MigrationHandler.** MigrationHandler owns *data* migrations + rollback and explicitly defers version-tracking to versioning/VersionManager. This boundary is the cleanest of the set (MigrationHandler already references VersionManager) but should be confirmed: schema/data migration rollback = MigrationHandler; artefact version rollback = VersionManager.
- **Global-rules check (CLAUDE.md).** No brand decisions, failure decisions, or renderer logic appear in versioning — global constraints are respected. Rollback is an operation, not a *failure decision* (those remain in `resilience/`); confirm VersionManager invokes rollback but does not *decide* when failure-driven rollback occurs.

---

## Overall Build Size Estimate

- **Estimated total system LOC:** ~650–1100 (ChangeLog ~250–400 + VersionManager ~400–700). With an optional version-registry/diff capability if central-authority intent is confirmed, add ~350–700.
- **Largest expected file:** VersionManager (~400–700 LOC).
- **Overall size risk:** Low. No file approaches the ~1500-LOC (other-projects) / ~2000-LOC (NCE-V2/TS) High-risk threshold. The only path to a large file is if VersionManager absorbs a cross-domain registry/coordinator role — which would be a scope change, not organic growth.

---

## Risk Assessment

- **Risk level: Medium** (driven by boundary ambiguity, not by size or capability).
- File-size risk is Low. Internal decomposition is clean. The Medium rating comes entirely from four unresolved boundary collisions — the central-vs-per-domain authority question, the two ChangeLog scope overlaps (audit + brand), and the Archivist "versioning" self-claim. These are PASS-0-appropriate *questions surfaced*, not defects to fix here. Left unresolved, they risk drift (two ChangeLogs of record, two systems claiming "versioning") during PASS 1–3.

---

## Verdict

**CAN** fulfil its role as currently structured — **for its stated, narrow scope** (versioning + change history for templates, prompts, and rules). The two subsystems are sufficient and well-separated for that scope.

Conditions:
1. Confirm scope is intentionally narrow (templates/prompts/rules) and versioning/ is **not** the central authority over the four existing per-domain version managers. If central authority *is* intended, the verdict drops to **CANNOT as currently structured** — a registry/coordinator responsibility and a per-domain-manager restructure would be required.
2. Draw the ChangeLog ↔ audit/DecisionTimeline line (feeds vs is) explicitly before PASS 1.
3. Give versioning/ChangeLog and brand/tracking/BrandChangeLog distinct, written scopes.
4. Resolve the "versioning" word collision between VersionManager and system/Archivist.

**Status:** Draft Complete – Awaiting Review
