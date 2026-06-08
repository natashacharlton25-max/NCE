# System Coverage Review — services

## System Role Summary
`services/` is the cross-cutting capability layer every other system relies on: durable persistence (D1), object/file read access (R2), credentials, auth, prompt assembly/condensing, content provenance/discovery, ingestion, library search, and the embedding store. It owns the *how* of shared infrastructure so domain systems deal in their own data, not in runtime mechanics. This is the project's largest system (14 subsystems) and concentrates several distinct concern-families under one roof, so overlap and overload are the primary review risks.

## Coverage Assessment

### Covered Responsibilities
| Responsibility | Covered By |
|----------------|------------|
| Single async gateway to shared D1 (queries, builders, batch, FTS, migrations, namespacing) | DatabaseHandler (Spec Complete, PASS 0/2/3 approved) |
| Object/repo read access over R2 (read, list, validate, schema load) | FileManager (Spec Complete v1.1.0) |
| Prompt assembly from templates + context | PromptBuilder (full descriptive spec) |
| Prompt size reduction to token limits | PromptCondenser (full descriptive spec) |
| API key/credential storage & retrieval (→ Workers Secrets) | APIKeyManager (intent stated; spec stub) |
| Authentication / authorization (single-user now; Google OAuth) | AuthHandler (intent stated; spec stub) |
| Content provenance / metadata / lineage | ContentMetadataManager (intent stated; spec stub) |
| PII scrubbing before external API calls | DataSanitizer (intent stated; spec stub) |
| Generated-content indexing for discovery/reuse | GeneratedContentCatalog (intent stated; spec stub) |
| Draft→library ingestion/promotion workflow | IngestionEngine (detailed gap analysis; spec stub) |
| Cross-repo discovery/search/recommend + reference-integrity/cascade | Librarian (detailed gap analysis; spec stub) |
| Vector storage / similarity search (→ Vectorize) | VectorStore (detailed intent + provisional schema; spec stub) |
| Research / external lookup | ResearchTools (intent stated; spec stub) |

### Partially Covered Responsibilities
- **Credential rotation / lifecycle** — APIKeyManager intent names storage/retrieval/rotation, but rotation, revocation, and per-provider scoping are unspecified. On Workers Secrets, rotation semantics differ from a writable store and need explicit treatment.
- **Auth depth** — AuthHandler declares single-user now / Google OAuth / multi-user "if needed." Session model, token verification, and the boundary against the global `access/` system (PolicyEngine-style permission decisions) are undefined.
- **Discovery ownership is split three ways** — ContentMetadataManager (provenance), GeneratedContentCatalog (index/search/reuse), and Librarian (cross-repo search/recommend/FTS) all claim search/index/lineage surface. Each is individually plausible; the seams between them are not drawn.
- **Reference-integrity & cascade** — Librarian's cascade-on-archive table is rich, but it references CacheHandler (not a services subsystem here) and overlaps IngestionEngine's "validate via Librarian" loop; ownership of the integrity check is ambiguous.
- **Embedding lifecycle** — VectorStore stores/queries vectors but depends on ai/EmbeddingService to generate them; re-indexing on model change is flagged in intent but unowned.

### Uncovered Responsibilities
- **Stale-reference reconciliation at scale** — Librarian marks `ref_status='stale'` but no subsystem owns sweeping/repairing stale refs over time.
- **Secret-access audit trail** — who-read-which-key is not assigned (audit/ system likely, but the services-side hook is unstated).
- **Cache layer** — repeatedly referenced (CacheHandler) by IngestionEngine and Librarian but not a subsystem of `services/`; either it lives elsewhere (state/?) or there is a hole. Flag, do not resolve.
- **PII compliance reporting** — DataSanitizer "owns compliance reporting" per intent, but where reports go / retention is uncovered.

