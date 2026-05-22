# ASTRO-INVENTORY-BRIEF.md

**Status:** Brief for Claude Code
**Type:** Inventory request — read-only, no implementation
**Generated:** 2026-05-18
**Purpose:** Verify 18 specific Astro platform capability claims used in REFINEMENTS.md

---

## Why This Brief Exists

REFINEMENTS.md §1.1–§1.5 makes 18 specific claims about the Astro website platform's current state. These claims drive recommendations to narrow or remove NCEMPIRE systems (`colours/`, `typography/`, `website/`, `visual/`, `verification/`). The recommendations only hold if the Astro claims are accurate.

The claims were not verified before being written. This brief asks Claude Code to produce a factual inventory of what the Astro platform **actually contains right now**, so REFINEMENTS.md v1.1 can rest on grounded evidence rather than assumed state.

This is **inventory work only**. No architectural decisions. No implementation. No recommendations. Just: "what is in the codebase, today, that supports or refutes each claim?"

---

## Scope and Boundaries (Mandatory)

### Claude Code MUST

- Read files in the Astro project (read-only)
- Count specific things (themes, variants, validator rules, etc.)
- Report findings factually with file paths and line numbers as evidence
- Mark each claim **Built / Partial / Aspirational** based on evidence
- Stop and ask if any claim is genuinely ambiguous from the codebase

### Claude Code MUST NOT

- Edit any files in the Astro project
- Edit any files in the NCEMPIRE project
- Edit REFINEMENTS.md directly — output goes to a new file for review
- Run code, scripts, builds, or tests beyond what's needed to count
- Make architectural recommendations
- Infer "what should exist" — only report "what does exist"
- Skip any claim — every one of the 18 below must be addressed
- Treat partial evidence as full evidence — when in doubt, mark Partial with a note

---

## Output Format

