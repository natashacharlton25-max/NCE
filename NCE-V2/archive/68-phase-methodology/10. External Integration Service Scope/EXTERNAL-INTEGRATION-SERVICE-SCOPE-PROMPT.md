# PHASE 10: EXTERNAL INTEGRATION SERVICE SCOPE

---
Phase: 10
Name: External Integration Service Scope (identify services per provider)
Location: NCE-V2/NCE V2.0 Spec & Build/10. External Integration Service Scope/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You identify which specific services/APIs are needed from each approved NCE-V2 provider.

You are NOT designing implementation or selecting specific endpoints yet.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../../FileTree-v2.md) — `integrations/` subsystems often imply service-level scope (e.g. `GoogleDocsRenderer` implies Google Docs API service of the Google provider)
5. `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md` (Phase 9 output, per provider)

---

## RUNTIME CONTEXT

- Each service may have its own auth, rate limits, payload caps
- Worker 5-min CPU / 128 MB memory constraint applies per request

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

Each service has a direction:
- **Outbound receiver** (Google Docs API receives rendered DOCX; Emailit receives rendered HTML)
- **Inbound source** (Pexels API supplies image URLs)
- Combined / Bi-directional (less common)

SERVICE-SCOPE entries must declare service direction.

---

## TASK

For each approved provider:

1. Read `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md`
2. Cross-reference FileTree-v2.md to see which `integrations/<Provider>...` subsystems exist (these typically map to services)
3. Identify the minimum services needed:
   - Service name + API version
   - Direction (inbound / outbound / combined)
   - Auth (often inherits from provider; sometimes service-specific)
   - Why needed + which internal system consumes it
4. Produce a Service Scope artefact per provider

> **Logging rule:** Any service inferred from FileTree-v2 but not stated in the user's intentions goes in `PASS-DECISION-NOTES.md` for confirmation.

---

## RULES

1. **Only identify services from approved providers** — no new providers
2. One service = one distinct API/product
3. Include auth services if needed (OAuth, etc.)
4. Do **NOT** design implementation
5. Prefer fewer services — minimum needed
6. Apply output-boundary direction per service
7. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/integrations/{{provider}}/SERVICE-SCOPE.md
```

One file per provider.

---

## END CONDITION

- [ ] Each provider has a `SERVICE-SCOPE.md`
- [ ] Each service has direction declared
- [ ] Each service maps to a consuming internal system
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 11 (External Integration Service Build)

---

## TEMPLATES (enriched for NCE-V2)

- [SERVICE-SCOPE-TEMPLATE.md](./SERVICE-SCOPE-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/specs/integrations/<provider>/PROVIDER-NOTES.md`
- `NCE-V2/FileTree-v2.md`
- All `NCE-V2/specs/<system>/SYSTEM-NOTES.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
