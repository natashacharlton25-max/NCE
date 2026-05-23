# NCE-V2

TypeScript-on-Cloudflare-Workers system-of-systems for controlled, explainable planning, generation, governance, and publishing of brand-aligned content and assets.

---

## Start here

| If you want to… | Open |
|---|---|
| Understand what NCE-V2 is and why it exists | [`Project-Intent.md`](./Project-Intent.md) |
| See the current build status (which methodology stage is active) | [`admin/PROJECT-FRAME.md`](./admin/PROJECT-FRAME.md) |
| Begin or continue the build (instructions per stage) | [`methodology/README.md`](./methodology/README.md) |
| See the canonical 27-system tree | [`FileTree-v2.md`](./FileTree-v2.md) |
| Understand the Cloudflare runtime + infrastructure | [`STACK-AND-RUNTIME.md`](./STACK-AND-RUNTIME.md) |
| Understand the build methodology design | [`docs/REDUCED-METHODOLOGY.md`](./docs/REDUCED-METHODOLOGY.md) |

---

## What's where

```
NCE-V2/
├── README.md                              ← you are here
│
├── Project-Intent.md                      ← why NCE-V2 exists; locked design principles + technical substrate
├── FileTree-v2.md                         ← canonical 27-system list + lib/ utilities
├── STACK-AND-RUNTIME.md                   ← Cloudflare setup, configs, Worker grouping
├── REFINEMENTS.md                         ← historical record of architectural refinements (proposals now in FileTree-v2)
│
├── admin/                                 ← project tracking + status (Stage outputs)
│   └── PROJECT-FRAME.md                   ← Stage 1 output: FRAME LOCKED
│
├── methodology/                           ← the 5-stage build process (prompts + templates)
│   ├── README.md                          ← start here for methodology
│   ├── STAGE-1-PROMPT.md … STAGE-5-PROMPT.md
│   ├── SYSTEM-SPEC-TEMPLATE.md, SUBSYSTEM-SPEC-TEMPLATE.md, etc.
│   └── STAGE-5-TEMPLATES/                 ← release-stage sub-templates
│
├── specs/                                 ← per-system, per-subsystem, per-library, per-integration specs (Stage 2+ outputs)
│   ├── access/, ai/, assets/ … (27 system folders, currently empty awaiting Stage 2)
│   └── integrations/
│
├── docs/                                  ← supporting docs
│   ├── REDUCED-METHODOLOGY.md             ← design rationale for the 5-stage methodology
│   └── templates/
│       └── LIBRARY-TEMPLATE.md            ← v2.0.0; D1-based library template used by Stage 2
│
└── archive/                               ← historical reference; not for active use
    └── 68-phase-methodology/              ← the 68-phase methodology this project used before reducing to 5 stages
```

---

## Current state

**Stage 1 of 5 — FRAME LOCKED** (see `admin/PROJECT-FRAME.md`).

Next: Stage 2 (Component Specs) — produces one comprehensive spec per system, subsystem, library, and integration service. ~300 docs. See `methodology/STAGE-2-PROMPT.md`.

---

## Locked constraints (apply throughout every stage)

These are non-negotiable without revisiting Project-Intent.md and re-running Stage 1.

- **Runtime:** TypeScript on Cloudflare Workers (V8 isolates; paid plan — 5-min CPU, 128 MB memory per invocation)
- **Storage:** D1 for libraries (text + JSON only); R2 for binaries (via `assets/` system); KV for cache/config; Durable Objects for stateful coordination; Vectorize for embeddings
- **27 systems** plus `lib/svg/` utilities (canonical list in FileTree-v2.md)
- **Worker organisation:** one Worker per system; `services` + `system` + `state` + `library` grouped into a `platform` Worker (foundation tier)
- **Output-boundary rule:** NCE-V2 emits JSON for web (rendered by Astro); NCE-V2 emits final rendered artefacts for PDF/DOCX/email/embedded marks
- **Library access:** all reads via `library/Librarian`; only library write-owner module writes its library
- **LOC band:** ~2000 lines per runtime `.ts` file (excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments); TS verbosity 1.3–1.5× over Python
- **TypeScript strict:** no `any` without justification; ESM only
- **Worker secrets:** never in repo; always via `wrangler secret`

---

## Related (outside this folder)

| Doc | Where | What for |
|---|---|---|
| AI collaboration contract | `C:/Users/NCE/CLAUDE.md` | Universal rules; §3 supersession note explains methodology choice for NCE-V2 |
