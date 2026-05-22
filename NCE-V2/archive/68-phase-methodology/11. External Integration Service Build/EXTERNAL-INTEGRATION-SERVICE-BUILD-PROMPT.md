# PHASE 11: EXTERNAL INTEGRATION SERVICE BUILD

---
Phase: 11
Name: External Integration Service Build (materialise SERVICE-NOTES.md per service)
Location: NCE-V2/NCE V2.0 Spec & Build/11. External Integration Service Build/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You materialise approved NCE-V2 services into per-service folders + SERVICE-NOTES.md inside their provider folders.

You are NOT designing implementation or writing code.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../../FileTree-v2.md)
5. `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md` and `SERVICE-SCOPE.md` (Phases 9+10 outputs)

---

## RUNTIME CONTEXT

- Worker 5-min CPU, 128 MB memory
- Outbound `fetch()` to provider API
- Auth via Worker secret binding

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

Each SERVICE-NOTES.md must declare:
- **Direction** (inbound / outbound / combined)
- **Output Form (if outbound)** (rendered HTML / PDF blob / DOCX blob / JSON / other)
- **Asset Sink (if inbound binary)**: R2 via `assets/` system
- **Library Sink (if inbound metadata)**: D1 binding via `library/`

Direct D1/R2 writes from outside `library/` or `assets/` are forbidden.

---

## TASK

For each approved service in each provider's `SERVICE-SCOPE.md`:

1. Create folder at `NCE-V2/specs/integrations/<provider>/<service>/`
2. Create `SERVICE-NOTES.md` inside
3. Complete sections:
   - Service identity (name, API version, docs URL)
   - Direction + form
   - Purpose + consuming systems
   - In Scope / Out of Scope / Non-Goals
   - Data boundary (what we send / what we receive, sensitivity, sink target)
   - Authentication (auth method + Worker secret binding name)
   - Service constraints (rate limit, payload size, quota)
   - Failure contract (what failures expected; resilience/ involvement)

> **Logging rule:** TBD markers and any inferred content go in `PASS-DECISION-NOTES.md`.

---

## RULES

1. Use **ONLY** approved services from `SERVICE-SCOPE.md`
2. Do **NOT** rename services
3. Do **NOT** design implementation or error-handling logic
4. Do **NOT** store credentials — reference Worker secret binding names
5. Mark unknowns as TBD; log them
6. Respect the **2000 LOC band** for the eventual integration wrapper
7. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/integrations/{{provider}}/{{service}}/
└── SERVICE-NOTES.md
```

---

## END CONDITION

- [ ] Each approved service has `NCE-V2/specs/integrations/<provider>/<service>/SERVICE-NOTES.md`
- [ ] Each declares Direction + Form + Auth binding name
- [ ] Provider's `PROVIDER-NOTES.md` "Services Used" table updated
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 12 (PrePass Check)

---

## TEMPLATES (enriched for NCE-V2)

- [SERVICE-NOTES-TEMPLATE.md](./SERVICE-NOTES-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md`
- `NCE-V2/specs/integrations/<provider>/SERVICE-SCOPE.md`
- All `NCE-V2/specs/<system>/SYSTEM-NOTES.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
