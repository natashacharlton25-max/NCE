# System Coverage Review — website

## System Role Summary
The `website` system **generates website content** — composed pages, sections, SEO data, page/social metadata, sitemaps, web-format constraints, web-content lifecycle, audience personalization, and analytics wiring. Two framing facts govern this review: (1) `website` is **built in a separate repo** (out of the main NCEMPIRE spec-filling track), and (2) it is **distinct from the Astro website platform that consumes NCEMPIRE output** — per STACK-PROPOSAL the Astro hosting/serving platform is out of scope. NCEMPIRE's responsibility **ends at validated web content in storage**; it does not host, serve, or render the live site.

## Coverage Assessment

> Assessment basis: every `website/` system- and subsystem-level structured doc (`System.md`, `Index.md`, `Notes.md`, `Admin.md`, and each subsystem's `Subsystem.md` / `Spec.md` / `Schema.md` / `Repositorys.md` / `Libraries.md` / `Pass0.md`) is an **empty scaffolding template**. The only seeded intent is the one-paragraph placeholder in each `<Subsystem>.md` module file (Status: Placeholder, v0.0.0, created 2026-01-21). Coverage below is assessed against the briefed system *intent* and the 9 named subsystems, not against written specs. No subsystems are invented or renamed; no fixes/schemas are proposed.

### Covered Responsibilities

| Responsibility | Covered By |
|---|---|
| Compose complete web pages from sections (ordering, layout, content flow) | PageComposer |
| Build individual page sections (hero, features, testimonials, CTA, etc.) | SectionBuilder |
| SEO data & optimization (meta tags, JSON-LD structured data, keywords, scoring) | SEOManager |
| Page metadata (Open Graph, Twitter cards, canonical URLs, social preview data) | MetadataBuilder |
| Web-format constraints (char limits, responsive requirements, accessibility checks) | WebConstraints |
| Sitemap generation & maintenance (URLs, priorities, change frequency) | SitemapManager |
| Web-content lifecycle (draft → published → archived; version history; rollback) | WebsiteLifecycle (overlaps content/ — see Boundary Issues) |
| Audience personalization of web content (segments, dynamic content, A/B variants) | WebPersonalizer (overlaps content/ — see Boundary Issues) |
| Web analytics integration (GA, tracking pixels, event-tracking setup) | WebAnalyticsAdapter (boundary with integrations/ — see Boundary Issues) |

### Partially Covered Responsibilities
- **URL / routing / slug ownership.** SitemapManager *tracks* URLs and MetadataBuilder *emits* canonical URLs, but no subsystem is named as the **owner of URL/slug generation and routing policy**. Multiple subsystems consume URLs; ownership of the canonical URL/slug is unstated. Flagged, not resolved.
- **Page/section data model & shared web-content types.** PageComposer (page structure), SectionBuilder (section content mapping), and WebsiteLifecycle (page state/version history) all operate on a page/section model, yet no single owner of the canonical page/section type is identified. Risk of each defining its own.
- **"Section rendering" vs composition.** SectionBuilder's placeholder claims "section rendering" and PageComposer claims "page structure." Genuine *composition* (selecting/ordering/mapping content into a structured page model) is covered; *rendering to HTML* is owned by `renderers/HTMLRenderer` and must stay there (logic-free rule). The placeholders' "rendering" wording over-reaches — composition is covered, rendering is not (and should not be) owned here.
- **SEO ↔ metadata split.** SEOManager ("generate SEO meta tags") and MetadataBuilder ("Open Graph / Twitter / page metadata") both produce `<head>`-level tags. The split (SEO/structured-data vs social/OG metadata) is plausible but the exact ownership line for the shared meta-tag surface is unstated — partial overlap.

### Uncovered Responsibilities
- **Web-content validation / well-formedness gate.** WebConstraints checks limits/responsive/accessibility, but there is no explicit owner for the **final "validated web content in storage"** gate that the system role hinges on (the hand-off point to the separate Astro consumer). Whether WebConstraints *is* that gate, or whether it sits in `checks/` / `verification/`, is unstated.
- **Internationalization / localization** (hreflang, locale variants, translated metadata). Not named in any subsystem; may be out of scope by intent, but currently unstated.
- **Persistence / storage of generated web content.** The role ends "at validated content in storage," but no website subsystem owns the storage write; presumably `services/` / `state/`. Out-of-system by design, but the hand-off owner is unstated.

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|---|---|---|---|---|
| PageComposer | Partial | Core composition role is well-aimed, but is the likely de-facto home of the page/section data model and content-flow orchestration; risk of becoming a catch-all "god" composer. Must not absorb HTML rendering (renderers/). | 500–900 | Medium |
| SectionBuilder | Partial | Section-type construction is bounded, but "section rendering" wording leaks into renderer territory; many section types (hero/features/testimonials/CTA/...) can each grow templates. Sufficient if scoped to building a section *model*, not rendering. | 500–900 | Medium |
| SEOManager | Sufficient (scope) but overlap | Meta-tag + JSON-LD + scoring is a well-understood, bounded surface. Overlaps MetadataBuilder on the shared `<head>` tag surface; sufficiency contingent on resolving that split. | 400–700 | Low–Medium |
| MetadataBuilder | Sufficient (scope) but overlap | OG / Twitter / canonical metadata is narrow and well-defined. Same shared-meta-tag overlap with SEOManager. | 250–450 | Low |
| WebConstraints | Partial | Char-limit/responsive checks are simple; **accessibility (WCAG) compliance** is a deceptively large, rule-heavy surface and is the swell risk here. Also unclear if this is the final validation gate (see Uncovered). | 400–800 | Medium |
| SitemapManager | Sufficient | XML sitemap generation, URL/priority/changefreq tracking is bounded and well-understood. Depends on an unstated URL owner. | 250–450 | Low |
| WebsiteLifecycle | At-risk (duplication) | Draft→published→archived + versioning/rollback **duplicates content/ContentLifecycleManager** (which already owns draft→QA→ready→published→archived→retired) and overlaps `versioning/`. Sufficiency contingent on a keep/merge/delegate ruling. | 400–800 | Medium |
| WebPersonalizer | At-risk (duplication) | Audience adaptation + variants **overlaps content/ContentPersonalizer**; adds A/B-testing variant management not present there. Distinct *enough* (web variant serving vs content adaptation) to possibly survive, but the boundary is unset. | 400–800 | Medium |
| WebAnalyticsAdapter | At-risk (boundary) | GA/pixel/event-tracking *setup* embedded into web content is a reasonable web concern, but holding its own API client would duplicate `integrations/` (auth/retry/rate-limit) and overlaps `observability/` on the word "analytics." Should route external calls through integrations/; analytics-config injection only. | 300–600 | Low–Medium |

## Missing Capability Areas
- **Final web-content validation / storage hand-off owner** — the "validated content in storage" boundary (end of NCEMPIRE, start of Astro consumer) has no clearly named owner; WebConstraints may or may not be it. High impact on buildability and on defining the system's outer edge.
- **URL / slug / routing-policy owner** — consumed by Sitemap, Metadata, PageComposer; no canonical owner.
- **Shared page/section data-type owner** — no canonical type owner across PageComposer / SectionBuilder / WebsiteLifecycle.
- **i18n / localization** — noted above as uncovered; flagged, not resolved.

## Boundary & Classification Issues
- **SEPARATE REPO (process boundary).** `website` is built in a separate repository and is excluded from the main spec-filling order. PASS 0 can proceed on intent here, but coordination/versioning across the repo split is itself an unmanaged risk (shared types, lifecycle, and URL ownership must be agreed across repos).
- **ASTRO CONSUMER (system boundary) — OUT OF SCOPE.** This system *generates web content*; the Astro platform that *consumes* it (hosts/serves/renders the live site) is explicitly out of scope per STACK-PROPOSAL. NCEMPIRE's `website` must end at **validated content in storage** — it must not absorb hosting, serving, runtime rendering, or build-time site generation. No website subsystem currently claims those, which is correct; the risk is scope-creep into the Astro layer over time.
- **website ↔ renderers/HTMLRenderer — LOGIC-FREE RULE.** HTMLRenderer explicitly "outputs HTML files... for web content" and must remain **logic-free** (global rule). PageComposer/SectionBuilder placeholders use "rendering" wording — composition logic must live in `website`, HTML serialization in `renderers/`. Watch that SectionBuilder/PageComposer do not pull rendering into the website system, and that HTMLRenderer holds no composition logic. Needs an explicit composition-vs-render line.
- **website/WebsiteLifecycle ↔ content/ContentLifecycleManager — OVERLAP / DUPLICATION.** ContentLifecycleManager already owns the full content lifecycle state machine (draft→QA→ready→published→archived→retired) plus stage notifications and reports. WebsiteLifecycle re-states draft→published→archived + versioning/rollback for web content. Verbs largely duplicate. Likely intended relationship: WebsiteLifecycle **delegates to / specializes** ContentLifecycleManager, or is merged. Also overlaps `versioning/`. Needs a keep / merge / delegate ruling (decision for human — not made here).
- **website/WebPersonalizer ↔ content/ContentPersonalizer — OVERLAP.** ContentPersonalizer adapts tone/vocabulary/examples/complexity/accessibility of generated content; WebPersonalizer personalizes web content per audience + A/B variants. Adjacent and partly duplicative. Distinction (content-level adaptation vs web variant serving / A-B) is plausible but unstated. Needs a boundary ruling.
- **website/WebAnalyticsAdapter ↔ integrations/ — DEPENDENCY DIRECTION.** integrations/IntegrationAdapter + GoogleIntegration own external-API auth/retry/rate-limit/normalization. WebAnalyticsAdapter should **route GA/pixel calls through integrations/**, owning only the analytics-config injection into web content — not its own API client. Direction must be `website → integrations` (no duplicated client).
- **website/WebAnalyticsAdapter ↔ observability/ — NAME OVERLAP, NOT DUPLICATE.** observability/MetricsCollector collects **internal system** metrics (timings/counts/throughput). WebAnalyticsAdapter wires **end-user web analytics** (GA, pixels) into shipped content. Different domains; not a merge — but the shared word "analytics" invites confusion; keep separate and label clearly.
- **website ↔ content/Content (old content/WebsiteContent?) — SUPERSESSION CHECK.** The brief asks whether old `content/WebsiteContent` is superseded by this system. No `WebsiteContent` subsystem exists under `content/` in the current tree (content/ holds Content, ContentManager, ContentLifecycleManager, ContentPersonalizer, etc.). If a prior `WebsiteContent` notion existed, it appears **already superseded by / migrated into the `website` system** — confirm no residual ownership remains in `content/`.
- **Global constraints.** No brand/access/resilience *decisions* are embedded in website subsystems (correct — brand belongs to `brand/`, failure decisions to `resilience/`, access to `access/`). At this scaffold stage no violations are observed; WebConstraints must enforce web *format* limits, not own brand or accessibility *policy* (policy source must be the global systems).

## Overall Build Size Estimate
Sum of conservative midpoints across the 9 subsystems ≈ **3,400–6,400 LOC** for the website system (TypeScript; excludes `*.test.ts`, `*.types.ts`, `*.debug.ts` per CLAUDE.md). No single subsystem is projected into the High band (≥~1500 LOC) on current intent. Concentrated swell risk in **PageComposer** (composition/orchestration + data model) and **WebConstraints** (if it owns full WCAG accessibility). The biggest size *lever* is not in-system code but the unresolved **duplication with content/** (Lifecycle + Personalizer): if those delegate rather than re-implement, the website total drops and two Medium risks fall.

## Risk Assessment
- **Overall risk: Medium.**
- **Size risk:** No High-band file expected; PageComposer and WebConstraints are the swell candidates (Medium). File size is a design signal, not yet a violation.
- **Boundary risk (primary):** three live duplication/direction issues — WebsiteLifecycle↔content/ContentLifecycleManager, WebPersonalizer↔content/ContentPersonalizer, WebAnalyticsAdapter↔integrations/ — plus the composition-vs-render (logic-free) line with renderers/HTMLRenderer. These would cause cross-system drift and duplicated logic if left undecided.
- **Scope-edge risk:** the system's outer edge ("validated content in storage", hard stop before the Astro consumer) has no named owner; risk of either a coverage gap at the hand-off or scope-creep into the out-of-scope Astro layer.
- **Process risk:** separate-repo build means shared types/lifecycle/URL ownership must be coordinated across repos — an organizational risk beyond the spec.
- **Documentation maturity:** all structured specs are empty templates; intent exists only as one-line module placeholders. PASS 0 can proceed on intent; PASS 1+ is blocked until the boundary/duplication rulings are made by a human.

## Verdict
**CAN** fulfil its role as currently structured — the 9 subsystems collectively cover the generate-web-content intent (compose, section, SEO, metadata, constraints, sitemap, lifecycle, personalize, analytics) and correctly stop short of hosting/serving — **conditional on** a human resolving the following before PASS 1:
1. **Rule on content/ duplication:** decide keep / merge / delegate for WebsiteLifecycle ↔ content/ContentLifecycleManager (and vs versioning/), and for WebPersonalizer ↔ content/ContentPersonalizer.
2. **Fix the WebAnalyticsAdapter direction:** route external analytics calls through `integrations/` (no own client); confirm it does not overlap observability/ internal metrics.
3. **Set the composition-vs-render line** with renderers/HTMLRenderer (renderers stay logic-free; SectionBuilder/PageComposer own composition only).
4. **Name the outer-edge owner:** who produces "validated web content in storage" (the hard stop before the out-of-scope Astro consumer) — WebConstraints, checks/, or verification/.
5. **Assign URL/slug/routing-policy and the shared page/section data-type owners**, and confirm any old `content/WebsiteContent` is fully superseded by this system.

**Status:** Draft Complete – Awaiting Review
