# PHASE 22: EXTERNAL INTEGRATION SPECS

---
Phase: 22
Name: External Integration Specs (implementation-ready specs for providers + services)
Location: NCE-V2/NCE V2.0 Spec & Build/22. External-Integration-Specs/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are writing implementation-ready specs for NCE-V2 external integrations. Each provider + service combination produces a focused set of spec files documenting the external boundary, our wrapper, and field mappings.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker `fetch()`, secret bindings
4. [FileTree-v2.md](../../FileTree-v2.md) — `integrations/` system listing
5. `NCE-V2/specs/integrations/<provider>/<service>/PRE-SPEC-NOTES.md` (Phase 20 — primary source)
6. `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md` + `SERVICE-SCOPE.md`
7. `NCE-V2/specs/integrations/<provider>/<service>/SERVICE-NOTES.md` + `CONTRACT.md`

---

## RUNTIME CONTEXT

- **Outbound from Workers** via `fetch()`; auth via Worker secret bindings
- **5-min CPU, 128 MB memory** per invocation
- **`resilience/`** patterns (RateLimiter, RetryPolicyEngine, FallbackStrategyResolver, etc.)
- **`assets/` (R2)** for inbound binary sinks
- **`library/` (D1)** for inbound metadata sinks

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

For each integration, the spec must respect:
- **Outbound receivers** (Emailit, Canva, Google Docs/Sheets/Slides): we send rendered artefacts (HTML / PDF blob / DOCX blob / etc.) — never raw JSON expecting downstream rendering
- **Inbound sources** (Pexels, Unsplash, Recraft, Phosphor): we receive raw data; binaries land in `assets/`/R2, metadata lands in library D1 via `library/`
- **Downstream renderers** (Astro): not a provider — listed separately if applicable

---

## TASK

**For EACH service in each provider's `SERVICE-SCOPE.md`:**

1. Read `PRE-SPEC-NOTES.md` for the service (Phase 20)
2. Create `spec/` folder at `NCE-V2/specs/integrations/<provider>/<service>/`
3. Create spec files using templates (10 files — slightly different set from Phase 21)
4. Get approval per service

---

## SPEC FILES PER SERVICE (10 files)

| File | Purpose | NCE-V2 Notes |
|------|---------|---------------|
| INDEX-EXTERNAL.md | Quick reference + spec gaps | Includes Direction (inbound/outbound/renderer) |
| OVERVIEW-EXTERNAL.md | Purpose, scope, boundaries | State direction + form |
| PROVIDER.md | Provider context (auth, account, billing) | Worker secret binding name |
| API-SURFACE.md | External API endpoints + auth scopes | TypeScript types for request/response |
| OUR-WRAPPER.md | Our internal wrapper (function names, retry/fallback patterns) | resilience/ involvement |
| FIELD-MAPPING.md | Map between external API fields and our internal types | TS type ↔ external field |
| ERRORS-EXTERNAL.md | External error codes + our mapping to error categories | resilience/ failure category |
| CONSTRAINTS.md | Rate limits, payload caps, quotas | Worker CPU/memory interaction |
| FAILURE-MODES.md | What failures + our handling | resilience/ pattern reference |
| EXAMPLES.md | Request/response examples (TypeScript code) | |

---

## PROCESS ORDER

Per provider (alphabetical), then per service inside that provider:
```
NCE-V2/specs/integrations/{{provider}}/{{service}}/spec/
├── INDEX-EXTERNAL.md
├── OVERVIEW-EXTERNAL.md
├── PROVIDER.md
├── API-SURFACE.md
├── OUR-WRAPPER.md
├── FIELD-MAPPING.md
├── ERRORS-EXTERNAL.md
├── CONSTRAINTS.md
├── FAILURE-MODES.md
└── EXAMPLES.md
```

---

## MANDATORY RULES

- Do **NOT** invent provider functionality
- Do **NOT** store credentials in spec — reference Worker secret binding names only
- Apply output-boundary direction (inbound / outbound / renderer)
- Reference `resilience/` patterns in OUR-WRAPPER.md and FAILURE-MODES.md
- Inbound binary sinks go to `assets/` (R2); inbound metadata sinks go via `library/` (D1)
- TypeScript signatures everywhere
- Do **NOT** exceed ~300 LOC per spec file
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/integrations/{{provider}}/{{service}}/spec/
```

---

## APPROVAL GATE

Per service:
- [ ] All 10 spec files created
- [ ] Direction correctly identified
- [ ] Worker secret binding name set
- [ ] resilience/ patterns referenced where applicable
- [ ] Human approves

---

## END CONDITION

Phase 22 is COMPLETE when:
- [ ] Every approved service has `spec/` with all 10 files
- [ ] All approved
- [ ] PASS-PROGRESS.md updated

**Next:** Phase 23 (Library Specs)

---

## TEMPLATES (enriched for NCE-V2)

10 templates in this folder.

---

## INPUTS

- `NCE-V2/specs/integrations/<provider>/<service>/PRE-SPEC-NOTES.md` (Phase 20)
- `NCE-V2/specs/integrations/<provider>/<service>/PASS-NOTES.md` (Phase 19)
- `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md`, `SERVICE-SCOPE.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
