# System Coverage Review — visual

## System Role Summary

`visual/` is intended to manage visual brand identity, run visual brand-compliance checks, and coordinate visual assets across systems. As scoped, three of its four subsystems describe *brand-owned data and decisions* (identity, values, logos, colours) rather than a distinct visual concern, placing the system in direct tension with the CLAUDE.md §4 rule that brand decisions belong to `brand/` and that each module belongs to exactly one authoritative system.

## Coverage Assessment

The four subsystems do cover the four responsibilities named in the system intent — but coverage is *nominal*. In nearly every case the responsibility is already owned (or co-owned) by an established authority system (`brand/`, `marks/`, `colours/`, `checks/`, `image/`), so "covered here" and "covered correctly" diverge. This is a coverage review, not a redesign, so the table records where each responsibility is nominally placed and flags the authority collision separately under Boundary & Classification Issues.

### Covered Responsibilities

| Responsibility | Covered By |
|---|---|
| Manage visual brand identity (logos, colours, visual guidelines) | VisualBrand (nominal) — but logos owned by `marks/`, colours by `colours/`, identity by `brand/identity/` |
| Validate visual brand compliance | VisualCheck (nominal) — sits alongside `checks/` LayoutCheck/ValuesCheck; self-described as a PostCreationCheckManager checker |
| Coordinate visual assets across systems | VisualManager (nominal) — coordinator over colour/image/visual-brand data owned elsewhere |
| Ensure visuals align with brand values | VisualValueAlignment (nominal) — values owned by `brand/values/`, alignment by `brand/alignment/`, checking by `checks/ValuesCheck` |

### Partially Covered Responsibilities

- **Visual-asset coordination scope is undefined.** VisualManager says it "coordinates visual design functionality across colour, image, and visual brand modules" but no contract states *what* it coordinates (resolution? caching? selection? handoff?). Without a defined coordination surface it cannot be sized or distinguished from a passthrough.
- **VisualCheck's relationship to the check pipeline is asserted, not owned.** VisualCheck.md states it "runs as part of PostCreationCheckManager (in parallel with other checkers)" and is "distinct from LayoutCheck" — i.e. it is described as a peer of `checks/` modules, but it lives outside `checks/`. The compliance responsibility is only partially covered because its home system is contested.
- **"Visual guidelines"** appears under VisualBrand with no definition of whether visual/ authors guidelines or merely consumes brand-authored ones.

### Uncovered Responsibilities

- **No source-of-truth statement.** Nothing in visual/ states whether visual/ *holds* visual brand data or *references* brand-owned data. This is the single most important uncovered responsibility for a system whose name collides with `brand/`.
- **No defined inputs/outputs or feed contracts.** All `Subsystem.md`, `Spec.md`, `Schema.md`, `Index.md`, and `System.md` files are unfilled templates; only the `<Name>.md` overview stubs carry content. Error domains, dependencies, and guarantees are entirely uncovered.

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|---|---|---|---|---|
| VisualBrand | Insufficient / Redundant-risk | "Logos, colours, visual guidelines" are owned by `marks/`, `colours/`, and `brand/identity/`. As a *store* it violates one-authoritative-system; as a *view* over brand data it has near-zero distinct logic. No distinct responsibility is established. | 150–400 | Low |
| VisualCheck | Sufficient-as-a-checker, mis-located | Has the most concrete content (check list, thresholds, output shape). A genuine, distinct validation concern — but self-describes as a `checks/`/PostCreationCheckManager peer, so its sufficiency argues for placement *in `checks/`*, not for `visual/` as a system. | 300–600 | Low |
| VisualManager | Insufficient | Pure coordinator with no defined coordination contract. Risks being a passthrough over colour/image/visual-brand. Cannot be sized meaningfully until its job is specified. | 150–400 | Low |
| VisualValueAlignment | Insufficient / Redundant-risk | Directly duplicates `checks/ValuesCheck` (validate value alignment) and depends wholly on `brand/values/` + `brand/alignment/`. "Visuals align with values" is a narrowing of an existing brand+checks concern, not a new authority. | 150–400 | Low |

## Missing Capability Areas

- **Authority/ownership declaration** — which (if any) visual data visual/ owns vs. references. Without it the system cannot be specced without breaching §4.
- **Coordination contract for VisualManager** — the actual orchestration surface (what is coordinated, in what direction, with what guarantees).
- **Relationship contract to the check pipeline** — whether VisualCheck is registered in `checks/` or duplicated here.
- (All standard PASS 1/2 artefacts — non-goals, dependencies, feeds, error domains — are absent, but that is expected at PASS 0 and not counted as a capability gap.)

## Boundary & Classification Issues

