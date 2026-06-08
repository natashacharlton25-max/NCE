# System Coverage Review — svg

## System Role Summary
The `svg` system creates and manipulates SVG (vector) graphics to produce scalable visual assets: drawing, path construction, transforms, boolean path ops, text parsing, geometry helpers, optimization, and export. The defining open question for this review is whether `svg` is intended as a **shared vector-primitive library** consumed by higher-level systems (notably `marks/`), or a standalone asset producer — its 8 subsystems read as low-level primitives, which strongly suggests the former.

## Coverage Assessment

> Assessment basis: all `svg/` system- and subsystem-level structured docs (`System.md`, `Index.md`, `Notes.md`, per-subsystem `Subsystem.md`/`Spec.md`/`Schema.md`) are **empty scaffolding templates**. The only seeded intent is the one-paragraph placeholder in each `<Subsystem>.md` module file (Status: Placeholder, v0.0.0, created 2026-01-21). Coverage below is therefore assessed against the system *intent* and the 8 named subsystems as briefed, not against written specs.

### Covered Responsibilities

| Responsibility | Covered By |
|---|---|
| Create / manipulate SVG graphics (elements, shapes) | SVGDrawer |
| Build SVG path data strings (command-level path construction) | SVGPathBuilder |
| Geometric transforms (translate / scale / rotate / skew / matrix) | SVGTransformEngine |
| Boolean path operations (union / intersect / subtract / XOR) | SVGBooleanOps |
| Parse SVG text elements (content, text-on-path, styling) | SVGTextParser |
| Geometry helpers (bounding boxes, path length, point math) | SVGGeometryUtils |
| Optimize / minify SVG (path simplification, attribute cleanup) | SVGOptimizer |
| Serialize / emit SVG output | SVGExporter (SVG-native output only — see Boundary Issues) |

### Partially Covered Responsibilities
- **SVG parsing / ingest (general).** `SVGTextParser` covers only `<text>` elements. There is no subsystem owning parse/deserialization of a full SVG document into an in-memory model (elements, attributes, the `d` attribute of `<path>`). `SVGBooleanOps`, `SVGTransformEngine`, and `SVGOptimizer` all logically require a parsed path/element representation as input — it is unclear which subsystem owns that. Possibly intended to live inside `SVGDrawer`, but unstated.
- **Path data model / shared geometry types.** Multiple subsystems (PathBuilder, BooleanOps, TransformEngine, GeometryUtils, Optimizer) operate on path/segment geometry, yet no single owner of the canonical path/segment/point type is identified. Risk of each subsystem defining its own.
- **Export to non-SVG formats.** `SVGExporter`'s placeholder claims PNG/PDF/rasterization, but raster and PDF output are owned elsewhere (`image/ImageRenderer`, `renderers/PDFRenderer`). Genuine SVG-native export (string/file serialization) is covered; cross-format export is mis-scoped, not covered here.

### Uncovered Responsibilities
- **SVG validation / well-formedness** (malformed input, unsupported elements, namespace correctness). No `Validation`-owning subsystem; per CLAUDE.md error categories exist but no module is named for structural validation. *(Not invented as a fix — flagged as a gap for human decision.)*
- **viewBox / coordinate-system & unit handling** (viewBox, preserveAspectRatio, user units vs px). Implied by transforms and geometry but not explicitly owned.
- **Styling / presentation attributes** beyond text (fill, stroke, gradients, clip/mask). Not named in any subsystem; may be out of scope by intent, but currently unstated.

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|---|---|---|---|---|
| SVGDrawer | Partial | Broad mandate ("create/manipulate"); likely the de-facto element model + parse home, but ownership of the in-memory SVG/document model is unstated. Could become a catch-all god-module. | 600–1000 | Medium |
| SVGPathBuilder | Sufficient | Well-scoped: programmatic emission of path command strings. Clear single responsibility. | 300–500 | Low |
| SVGTransformEngine | Sufficient | Matrix/affine math is bounded and well-understood. | 350–600 | Low |
| SVGBooleanOps | At-risk (scope) | Robust polygon/path boolean ops (curve flattening, intersection, winding, numerical robustness) are inherently large and algorithm-heavy; commonly delegated to a library. As hand-written, this is the most likely file to swell. | 800–1500 | **High** |
| SVGTextParser | Sufficient | Narrow `<text>`/`textPath`/styling parse scope. | 300–500 | Low |
| SVGGeometryUtils | Sufficient (scope) but **overlap** | Bounding box / path length / point math is bounded, BUT verb-overlaps `marks/GeometryValidator` and is a likely shared dependency of BooleanOps/Optimizer. Sufficiency contingent on resolving the marks boundary. | 400–700 | Low–Medium |
| SVGOptimizer | At-risk (scope) | Path simplification (Douglas–Peucker / curve fitting), attribute/structure cleanup, and numeric precision reduction is a deceptively large surface. Often library-backed (cf. SVGO). | 500–900 | Medium |
| SVGExporter | Insufficient as scoped | Placeholder over-claims raster/PDF conversion that belongs to `image/` and `renderers/`. As an SVG-native serializer it is small; as currently described it crosses two system boundaries. | 200–400 (SVG-native only) | Low |

