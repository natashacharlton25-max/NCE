# System Coverage Review — social

## System Role Summary
`social` plans, creates, publishes, and analyzes social media content across platforms. It is the social-domain orchestration layer — owning campaign planning, platform-specific shaping, scheduling/review collation, and engagement interpretation — while delegating the mechanical push, the platform API calls, generic content adaptation, raw metric collection, and timed execution to other systems.

## Coverage Assessment

### Covered Responsibilities
| Responsibility | Covered By |
|----------------|------------|
| Campaign planning, content calendars, posting strategy | SocialMediaPlanner |
| Orchestrating publication of social posts (deciding what/when/where, handing to publishing) | SocialMediaManager |
| Collating posts for scheduling / review queues | SocialMediaCollator |
| Interpreting engagement & reach into social-domain metrics | SocialMediaAnalytics |

### Partially Covered Responsibilities
- **Actually pushing posts to platforms** — SocialMediaManager *intends* "manages publishing to platforms," but the mechanical push is owned by `publishing/DistributionManager` (push + delivery tracking + retry) and the API calls by `integrations/` (YouTubeAPI, IntegrationAdapter, etc.). social's covered share is the social-specific decision/orchestration only, not the transport.
- **Post content generation** — Generating/adapting the actual post body is owned by `content/` and specifically `content/ContentAdapterEngine` ("workbook → social posts"). social can request and arrange content but does not own copy generation.
- **Analytics data collection** — Raw timings/counts/throughput are owned by `observability/MetricsCollector`; system-usage analytics by `orchestration/AnalyticsHandler`. SocialMediaAnalytics's covered share is social-domain *interpretation* of those signals, not raw collection/aggregation.
- **Timed posting** — Scheduled execution is owned by `orchestration/Scheduler` (queued jobs, timing). SocialMediaPlanner/Collator can declare *when*, but do not own the timer.

### Uncovered Responsibilities
- **Per-platform credential / OAuth token handling** — not named in any of the 4 subsystems; likely sits in `access/` or `integrations/`, but the seam is unassigned for social specifically. Flag, do not design.
- **Per-platform constraint enforcement** (char limits, media specs, hashtag rules) — implied by "across platforms" but not explicitly owned by any subsystem; ambiguous between SocialMediaManager (shaping) and content/ContentAdapterEngine (format mapping).

## Subsystem Sufficiency Review
| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| SocialMediaPlanner | Sufficient | Calendars/strategies are self-contained planning logic with little external overlap; clearest unique-value subsystem. | 400–650 | Low |
| SocialMediaManager | At-risk / thin | Stated role "manages publishing to platforms" collides head-on with publishing/DistributionManager + integrations/. Net unique role is social-specific orchestration (per-platform shaping, what/when/where decisioning) — real, but smaller than the one-liner implies. Needs boundary ruling before it can be specced. | 300–550 | Low |
| SocialMediaCollator | Sufficient (narrow) | Collation/queueing for scheduling/review is genuine, but overlaps review/ (review queues) and orchestration/Scheduler (queue) at the edges; could collapse into Planner if it stays this thin. | 200–400 | Low |
| SocialMediaAnalytics | Partial | Engagement/reach interpretation is a real social-domain need, but raw collection is observability/MetricsCollector + orchestration/AnalyticsHandler. Viable only as an interpretation/reporting layer over those feeds. | 300–550 | Low |

## Missing Capability Areas
- **Platform credential / connection state** — which system holds social platform tokens and connection health is unassigned for social. (Flag only.)
- **Per-platform output constraints** — enforcement of platform-specific limits/specs has no clear owner among the 4 subsystems.
- No High-risk (>1500 LOC) file is anticipated in any subsystem.

## Boundary & Classification Issues
- **SocialMediaManager ↔ publishing/DistributionManager (MERGE/BOUNDARY):** DistributionManager already owns "push content to platforms," distribution state, and retry; publishing's Approved Pass0 explicitly assigns "distribution coordination" to publishing and "social-media-specific logic" to social. SocialMediaManager must be re-scoped to the social-decision layer or it duplicates publishing. Highest-priority ruling.
- **SocialMediaManager ↔ integrations/ (BOUNDARY):** The actual platform API calls (YouTubeAPI, IntegrationAdapter, WebhookReceiver) live in integrations/. social must consume these, not reimplement them.
- **post content ↔ content/ContentAdapterEngine (BOUNDARY):** "create" in the social intent must not pull copy/format generation into social; that is content/'s. social arranges, it does not author.
- **SocialMediaAnalytics ↔ observability/MetricsCollector + orchestration/AnalyticsHandler (BOUNDARY):** raw metrics belong to observability/orchestration; social owns only the social-domain interpretation. Risk of duplicating collection.
- **timed posting ↔ orchestration/Scheduler (BOUNDARY):** social declares schedule intent; Scheduler executes. social must not own a timer.
- **SocialMediaCollator ↔ review/ + orchestration queue (DROP/MERGE candidate):** if collation is just queueing, it may duplicate review queues / Scheduler queue and could fold into Planner.
- **Global constraints:** any platform-failure/retry *decisions* belong to `resilience/`; any brand rules on social output belong to `brand/`; social must not embed either. No renderer logic in social.

## Overall Build Size Estimate
- **Total estimated LOC:** ~1200–2150 across the 4 subsystems (conservative).
- **Largest expected file:** SocialMediaPlanner (~650 LOC).
- **No file expected to exceed ~1500 LOC** → no High size risk; all subsystems Low.

## Risk Assessment
- **Overall risk level: Medium** — not from size (all Low), but from **boundary ambiguity**. Two of four subsystems (Manager, Analytics) substantially overlap already-Approved neighbouring systems, and the post-creation seam touches content/. Specs cannot be locked until ownership is ruled, or social will duplicate publishing/integrations/observability.
- Estimation confidence is **low**: all social system- and subsystem-level docs are empty templates; intent is drawn only from the one-line subsystem descriptions and boundary hints. LOC ranges are indicative.

## Verdict
**CAN** fulfil its role as currently structured — *conditionally*. The four subsystems span the social intent (plan / create / publish / analyze) with no missing core area and no size risk. However, this is contingent on the following boundary rulings being made (by a human; PASS 0 surfaces them, it does not decide them) before PASS 2/3:
1. Re-scope **SocialMediaManager** to social-decision/orchestration only; the platform push stays with publishing/DistributionManager + integrations/.
2. Confirm **SocialMediaAnalytics** is an interpretation layer over observability/MetricsCollector + orchestration/AnalyticsHandler, not a second collector.
3. Confirm post content "create" is arrangement over content/ContentAdapterEngine output, not authoring.
4. Assign owners for the two uncovered seams (platform credentials/connection state; per-platform output constraints).
5. Decide whether **SocialMediaCollator** survives independently or folds into Planner.

Until these are ruled, social CAN be built but MUST NOT proceed to spec lock.

**Status:** Draft Complete – Awaiting Review
