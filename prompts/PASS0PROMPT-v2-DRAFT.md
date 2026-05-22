> **Superseded by `NCE-V2/NCE V2.0 Spec & Build/13. Pass-0-Coverage/13a. Pass-0a-Systems-Subsystems/PASS-0a-PROMPT.md` (merged into B's process skeleton on 2026-05-22); retained for history.**

**Status:** Draft Complete – Awaiting Review
**Type:** Updated PASS 0 review prompt for TypeScript-on-Workers clean-slate cycle
**Supersedes (pending approval):** [PASS0PROMPT.md](PASS0PROMPT.md)
**Generated:** 2026-05-18

---

# PASS 0 SYSTEM REVIEW — Prompt Template

You are acting as a senior systems architect performing a PASS 0 SYSTEM REVIEW.

This is an ASSESSMENT-ONLY task.
You are NOT designing, refactoring, or proposing solutions.

**Target system:** `{{SYSTEM_NAME}}`

*Replace `{{SYSTEM_NAME}}` with the system to review (e.g. `access`, `ai`, `brand`, etc.) before submitting this prompt.*

---

## Locked Context (Required Reading)

You are given the following authoritative documents. Treat them in this order of precedence per [CLAUDE.md](../CLAUDE.md) §10:

1. [Project-Intent.md](../Project-Intent.md) — Project intention, design principles, non-goals
2. [CLAUDE.md](../CLAUDE.md) — AI collaboration contract; pass methodology; status vocabulary
3. [STACK-AND-RUNTIME.md](../STACK-AND-RUNTIME.md) — Runtime, infrastructure, Worker boundaries
4. [REFINEMENTS.md](../REFINEMENTS.md) — Active architectural refinements affecting scope (where applicable to target system)
5. [FileTree.md](../FileTree.md) — Canonical subsystem list (current revision reflects Worker boundaries + refinements)

---

## Output-Boundary Rule (Canonical)

When assessing whether a subsystem belongs in NCEMPIRE or is a downstream-platform concern, apply this rule:

> **NCEMPIRE owns content authoring + the rendering of any artefact it itself ships to a final consumer.**
>
> - Web content is shipped as JSON to the Astro website platform for rendering. **Web rendering is not NCEMPIRE's responsibility.**
> - PDFs, DOCX, emails, and embedded mark assets are shipped by NCEMPIRE in their final rendered form. **Their rendering is NCEMPIRE's responsibility.**

Flag any subsystem whose responsibilities appear to operate on rendered web output — those concerns belong downstream (Astro), not in NCEMPIRE.

---

## Runtime Context

- **Language:** TypeScript (strict mode)
- **Runtime:** Cloudflare Workers (V8 isolates)
- **Storage primitives:** D1 (SQLite at edge), R2 (object store), KV (cache/config), Durable Objects (stateful coordination), Vectorize (embeddings)
- **Per-system organisation:** one Worker per system, with foundational systems (services, system, state) grouped into a `platform` Worker
- **External constraints:** Workers paid plan (5-minute CPU max per invocation, 128MB memory)

---

## Your Task

Evaluate whether the existing subsystems are sufficient for the target system to fulfil its role, AND estimate whether the system can be built while respecting maintainability constraints.

---

## Mandatory Rules

- Do **NOT** invent new subsystems
- Do **NOT** propose fixes or designs
- Do **NOT** rename modules
- Do **NOT** write specs or schemas
- Do **NOT** make architectural decisions; flag observations only
- If information is missing, say so explicitly
- Treat file size as a **design signal**, not an optimisation problem
- Apply the output-boundary rule when assessing whether a subsystem belongs in NCEMPIRE
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../CLAUDE.md) §7, approval is human-only

---

## Build Size Constraint

Per [Project-Intent.md](../Project-Intent.md) (updated 2026-05-18 for TypeScript-on-Workers):

- **Target:** ~2000 lines of code per runtime file
- **Exceptions allowed** with explicit justification at Pass 0
- **Where splitting is feasible, prefer splitting** over an oversized file

### What counts as runtime LOC

- Production TypeScript source files (`.ts`)
- Excludes: comments, blank lines

### What is EXCLUDED from the runtime LOC count

