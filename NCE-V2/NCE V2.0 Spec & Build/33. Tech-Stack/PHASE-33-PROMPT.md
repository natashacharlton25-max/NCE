# PHASE 33: TECH STACK

---
Phase: 33
Name: Tech Stack
Section: 0d. PreCode
Location: NCE-V2/NCE V2.0 Spec & Build/33. Tech-Stack/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You document the explicit tech stack decisions for NCE-V2. For NCE-V2, most decisions are already made (and locked in STACK-AND-RUNTIME.md). This phase produces a `TECH-STACK.md` that consolidates them.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — primary source for stack decisions
2. [Project-Intent.md](../../Project-Intent.md) — constraints + non-goals
3. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) — D1 confirmed

---

## NCE-V2 TECH STACK (PRE-DECIDED)

| Category | Choice | Source |
|----------|--------|--------|
| **Language** | TypeScript (strict mode) | Project-Intent.md |
| **Runtime** | Cloudflare Workers (V8 isolates) | STACK-AND-RUNTIME.md |
| **Plan** | Workers paid plan (5-min CPU, 128 MB memory) | STACK-AND-RUNTIME.md |
| **Database** | Cloudflare D1 (SQLite at edge) | LIBRARY-TEMPLATE v2.0.0 |
| **Object Storage** | Cloudflare R2 | STACK-AND-RUNTIME.md |
| **Cache / Config** | Cloudflare KV | STACK-AND-RUNTIME.md |
| **State Coordination** | Cloudflare Durable Objects | STACK-AND-RUNTIME.md |
| **Embeddings** | Cloudflare Vectorize | STACK-AND-RUNTIME.md |
| **Downstream Web Platform** | Astro (consumes NCE-V2 JSON) | Output-boundary rule |
| **Email Send** | Emailit (`integrations/EmailitIntegration`) | FileTree-v2.md |
| **CLI / Deploy** | wrangler | STACK-AND-RUNTIME.md |

## OPEN DECISIONS (to lock in this phase)

| Category | Options | Recommendation |
|----------|---------|----------------|
| **Package Manager** | npm / pnpm / yarn | pnpm (good with workspaces) |
| **Build Tool** | wrangler-bundled / esbuild / vite | wrangler (default for Workers) |
| **TypeScript Version** | latest stable | Pin in tsconfig + package.json |
| **Test Framework** | vitest / jest | vitest (faster, ESM-native, works with Workers via miniflare) |
| **Worker Testing** | miniflare / wrangler dev | miniflare (in-process; integrates with vitest) |
| **Linting** | ESLint / Biome | Biome (faster, one tool for lint + format) |
| **Formatting** | Prettier / Biome | Biome (same reason) |
| **D1 Query Builder** | raw SQL / Drizzle ORM / Kysely | TBD — flag for user decision |
| **CI/CD** | GitHub Actions / Cloudflare Workers Builds | GitHub Actions + wrangler (more flexibility); or Workers Builds for tighter integration |

Each open decision goes in `PRECODE-DECISION-NOTES.md` with justification.

---

## TASK

For each tech-stack category (pre-decided AND open):

1. Document the choice
2. Document the version constraint (e.g. `typescript@^5.6`, `wrangler@^3.x`)
3. Document the justification
4. Document alternatives considered
5. Document trade-offs accepted

Produce `TECH-STACK.md` covering all categories.

> **Logging rule:** Each "open decision" resolution logged in `NCE-V2/admin/PRECODE-DECISION-NOTES.md`.

---

## OUTPUT LOCATION

```
NCE-V2/specs/TECH-STACK.md
```

---

## MANDATORY RULES

- Don't re-debate pre-decided choices (they're locked)
- Force a decision on every open category — don't leave TBD without justification
- TypeScript everywhere (no JS files outside config)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] `TECH-STACK.md` covers every category
- [ ] All open decisions resolved or explicitly deferred with reason
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 34 (Environment)

---

## TEMPLATES (enriched for NCE-V2)

- [TECH-STACK-TEMPLATE.md](./TECH-STACK-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- The "open decisions" list above — confirm or override the recommendations?
- May this draft be promoted to "Approved"?
