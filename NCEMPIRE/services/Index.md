# services — Index

## Subsystems

| Subsystem | Responsibility | Status | Spec | Tests | Notes |
|----------|----------------|--------|------|-------|-------|
| DatabaseHandler | Single async interface to shared D1 (queries, builders, batch, FTS, migrations) | Spec Complete | Spec.md (v1.0.0) | — | PASS 0/2/3 approved 2026-06-07. Single shared D1. DATABASE_ codes in ERROR-CODES v1.9.0. |
| FileManager | Repo/object access (R2) — read/list/validate | Spec Complete | FileManager.md | — | Pre-existing approved spec. Legacy checklist marked "Approved"; own doc "Spec Complete" — discrepancy unreconciled. |
| APIKeyManager | API key storage/retrieval/rotation | Placeholder | — | — | Workers Secrets target (per STACK-PROPOSAL). |
| AuthHandler | Authentication / permissions | Placeholder | — | — | |
| ContentMetadataManager | Metadata for generated content | Placeholder | — | — | |
| DataSanitizer | Input/output sanitization | Placeholder | — | — | |
| GeneratedContentCatalog | Catalog of generated artifacts | Placeholder | — | — | |
| IngestionEngine | Ingestion of source material | Placeholder | — | — | |
| Librarian | Discovery/search/recommend across repos | Placeholder | — | — | |
| PromptBuilder | Prompt compilation from templates + context | Placeholder | — | — | Feeds AI chain (ThemeGen/OutlineGen/TemplateEngine). |
| PromptCondenser | Prompt size reduction | Placeholder | — | — | |
| PythonRunner | (legacy) Python script execution | Placeholder | — | — | ⚠ No native Workers equivalent — needs real PASS 0 viability call (KEEP/MERGE/DROP) when its turn comes. |
| ResearchTools | Web search / scrape / synthesize | Placeholder | — | — | |
| VectorStore | Embedding storage/query | Placeholder | — | — | Cloudflare Vectorize candidate (OQ-STORE-2). |

## External Dependencies

| System | Reason |
|-------|--------|
| Cloudflare D1 binding | DatabaseHandler — the shared database |
| Cloudflare R2 binding | FileManager — object/repo storage |
| Workers Secrets | APIKeyManager (when specced) |
| Cloudflare Vectorize | VectorStore (candidate, when specced) |

## Feeds

### Feeds In
- Queries/migrations from all stateful modules (→ DatabaseHandler)
- Repo/object requests (→ FileManager)

### Feeds Out
- Durable D1 reads/writes; R2 objects; (others as specced)

## Error Domains
- DATABASE_ (DatabaseHandler) — see ERROR-CODES.md v1.9.0
- PATH_/FILE_/ENTRY_/REPO_/SCHEMA_/JSON_/MODULE_ (FileManager)
- (Others recorded as those specs complete)

## Index Notes
- Co-located tracker for the `services/` domain. Canonical-tracker question
  (this vs legacy MODULE-SPEC-CHECKLIST.md) deferred per user 2026-06-07.
- This pass: DatabaseHandler filled (Spec Complete). FileManager status carried
  from its own doc. Other 12 remain Placeholder, not yet through PASS 0.
- ⚠ PythonRunner flagged: its legacy spec assumes CPython subprocess + numpy/
  pandas/nltk, which have no native Workers equivalent. Needs a genuine PASS 0
  KEEP/MERGE/DROP decision (not a rubber-stamp) when reached.
- Discrepancy to reconcile later: legacy checklist says FileManager "Approved";
  FileManager.md says "Spec Complete".
