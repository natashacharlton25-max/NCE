# PHASE 32: REPO SETUP

---
Phase: 32
Name: Repo Setup
Section: 0d. PreCode
Location: NCE-V2/NCE V2.0 Spec & Build/32. Repo-Setup/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You produce a concrete repository setup spec that defines the actual folder layout, top-level files, and per-system Worker scaffolding for NCE-V2.

This builds on Phase 24's REPO-SPEC.md (which defined principles) and produces the actionable Repo Structure for implementation.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker grouping
2. [FileTree-v2.md](../../FileTree-v2.md) — 27 systems + lib/
3. `NCE-V2/specs/REPO-SPEC.md` (Phase 24)
4. `NCE-V2/specs/LIBRARY-STRUCTURE.md` (Phase 31)

---

## TASK

Produce `REPO-STRUCTURE.md` with the full implementation-ready folder layout:

```
nce-v2/                                  # repo root
├── README.md
├── package.json                         # workspace root
├── pnpm-workspace.yaml (or yarn/npm workspaces)
├── tsconfig.base.json                   # shared TS config
├── .gitignore
├── .github/                             # CI (Phase 61)
│
├── src/
│   ├── platform/                        # platform Worker (services + system + state + library)
│   │   ├── services/
│   │   │   ├── APIKeyManager.ts
│   │   │   ├── AuthHandler.ts
│   │   │   ├── ContentMetadataManager.ts
│   │   │   ├── DatabaseHandler.ts
│   │   │   ├── DataSanitizer.ts
│   │   │   ├── GeneratedContentCatalog.ts
│   │   │   ├── IngestionEngine.ts
│   │   │   ├── PromptBuilder.ts
│   │   │   ├── PromptCondenser.ts
│   │   │   ├── PythonRunner.ts          # [FLAG] OQ-PY-1 — disposition unresolved
│   │   │   ├── ResearchTools.ts
│   │   │   └── VectorStore.ts
│   │   ├── system/
│   │   ├── state/
│   │   └── library/                     # Librarian, FileManager, Writer, Archivist, CacheHandler
│   │
│   ├── access/                          # own Worker
│   ├── ai/
│   ├── assets/
│   ├── audit/
│   ├── brand/
│   ├── checks/
│   ├── content/
│   ├── website/
│   ├── email/
│   ├── social/
│   ├── document-templates/
│   ├── documents/
│   ├── renderers/
│   ├── marks/
│   ├── integrations/
│   ├── observability/
│   ├── orchestration/
│   ├── publishing/
│   ├── resilience/
│   ├── review/
│   ├── template/
│   ├── verification/
│   └── versioning/
│
├── src/lib/                             # shared utilities (Phase 31)
│   └── svg/
│
├── tests/                               # parallel structure under src/
│
├── migrations/                          # D1 migrations per library (Phase 29)
│   ├── brands/
│   ├── audiences/
│   └── ...
│
├── wrangler/                            # per-Worker wrangler.toml configs
│   ├── platform.toml
│   ├── access.toml
│   └── ... (one per system Worker)
│
└── docs/                                # references back to NCE-V2 docs
```

Per-Worker `wrangler.toml` template (skeleton):
```toml
name = "{{worker-name}}"
main = "src/{{system}}/index.ts"
compatibility_date = "2025-01-01"

[[d1_databases]]
binding = "{{BINDING_NAME}}"
database_name = "{{db-name}}"
database_id = "{{from-d1-create}}"

[[r2_buckets]]
binding = "{{BUCKET_BINDING}}"
bucket_name = "{{bucket-name}}"

[vars]
ENVIRONMENT = "production"
```

---

## OUTPUT LOCATION

```
NCE-V2/specs/REPO-STRUCTURE.md
```

---

## MANDATORY RULES

- Match FileTree-v2.md exactly
- `platform` Worker groups services/system/state/library
- Each other system gets its own Worker
- Apply Phase 24's REPO-SPEC.md principles
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] `REPO-STRUCTURE.md` shows full layout
- [ ] Per-Worker wrangler.toml skeleton documented
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 33 (Tech Stack)

---

## TEMPLATES (enriched for NCE-V2)

- [REPO-STRUCTURE-TEMPLATE.md](./REPO-STRUCTURE-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