Produce a single file: `ASTRO-INVENTORY.md` in the project root (or wherever the project's review documents currently live). Format:

```markdown
# ASTRO-INVENTORY.md

**Status:** Inventory Complete – Awaiting Review
**Generated:** [date]
**Verifier:** Claude Code
**Astro project location:** [path to the Astro codebase that was inventoried]

## Claim 1: OKLCH-first colour engine
**Status:** Built | Partial | Aspirational
**Evidence:**
- [File path:line] [what was found]
- [File path:line] [what was found]
**Notes:** [any ambiguity, caveats, or context]

## Claim 2: 114 themes built
**Status:** [status]
**Count found:** [actual number]
**Evidence:** [file paths showing theme definitions]
**Notes:**

[…repeat for all 18 claims…]

## Summary
| # | Claim | Status |
|---|-------|--------|
| 1 | OKLCH-first colour engine | [status] |
| 2 | 114 themes built | [status, with actual count] |
| … | … | … |

## Open Questions
[Any claims that couldn't be cleanly resolved and need human input]
```

---

## The 18 Claims to Verify

Per REFINEMENTS.md §1.1–§1.5:

### Colour system (§1.1)

**Claim 1: OKLCH-first colour engine.** Look for: OKLCH colour-space usage in the theme engine, conversion from OKLCH to sRGB/P3 outputs, OKLCH as the authored input format. Files likely under `src/lib/`, `src/styles/`, or wherever the theme engine lives.

**Claim 2: 114 themes built.** Count actual theme definitions in whatever format they're stored (JSON, JS, CSS, etc.). Report exact number found, not approximation.

**Claim 3: 1,852+ WCAG-audited theme variants.** Look for: variant files, generated variant catalogues, or audit output files. Report exact count if findable. If the number comes from a runtime calculation rather than stored files, note that.

**Claim 4: Dual contrast auditing (WCAG 2.x + perceptual ΔE).** Look for: contrast calculation code, both WCAG-formula and ΔE-formula implementations, where they're called.

**Claim 5: CVD-safe variant generation.** Look for: protan / deutan / tritan adjustments, colour-blindness simulation code, CVD-tagged theme variants.

**Claim 6: Gamut-clamp hue preservation.** Look for: gamut-clamping code (typically in colour-space conversion), hue-preservation logic distinct from lightness/chroma clamping.

### Typography (§1.2)

**Claim 7: rem-based composable type system with multipliers.** Look for: rem-based type scale, multiplier system that composes scale × user-setting × context, anywhere the multiplier chain is defined.

**Claim 8: Comfort settings (per-user reading-comfort configuration).** Look for: comfort-setting state, persistence (localStorage / cookies / similar), CSS variables driven by comfort settings. Count the actual number of independently-toggleable settings.

**Claim 9: Render-mode-aware sizing.** Look for: render-mode enum or similar, sizing rules conditional on render mode, whether render-mode affects type scale.

### Website composition (§1.3)

**Claim 10: JSON-as-page composition system.** Look for: page-level Astro files that read from JSON, JSON files that define page structure, the actual ratio of Astro file size to JSON file size (claim implied earlier that Astro files are "~3 lines").

**Claim 11: Multiple render modes.** Look for: render-mode definitions, what each mode does. Count and list found modes.

**Claim 12: Brand templates as build-time primitives.** Look for: brand template definitions, how they're consumed at build time, whether the build branches on brand template input.

**Claim 13: Accessibility-by-construction at component level.** Look for: component-level patterns that enforce accessibility (semantic HTML defaults, alt-text requirements, focus management, etc.), whether these are enforced or merely encouraged.

### Visual validator (§1.4)

**Claim 14: Render-layer validator running accessibility + visual-rule checks.** Look for: a validator that runs at render time (build or request), accessibility checks specifically, visual-rule checks (banned patterns, contrast, etc.).

**Claim 15: CVD safety enforced at render.** Look for: CVD-specific validation gates, whether render fails or warns on CVD-unsafe combinations.

### Verification (§1.5)

**Claim 16: 34-rule validator that gates every change.** Look for: the validator script(s), count actual rules implemented, evidence that it gates changes (CI integration, pre-commit hook, build-fail behaviour).

**Claim 17: Banned-pattern enforcement.** Look for: banned-pattern definitions (hardcoded colours, !important, @layer, component-level dark selectors, fallback values in CSS custom property calls — all named in earlier conversation), validator code that catches them.

**Claim 18: Structural-correctness enforcement.** Look for: structural rules (e.g. JSON schema validation, atom-prop type-checking, composition-pattern enforcement), validator code that catches violations.

---

## How to Mark Each Claim

**Built** — The claim is accurate. Specific evidence exists in the codebase. Number-claims (114 themes, 34 rules) match the count found or are within ±5%.

**Partial** — Part of the claim is accurate; part is not. Use when:
- A capability exists but is less complete than claimed (e.g. "OKLCH used in some places, sRGB elsewhere")
- A count is significantly off (e.g. "claim says 114, found 47")
- A feature exists but isn't enforced (e.g. "accessibility patterns documented but not validator-checked")

**Aspirational** — The claim has no implementation evidence. Use when:
- No files matching the capability exist
- Comments or docs mention the capability but no code implements it
- The capability was designed but not built

**Mark with confidence:** if you're unsure whether something is Built vs Partial, mark Partial with a note explaining the uncertainty. Don't overclaim.

---

## Process

1. Read this brief
2. Confirm you can read the Astro project (state the path)
3. Confirm you cannot or will not write to it
4. Work through the 18 claims in order
5. Produce `ASTRO-INVENTORY.md` per the format above
6. Stop and mark **Inventory Complete – Awaiting Review**
7. Do not proceed to update REFINEMENTS.md — that's a separate task after human review of the inventory

---

## What Happens Next (Out of Scope for This Brief)

After this inventory:

1. Human reviews `ASTRO-INVENTORY.md`
2. Decides per claim whether the original REFINEMENTS.md recommendation stands, is revised, or is dropped
3. REFINEMENTS.md v1.1 is produced grounded in the inventory results
4. REVIEW-QUESTIONS.md is produced against the corrected v1.1
5. Pass 0 clean-slate cycle begins

None of those steps are this brief's job. This brief produces evidence; downstream decisions use it.

---

## Self-Check (Required Before Submitting)

Before marking the inventory complete, confirm:

- [ ] All 18 claims addressed (no skips)
- [ ] Each claim has Built / Partial / Aspirational marked
- [ ] Each claim has file-path-and-line evidence (not vague references)
- [ ] Counts are exact, not approximated, where countable
- [ ] No edits made to any file outside `ASTRO-INVENTORY.md`
- [ ] No architectural recommendations included
- [ ] Open questions section captures genuine ambiguities

If any item is unchecked, stop and either complete it or flag what's missing as an open question.

---

### Review & Clarification Needed
- Is the scope of the inventory clear?
- Are the 18 claims correctly translated from REFINEMENTS.md §1.1–§1.5?
- Is the output format usable for the downstream v1.1 work?
- May the inventory proceed against this brief, or does it need revision first?

---

*Created: 2026-05-18*
*Type: Brief for Claude Code, read-only inventory*
*Status: Draft Complete – Awaiting Review*