## Subsystem Sufficiency Review
| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| DatabaseHandler | Sufficient | Spec Complete; single shared D1 gateway, builders, batch, FTS5, migrations, namespacing all owned and bounded; non-goals explicit. | 900–1300 | Medium |
| FileManager | Sufficient | Spec Complete v1.1.0; clear read-only boundary vs Writer/Librarian/Archivist; schema load/validate included. | 700–1000 | Low–Medium |
| PromptBuilder | Mostly sufficient | Full pipeline doc (load→inject→merge→validate/condense→return); delegates condensing to PromptCondenser. Context-source breadth is wide. | 800–1200 | Medium |
| PromptCondenser | Mostly sufficient | Full doc; priority-based reduction to token limits. Token counting boundary vs ai/TokenManager to confirm. | 500–800 | Low–Medium |
| Librarian | Underspecified (intent rich) | Strong gap analysis (multi-library search, recommend, FTS helpers, cascade rules) but spec stub; large cross-cutting surface; overlaps Catalog/Metadata. Recommendation engine is a sub-concern that could overload it. | 1000–1500 | **High** |
| VectorStore | Underspecified (intent rich) | Clear methods/schema/use-cases; depends on ai/EmbeddingService; SQLite-VSS framing must remap to Vectorize on target (intent SQL is legacy). | 600–900 | Medium |
| IngestionEngine | Underspecified (intent rich) | Detailed promotion workflow (validate→version→insert→index→audit→remove), rollback, batch; spec stub; leans on Librarian + CacheHandler. | 700–1000 | Medium |
| ContentMetadataManager | Underspecified | Role/owns clear (content ID, metadata store/query, lineage) but no schema/contract; discovery overlap with Catalog. | 500–800 | Medium |
| GeneratedContentCatalog | Underspecified | Index/search/reuse/remix intent; spec stub; overlaps Metadata (lineage) and Librarian (search). | 500–800 | Medium |
| APIKeyManager | Underspecified | Storage/retrieval/rotation intent; Workers Secrets target named; rotation/scoping unspecified. | 300–500 | Low |
| AuthHandler | Underspecified | Single-user + Google OAuth intent; session/permission boundary vs access/ undefined. | 400–700 | Medium |
| DataSanitizer | Underspecified | PII scan/scrub/compliance intent; detection patterns and report destination unspecified. | 400–700 | Medium |
| ResearchTools | Thin / overlap | Single-line intent (search/scrape/synthesize); overlaps ai/AIWebSearch directly. Viability unclear without a drawn boundary. | 300–600 | Low–Medium |
| PythonRunner | **DROP/REMAP candidate** | No Python runtime on Cloudflare Workers; CPython + numpy/pandas/nltk cannot run. Index already carries a 2026-06-07 DROP-or-REMAP ruling. Assessment only. | 0 (drop) / unknown (remap) | n/a |

## Missing Capability Areas
| Capability | Impact | Est. LOC | Notes |
|------------|--------|----------|-------|
| Cache layer (CacheHandler) | Medium–High | n/a (assess) | Referenced by IngestionEngine & Librarian as a peer but absent from `services/`. Either lives in another system or is a hole — flag for owner confirmation; do not invent. |
| Secret-access audit hook | Medium | 50–150 | Who-read-which-key; likely surfaces to audit/, but the APIKeyManager-side emission is unassigned. |
| Stale-ref reconciliation sweep | Medium | 150–300 | Librarian sets `ref_status='stale'`; nothing owns periodic repair/cleanup. |
| Embedding re-index trigger | Medium | 100–200 | On EmbeddingService model change all vectors invalidate; trigger/owner undefined (VectorStore vs ai/). |
| Discovery contract between Metadata/Catalog/Librarian | High (clarity) | n/a (assess) | Not new code — a boundary decision needed before specs to prevent three overlapping search surfaces. |