- **visual/ ↔ brand/ authority collision (PRIMARY).** CLAUDE.md §4: "Brand decisions belong to `brand/`" and "each module belongs to one authoritative system." VisualBrand ("visual brand identity") and VisualValueAlignment ("align with brand values") describe brand-owned decisions and brand-owned data (`brand/identity/BrandIdentityResolver`, `brand/values/BrandValuesManager`, `brand/alignment/BrandAlignmentEngine`). visual/ must be classified as **either a logic-free coordinator/view over brand-owned data, or it is in breach.** It cannot be an authority over visual brand identity.
- **VisualBrand ↔ marks/ + colours/.** "Logos" is owned by `marks/` (LogoComposer, LogoRules, LogoVariantGenerator, IconGenerator, MarkCatalog…); "colours" by `colours/` (ColourMaker, ColourMix, ColourToken). VisualBrand storing these duplicates two dedicated authority systems.
- **VisualValueAlignment ↔ checks/ValuesCheck + brand/values + brand/alignment.** `checks/ValuesCheck` already "validates content reflects brand values"; `brand/alignment/` already owns alignment scoring/drift. VisualValueAlignment is a visual-scoped slice of an existing, already-staffed concern — MERGE candidate.
- **VisualCheck ↔ checks/ (+ verification/).** VisualCheck.md positions itself as a sibling of `checks/LayoutCheck` and a `PostCreationCheckManager` checker. Every other such checker lives under `checks/`. Classification question: should VisualCheck be a `checks/` module rather than a `visual/` subsystem? (verification/ overlap is lighter — that system runs AccessibilityValidator/ContentIntegrityValidator, adjacent but distinct.)
- **VisualManager ↔ image/ + colours/.** Coordinating "visual assets" overlaps `image/` (ImageAssetLibrary, AssetOptimizer, ImageRenderer). If coordination means asset selection/optimisation, that is `image/`'s domain.
- **Net classification flag:** On current scope, visual/ reads as a **coordinator/aggregator layer, not an authority.** If confirmed as coordinator-only, VisualBrand and VisualValueAlignment are strong **MERGE-into-brand** candidates and VisualCheck a strong **MERGE-into-checks** candidate, leaving at most a thin VisualManager — which itself may not justify a standalone system. This is a decision for the human architect, surfaced here per CLAUDE.md §6 as non-binding options, not a ruling.

## Overall Build Size Estimate

Total estimated build: **~750–1,800 LOC** across the four subsystems if built as currently scoped (coordinator/view layer). No subsystem approaches the 1,500-LOC High-risk threshold; the largest plausible file is VisualCheck at ~300–600 LOC. **Size risk is uniformly Low.** The dominant risk is *not* size — it is duplication: a meaningful fraction of this LOC would re-implement logic already owned by brand/, marks/, colours/, and checks/, so the "true new build" net of redundancy could be far smaller (potentially near-zero for VisualBrand/VisualValueAlignment).

## Risk Assessment

- **Overall risk: HIGH** — driven by boundary/authority collision, not by file size.
- **Authority breach risk (High):** VisualBrand and VisualValueAlignment cannot be specced as authorities without violating CLAUDE.md §4 (brand decisions → brand/; one authoritative system per module).
- **Redundancy risk (High):** three of four subsystems duplicate established systems (brand/, marks/, colours/, checks/).
- **Specability risk (Medium):** VisualManager has no coordination contract and cannot be sized or specced as-is.
- **Size risk (Low):** no file projected near 1,500 LOC.
- **Documentation risk (informational):** all structural docs are empty templates; only overview stubs carry content. Expected at PASS 0, noted for context.

## Verdict

**CANNOT** fulfil its stated role as currently structured — *as an authority over visual brand identity and visual value alignment*. Doing so collides head-on with brand/ (CLAUDE.md §4) and duplicates marks/, colours/, and checks/.

It **CAN** be viable **only** under explicit conditions, to be decided by the human architect (non-binding options, not selected here):

1. **Authority is declared:** visual/ is confirmed as a **logic-free coordinator/view over brand-/marks-/colours-owned data**, owning no visual brand decisions itself.
2. **VisualBrand and VisualValueAlignment** are resolved against brand/ (identity/values/alignment) — confirmed as references/MERGE candidates, not stores/authorities.
3. **VisualCheck's home** is decided: remain in visual/ or relocate to `checks/` alongside its declared PostCreationCheckManager peers.
4. **VisualManager's coordination contract** is defined (what it coordinates, direction, guarantees) so it can be sized and distinguished from a passthrough.

Until conditions 1–4 are resolved, this system should not advance to PASS 1.

**Status:** Draft Complete – Awaiting Review

---
### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Are there any corrections or refinements required?
- May I proceed, or should I revise this first?
---
