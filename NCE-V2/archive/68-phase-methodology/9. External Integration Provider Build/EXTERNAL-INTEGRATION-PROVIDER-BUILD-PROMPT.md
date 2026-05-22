# PHASE 9: EXTERNAL INTEGRATION PROVIDER BUILD

---
Phase: 9
Name: External Integration Provider Build (materialise PROVIDER-NOTES.md per provider)
Location: NCE-V2/NCE V2.0 Spec & Build/9. External Integration Provider Build/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are materialising NCE-V2's approved external integration providers into per-provider folders + PROVIDER-NOTES.md.

For NCE-V2: providers are listed under `integrations/` in FileTree-v2.md — 15 of them (Canva, Google, IntegrationAdapter, WebhookReceiver, WebScraper, YouTube, Recraft, Pexels, Unsplash, Phosphor, Emailit, plus 4 moved-from-renderers Google services). Some entries in FileTree-v2 are renderer-style (GoogleDocsRenderer, GoogleSheetsRenderer, GoogleSlidesRenderer, CanvaRenderer) — treat these as services of their respective providers in this phase.

You are NOT designing implementation or writing code.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker outbound `fetch()` patterns; secret bindings
4. [FileTree-v2.md](../../FileTree-v2.md) — `integrations/` system contents
5. `NCE-V2/admin/EXTERNAL-INTEGRATIONS-REGISTER.md` (Phase 3 output — list of approved providers)

---

## RUNTIME CONTEXT

- Outbound from Workers via `fetch()`
- Auth via Worker secret bindings (no credentials in code)
- 5-min CPU limit, 128 MB memory per invocation
- Rate limits + retry handled by `resilience/` system

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

Each provider has a direction:
- **Outbound receiver**: receives rendered artefacts from NCE-V2 (e.g. Emailit, Canva, Google Docs/Sheets/Slides)
- **Inbound source**: supplies raw inputs to NCE-V2 (e.g. Pexels, Unsplash, Recraft, Phosphor)
- **Downstream renderer**: receives JSON from NCE-V2 (Astro — but Astro isn't an integration in `integrations/`; it's the downstream platform)

PROVIDER-NOTES.md must declare the direction.

---

## TASK

For each approved provider from `EXTERNAL-INTEGRATIONS-REGISTER.md`:

1. Create folder at `NCE-V2/specs/integrations/<provider>/`
2. Create `PROVIDER-NOTES.md` inside
3. Complete all sections:
   - Provider identity (name, website, API docs)
   - Direction (Outbound receiver / Inbound source / Downstream renderer)
   - Purpose: pull from `EXTERNAL-INTEGRATIONS-REGISTER.md`
   - Services Used: "TBD — to be identified in Phase 10 (Service Scope)"
   - Consuming Systems: from `EXTERNAL-INTEGRATIONS-REGISTER.md`
   - Auth method + Worker secret binding name (placeholder if not yet decided)
   - Constraints (rate limits, quotas, pricing — research public docs)
   - Compliance & vendor risk

> **Logging rule:** Anything researched from public provider docs that wasn't in the register goes into `PASS-DECISION-NOTES.md`.

---

## SKIP CONDITION

If no external integrations approved: skip Phases 9–11 entirely.
For NCE-V2: unlikely — `integrations/` has 15 provider subsystems.

---

## RULES

1. Use **ONLY** approved providers from the register — no inventions
2. Do **NOT** select services yet (that's Phase 10)
3. Do **NOT** store credentials — reference Worker secret binding names only
4. Mark unknowns explicitly ("Unknown — verify before implementation")
5. Apply the output-boundary direction rule
6. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/integrations/{{provider}}/
└── PROVIDER-NOTES.md
```

---

## END CONDITION

- [ ] Each approved provider has `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md`
- [ ] Direction declared for each
- [ ] Worker secret binding name placeholder set
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 10 (External Integration Service Scope)

---

## TEMPLATES (enriched for NCE-V2)

- [PROVIDER-NOTES-TEMPLATE.md](./PROVIDER-NOTES-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/admin/EXTERNAL-INTEGRATIONS-REGISTER.md`
- All `NCE-V2/specs/<system>/SYSTEM-NOTES.md` (for consuming-system context)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Treating `GoogleDocsRenderer`/`GoogleSheetsRenderer`/`GoogleSlidesRenderer`/`CanvaRenderer` as services of their providers (Google, Canva) rather than separate providers — confirm?
- May this draft be promoted to "Approved"?