## Boundary & Classification Issues
- **PythonRunner — DROP/REMAP (primary flag).** No Workers Python runtime. Index ruling 2026-06-07 says likely DROP (content gen uses APIs, not local Python); REMAP only if an external Python service is genuinely required. Confirm at this PASS 0. Assessment only — no redesign.
- **VectorStore ↔ ai/EmbeddingService.** Clean in principle: EmbeddingService generates vectors and "populates VectorStore"; VectorStore stores/queries. Confirm VectorStore never generates embeddings (would cross into ai/). VectorStore's intent SQL (SQLite-VSS) is legacy and must remap to Vectorize on target — note, don't redesign.
- **GeneratedContentCatalog ↔ ContentMetadataManager ↔ Librarian (triple discovery overlap).** Metadata = provenance/lineage; Catalog = index/search/reuse of generated artifacts; Librarian = cross-repo search/recommend of library entries. All three claim "search/index/lineage." Real risk of duplicated FTS/search surface. Boundary must be drawn before PASS 2; possible MERGE candidate pair (Catalog↔Metadata) to assess — not decided here.
- **ResearchTools ↔ ai/AIWebSearch.** Both cover web search/research. ResearchTools is single-line intent; AIWebSearch is an ai/ subsystem (and labelled "Integrations Module"). Overlap flag — confirm whether ResearchTools wraps AIWebSearch, owns scrape/synthesize only, or is redundant.
- **DatabaseHandler as single D1 gateway.** Correctly asserts no module touches the D1 binding directly. Verify VectorStore (if it backs onto D1 rather than Vectorize), IngestionEngine writes, and Catalog/Metadata indexes all route through DatabaseHandler — none should hold their own binding.
- **FileManager(read) ↔ system/Writer(write).** FileManager doc explicitly mirrors Writer and disclaims writes/discovery/validation-workflow. Clean; confirm Writer lives in system/ and the read/write split holds.
- **AuthHandler ↔ global access/.** Brand/Access/Resilience are global constraints (CLAUDE.md §4). Permission *decisions* belong with access/; AuthHandler should own authentication/session mechanics, not policy. Boundary to confirm.
- **CacheHandler references.** IngestionEngine and Librarian name CacheHandler as a collaborator, but it is not a `services/` subsystem — classification gap (see Missing Capabilities).

## Overall Build Size Estimate
Summing conservative midpoints across the 13 viable subsystems (PythonRunner excluded as DROP):
- Specced/sufficient core (DatabaseHandler, FileManager, PromptBuilder, PromptCondenser): ~2,900–4,300 LOC.
- Underspecified remainder (Librarian, VectorStore, IngestionEngine, ContentMetadataManager, GeneratedContentCatalog, APIKeyManager, AuthHandler, DataSanitizer, ResearchTools): ~4,700–7,100 LOC.
- **System total (excl. PythonRunner): ~7,600–11,400 LOC.** No single file is currently projected above the ~1500 LOC threshold, but **Librarian (1,000–1,500)** is the one to watch — its recommendation engine + FTS helpers + cascade logic could push it over if not partitioned.

## Risk Assessment
- **Overall risk: Medium–High.** Driven by (a) system breadth (14 subsystems, multiple concern-families), (b) the triple discovery overlap, and (c) nine subsystems still at intent-only with no responsibility-locking specs.
- **High-risk subsystem:** Librarian (size + overlap + cross-cutting reach).
- **Structural risk:** discovery responsibility split across three subsystems with undrawn seams → duplicated search surface if specced independently.
- **Platform risk:** PythonRunner is non-viable on target; VectorStore/DatabaseHandler legacy SQLite framing must translate to Vectorize/D1 (intent, not mechanism).
- **Dependency risk:** CacheHandler referenced but unplaced; secret-access audit and embedding re-index triggers unowned.
- **Low-risk anchors:** DatabaseHandler and FileManager are spec-complete and well-bounded, giving the system a solid persistence/IO foundation.

## Verdict
**CAN** fulfil its role as currently structured — but conditionally, and not without decisions before PASS 2:
1. Resolve PythonRunner (DROP vs REMAP) per the standing 2026-06-07 ruling.
2. Draw the Metadata ↔ Catalog ↔ Librarian discovery boundary (assess Catalog↔Metadata MERGE) before specs are written.
3. Place or disclaim CacheHandler (which system owns it?).
4. Confirm ResearchTools ↔ ai/AIWebSearch boundary (own scope vs redundant).
5. Confirm AuthHandler vs global access/ split (mechanics vs policy).
6. Partition Librarian to keep it under the ~1500 LOC design signal.

The four spec-complete/strong subsystems already cover the load-bearing persistence and IO responsibilities; the remaining gaps are boundary and specification work, not missing capability — hence CAN, with conditions.

**Status:** Draft Complete – Awaiting Review

---
### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Are there any corrections or refinements required?
- May I proceed, or should I revise this first?
---
