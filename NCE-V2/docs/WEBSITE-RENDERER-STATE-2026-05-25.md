# Website Renderer — State Snapshot (2026-05-25)

**Type:** Point-in-time factual snapshot of an external project, recorded for NCE-V2's reference.
**Source:** Website project Claude Code session report, relayed by Natasha.
**Captured into NCE-V2:** 2026-05-25.
**Note:** This is NCE-V2's record of a relayed report; it is **not** an NCE-V2-authored specification of the website. The website is a separate project with its own canonical sources. This snapshot may go stale (the briefing itself reported 8 of 17 atoms mid prop-rename audit) — when staleness matters, re-fetch from the website project rather than rely on this file. NCE-V2 references this as factual context for design decisions on its own side (notably the Parsers atom-list direction in `admin/TIER-2-PREP-NOTES.md`); it does not modify the website.

---

## Website renderer — current factual state

**Source files (paths in the website repo):**

- `src/components/Renderer.astro` — the universal render entry point
- `src/lib/render.ts` — component registry + resolveNode lookup
- `src/lib/schema-validator.ts` — DEV-mode per-instance schema validator
- `scripts/validate-data.ts` — build-time validator over all data JSON

**JSON shape the renderer expects** (canonical, every test page uses this):

```json
{
  "component": "Page",
  "children": [
    {
      "component": "Section",
      "container": "7xl",
      "children": [
        { "component": "Heading", "level": 2, "contentHeading": "Hello" },
        { "component": "Text", "contentText": "Body copy here." }
      ]
    }
  ]
}
```

Each node is a flat object with:

- `"component"` — string, name of a registered component
- `...props` — any other keys, flat (no grouped content/visual/animation blocks; that's schema-side organisation only)
- `"children"` — optional array of more nodes, recursed by the renderer

Two keys are structural and stripped before props are spread to the component: `component`, `children`. Everything else passes through.

**The mapping mechanism — single registry, name → Astro component:**

```ts
// src/lib/render.ts
export const componentRegistry = {
  Page, Section, Grid, Heading, Text, Badge, Button, Card,
  Icon, Image, Link, List, LottieIcon, Tooltip, FormField, Shape, Burst,
};
```

`resolveNode(node, registry)` looks up `registry[node.component]`, returns `{ Component, props, children }`. `Renderer.astro` recurses on `children` via `<Astro.self node={child} />`. **If the name isn't registered, the node renders as nothing.**

**Currently registered component types (17 atoms):**

| Component | Notes |
|---|---|
| Page | Top-level page wrapper |
| Section | Container with optional separator, gap, container-width, background |
| Grid | Layout primitive (column/row spans) |
| Heading | h1–h6 + level prop, gradient/typography/colour props |
| Text | Body text, with size/weight/align/family props |
| Badge | Inline label, variants (fill/outline/glass), shapes, sizes |
| Button | Action / link, many variants and effects |
| Card | Container card |
| Icon | API-backed Phosphor icons (5 weights × variants) |
| Image | Photos / illustrations, with alt-text variants |
| Link | Inline text link, variants (default/bare/underline/highlight/border/etc.) |
| List | ul/ol with marker variants (default/numbered/none/inline/dot) |
| LottieIcon | Animated icon with static fallback |
| Tooltip | Hover/focus contextual display, label or info purpose |
| FormField | All form input types (text/email/textarea/checkbox/radio/toggle/select/card-select/etc.) |
| Shape | SVG shape primitive, gradient/draw/morph capable |
| Burst | Particle burst effect |

Also rendered via the same pipeline (not in atom registry but composed by atoms): **FigCaption** (CSS+JS-only, rendered by Image when alt-text props are set and `html` attribute toggled).

**Schema validation at render-time:**

In DEV mode, `Renderer.astro` loads every atom's `<Component>.schema.json` and calls `validateComponent(node, schema)` per instance. Errors fail the render with an Astro overlay; warnings log to terminal. The same validator powers a build-time check (`npm run validate:data`) that walks every `src/data/**/*.json` file pre-build.

**What the renderer does NOT do:**

- It does not emit atoms or modify content. Pure consumer.
- It does not have a "block type" intermediary — `component` is the type, mapped 1:1 to an Astro component.
- It does not transform JSON shape (no markdown parsing, no MDX). Authors hand-author or generate the JSON tree as-is.
- It does not interpret semantic intent — that lives in atom-side props (e.g. Image's `semanticRole`, Icon's `labelIcon`, FormField's `labelForm`, content atoms' `content<Atom>` prop family).

**Cross-atom pipeline defaults** (one current rule):

When a Button has an Icon or LottieIcon in its media slot, the renderer auto-sets `noExplainer: true` on the nested icon (chrome icons skip the heavy AAC explainer overlay). Author can override per-instance.

**Audit status:**

The renderer's mechanism is stable. Of the 17 registered atoms, 9 have completed a content-rename audit pass (Badge, Button, FigCaption, FormField, Heading, Link, List, Text, Tooltip) — their props use the per-atom naming convention (`content<Atom>` for visible content, `label<Atom>` for screen-reader-only labels). The other 8 atoms register and render fine but their schemas use older prop names; they'll be brought in line over upcoming audit passes. This affects which prop names appear in their current schemas, not whether the renderer can route to them.