## Missing Capability Areas
- **SVG document parse / in-memory model owner** — required by BooleanOps, TransformEngine, Optimizer; no subsystem explicitly owns full-document parsing (only text is covered). High impact on buildability.
- **SVG structural validation** — no owner; relevant to the global `Validation` error category.
- **Shared path/geometry data-type ownership** — no canonical type owner across the five geometry-touching subsystems.
- (viewBox/units and styling-attribute handling noted above as partial/uncovered; flagged, not resolved.)

## Boundary & Classification Issues
- **svg ↔ marks (geometry) — OVERLAP, needs ruling.** `svg/SVGGeometryUtils` *computes* geometry (bounding box, path length, point math); `marks/GeometryValidator` *validates* mark geometry (alignment, symmetry, consistency). Verbs differ (compute vs validate), so not a strict duplicate — but `GeometryValidator` will almost certainly need `SVGGeometryUtils` primitives. Likely relationship: `svg` is the **shared primitive library**, `marks` a **consumer**. Needs explicit dependency-direction decision (svg must not depend on marks).
- **svg ↔ marks (text→path) — ADJACENT, not duplicate.** `marks/FontToPathConverter` (font glyph → vector outline) is distinct from `svg/SVGTextParser` (parse existing `<text>`) and `svg/SVGPathBuilder` (emit path strings). FontToPathConverter is plausibly a `marks` consumer of `SVGPathBuilder`. No merge implied; confirm direction.
- **svg ↔ marks (kerning) — CONSUMER.** `marks/KerningGeometryEngine` (optical letter-spacing math for logos) is logo-domain logic, not generic SVG; it would *consume* svg geometry, not live in svg. Keep separate.
- **svg/SVGExporter ↔ image/ ↔ renderers/ — MIS-SCOPED.** SVGExporter's placeholder claims SVG→PNG/PDF + "rasterization." But `image/ImageRenderer` owns PNG/JPG raster (Sharp/Canvas), `image/AssetOptimizer` owns raster optimization, and `renderers/PDFRenderer` owns PDF (PDFKit). SVGExporter should own **SVG-native serialization only**; raster/PDF conversion crosses into `image/` and `renderers/`. Recommend de-scoping (decision for human).
- **svg ↔ renderers (logic-free rule).** Renderers must remain logic-free (CLAUDE.md §4). SVG geometry/boolean/optimize logic correctly lives in `svg`, not in renderers — consistent. Only watch that SVGExporter does not absorb renderer responsibilities.
- **svg ↔ image (vector vs raster).** Clean split is correct: `svg` = vector, `image` = raster. The only leak is via SVGExporter (above).
- **Global constraints.** No brand/access/resilience decisions are embedded in svg subsystems (correct — they belong to `brand/`, `access/`, `resilience/`). Error categories use the standard set. No violations observed at this scaffold stage.

## Overall Build Size Estimate
Sum of conservative midpoints across 8 subsystems ≈ **3,450–6,500 LOC** for the svg system (TypeScript; excludes `*.test.ts`, `*.types.ts`, `*.debug.ts` per CLAUDE.md). Concentrated risk in **SVGBooleanOps** (up to ~1500, High) and secondarily **SVGOptimizer**. If BooleanOps and Optimizer are library-backed rather than hand-rolled, the total and the High-risk flag both drop materially — a library-vs-hand-written decision is the single biggest size lever and is currently unmade.

## Risk Assessment
- **Overall risk: Medium-High.**
- **Size risk:** One subsystem (SVGBooleanOps) projects into the High band (≥~1500 LOC) if implemented in-house; SVGOptimizer is a secondary swell risk. File-size is a design signal here, not yet a violation.
- **Boundary risk (primary):** the svg↔marks shared-library direction and the SVGExporter raster/PDF mis-scope are unresolved and would cause cross-system drift / duplicated geometry code if left undecided.
- **Coverage risk:** missing owner for full-SVG parse and for the canonical path/geometry data type — these are upstream dependencies of 4–5 subsystems and could block coherent spec-filling.
- **Documentation maturity:** all structured specs are empty templates; intent exists only as one-line module placeholders. PASS 0 can proceed on intent, but PASS 1+ is blocked until the boundary and library decisions are made by a human.

## Verdict
**CAN** fulfil its role as currently structured — the 8 subsystems are individually well-aimed and collectively cover the create/manipulate/transform/optimize/export intent for vector assets — **conditional on** the following being resolved by a human before PASS 1:
1. Declare svg as a **shared vector-primitive library** and fix the dependency direction `marks → svg` (svg must not depend on marks); resolve `SVGGeometryUtils` vs `marks/GeometryValidator` ownership.
2. Assign an explicit owner for **full-SVG document parse** and the **canonical path/geometry data type** (likely SVGDrawer or SVGGeometryUtils — decision required, not assumed).
3. **De-scope SVGExporter** to SVG-native serialization; route PNG/raster to `image/` and PDF to `renderers/`.
4. Decide **library-backed vs hand-written** for SVGBooleanOps and SVGOptimizer (drives the High size risk).

**Status:** Draft Complete – Awaiting Review