- Files matching `*.debug.ts` (development-only code)
- Files matching `*.test.ts` (test code)
- Files under `__debug__/` directories (debug utilities)
- Files matching `*.types.ts` containing type-only declarations (no runtime impact)
- Comments and JSDoc

### Estimating LOC

- Estimate conservatively (order-of-magnitude is sufficient)
- TypeScript is roughly 1.3–1.5× more verbose than Python for equivalent logic
- When porting Python-era LOC estimates from prior Pass 0 docs (in `legacy/pass-0-python-era/`), apply this multiplier

---

## Output Format (Strict)

## System Role Summary
- Restate what this system is responsible for (1–2 sentences)

## Coverage Assessment

### Covered Responsibilities
- …

### Partially Covered Responsibilities
- …

### Uncovered Responsibilities
- …

## Subsystem Sufficiency Review

For each subsystem listed in `FileTree.md` under `{{SYSTEM_NAME}}/`:

- **<Subsystem Name>**
  - Sufficiency: Sufficient / Overloaded / Underdefined
  - Reasoning: (1–2 sentences)
  - Estimated LOC: ~<number>
  - File Size Risk: Low / Medium / High

### File Size Risk Bands (updated)

- **Low:** <1500 LOC
- **Medium:** 1500–2000 LOC
- **High:** >2000 LOC (design smell — flag for splitting or explicit exception)

## Missing Capability Areas

(List conceptual capability areas with no clear owner. Do **NOT** propose subsystems.)

- **Capability:**
  - Impact: Low / Medium / High
  - Estimated LOC: ~<number>

## Boundary & Classification Issues

(List any responsibility that appears to leak across systems or be misclassified. Apply the output-boundary rule: flag any subsystem operating on rendered web output that should belong to Astro, not NCEMPIRE.)

- …

## Overall Build Size Estimate

- **Estimated total system LOC:** ~<number>
- **Largest expected file:** ~<number> LOC
- **Overall size risk:** Low / Medium / High

## Risk Assessment

- **Risk level:** Low / Medium / High
- **Reasoning:** …

## Verdict

This system **CAN / CANNOT** reasonably fulfil its role with the current subsystem set and size constraints.

## Status

**Draft Complete – Awaiting Review**

*Per [CLAUDE.md](../CLAUDE.md) §7, only a human reviewer may assign the status "Approved." Do not self-approve.*

---

## Pre-Submission Self-Check

Before submitting the review, verify:

- [ ] Did **NOT** invent any subsystems
- [ ] Did **NOT** propose fixes, designs, or renames
- [ ] All subsystems listed for `{{SYSTEM_NAME}}` in `FileTree.md` were reviewed (no skips)
- [ ] LOC estimates use the ~2000 limit and TS-verbosity multiplier
- [ ] Runtime LOC excludes debug, test, and type-only files per the convention
- [ ] Output-boundary rule was applied to any web-rendering concerns
- [ ] File size risk bands match Low <1500 / Medium 1500–2000 / High >2000
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")
- [ ] Did not make architectural decisions; only observations

If any item is unchecked, stop and either complete it or flag what's missing as an open question.

---

## Output Disposition

The completed review is written to: `NCEMPIRE/{{SYSTEM_NAME}}/Pass0-Coverage.md`

It supersedes the previous Pass 0 document for this system (now archived at `legacy/pass-0-python-era/{{SYSTEM_NAME}}/Pass0-Coverage.md`).

---

*End of prompt template.*

---

### Review & Clarification Needed (about this draft prompt)

- Is the prompt's structure right — locked context order, runtime context section, output-boundary rule placement?
- Are the LOC exclusion patterns (`*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`) the right set, or do you want different / additional patterns?
- Should there be a separate prompt variant for systems that were SAFE-verdicted (like `integrations/`) vs CAN-verdicted, or is the same prompt fine?
- Should the pre-submission self-check be more or less stringent?
- Is the output disposition (write to `NCEMPIRE/{{SYSTEM_NAME}}/Pass0-Coverage.md`, supersede prior, archive prior) the right path?
- May this draft be promoted to canonical [PASS0PROMPT.md](PASS0PROMPT.md), or does it need revision first?
