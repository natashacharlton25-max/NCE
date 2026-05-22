# Pre-Spec Notes Checklist — External Integrations
Do NOT invent functionality. If information is missing, leave TODO markers.

> **Purpose:** Ensure external integration pre-specs capture all provider-specific concerns before detailed spec writing.
> **Applies to:** All external integration placeholders (integration-{{provider}}-{{service}}).

---

## Overview

External integration pre-spec notes are different from internal components. They focus on:
- What the PROVIDER offers vs what WE use
- Field mappings (their schema → our schema)
- Their errors → our errors
- Rate limits, quotas, costs
- Failure modes and degradation

---

## Pre-Spec Notes Checklist — External Integrations

### 1. Core Identity
- [ ] **Integration name** — Follows `integration-{{provider}}-{{service}}` pattern
- [ ] **Provider** — External company/service identified
- [ ] **Service/API** — Specific API or service being used
- [ ] **Purpose statement** — One sentence: what capability this gives us
- [ ] **Status** — Set to "Placeholder" initially

### 2. Scope & Boundaries

#### What This Integration DOES
- [ ] **Capabilities we're using** — List 3-5 specific provider features
- [ ] **Why we need each** — Justify each capability

#### What This Integration DOES NOT Do
- [ ] **Provider features we're NOT using** — Explicit exclusions
- [ ] **Adjacent integrations** — "X is handled by integration-Y"
- [ ] **Internal systems** — "X is handled by System-Y"

#### Their API vs Our Wrapper
- [ ] **Their API** — What the provider exposes (raw)
- [ ] **Our wrapper** — What we expose to our systems (simplified)

### 3. Data Flow
- [ ] **Input from our systems** — What our systems send
- [ ] **Data sent to provider** — What we send to their API
- [ ] **Data received from provider** — What they return
- [ ] **Output to our systems** — What our systems receive
- [ ] **Visual flow diagram** — ASCII or description

### 4. Consuming Systems
- [ ] **Systems that use this** — List all internal systems
- [ ] **What each needs** — Capability per consumer
- [ ] **How they call it** — Method/function names
- [ ] **Data flow per consumer** — Input/output per system

### 5. Provider API Surface

#### Endpoints
- [ ] **Endpoints we use** — List with method, purpose, rate limit
- [ ] **Endpoints we DON'T use** — Explicit exclusions

#### Authentication
- [ ] **Auth type** — OAuth2 / API Key / Bearer Token / etc.
- [ ] **Scopes required** — List all scopes
- [ ] **Credential storage** — Via APIKeyManager (never in code)
- [ ] **Token refresh** — How/when tokens refresh

#### Documentation
- [ ] **API docs URL** — Link to provider docs
- [ ] **Rate limits URL** — Link or summary
- [ ] **Status page URL** — For monitoring

### 6. Field Mapping

#### Our Fields → Their Fields
- [ ] **All outbound mappings** — Every field we send
- [ ] **Transforms** — Any format/type conversions
- [ ] **Required vs optional** — Which fields are mandatory

#### Their Fields → Our Fields
- [ ] **All inbound mappings** — Every field we receive
- [ ] **Transforms** — Any format/type conversions
- [ ] **Default values** — What if field is missing

#### Unmapped Fields
- [ ] **Their fields we ignore** — Explicit list
- [ ] **Our fields with no equivalent** — What we can't send

### 7. Error Handling

#### Error Mapping
- [ ] **Their codes → our codes** — Full mapping table
- [ ] **HTTP status handling** — 4xx, 5xx responses
- [ ] **Provider-specific errors** — Any custom error formats

#### Transient vs Permanent
- [ ] **Transient errors** — Which can be retried
- [ ] **Permanent errors** — Which fail immediately
- [ ] **Retry strategy per error** — Specific handling

#### Fallback Strategy
- [ ] **On transient failure** — Retry / Queue / etc.
- [ ] **On permanent failure** — Fail / Cache / etc.
- [ ] **On provider outage** — Degrade / Block / etc.

### 8. Constraints & Limits

#### Rate Limits
- [ ] **Request limits** — Per minute/hour/day
- [ ] **Throughput limits** — MB/s if applicable
- [ ] **Concurrent connection limits** — If applicable

#### Quotas
- [ ] **Monthly/daily quotas** — API calls, storage, etc.
- [ ] **Overage behavior** — Cost increase or block?

#### Cost
- [ ] **Per-operation cost** — What costs money
- [ ] **Budget concerns** — Any expensive operations flagged
- [ ] **Cost tracking** — Emit events to CostTracker

### 9. Failure Modes
- [ ] **What can fail** — List all failure scenarios
- [ ] **Likelihood** — Low/Medium/High per failure
- [ ] **Impact** — Low/Medium/High per failure
- [ ] **Detection** — How we know it failed
- [ ] **Degradation behavior** — What happens when it fails

### 10. Concern Ownership
- [ ] **API key storage** — APIKeyManager owns
- [ ] **Rate limiting** — RateLimiter owns
- [ ] **Retry logic** — FailureHandler owns
- [ ] **Cost tracking** — CostTracker owns
- [ ] **Logging** — Logger owns (sanitize sensitive data!)
- [ ] **Validation** — Validator owns

### 11. Architecture Considerations
- [ ] **Sync vs Async** — Blocking or non-blocking?
- [ ] **Batching** — Does provider support? Do we use?
- [ ] **Caching** — What can be cached? TTL?
- [ ] **Webhooks** — Does provider push? Do we listen?
- [ ] **Polling** — Do we poll? Frequency?
- [ ] **Circuit breaker** — When do we stop calling?

### 12. Summary
- [ ] **All sections completed** — No blanks
- [ ] **Ready for approval** — Yes/No with reasoning

---

## Before Approval Checklist

Once you've drafted external integration pre-spec notes, verify:

- [ ] **All 12 sections completed** — No blanks or TBD items
- [ ] **Provider documented** — Can find their API docs
- [ ] **Field mappings complete** — Both directions
- [ ] **Error mappings complete** — Their codes → our codes
- [ ] **Rate limits known** — Or marked "Unknown — verify"
- [ ] **Costs known** — Or marked "Unknown — verify"
- [ ] **Failure modes listed** — At least 5 scenarios
- [ ] **Consuming systems identified** — Who uses this
- [ ] **No implementation details** — No code, no library names
- [ ] **Concern ownership clear** — Every cross-cutting concern assigned

---

## Differences from Internal Components

| Aspect | Internal Component | External Integration |
|--------|-------------------|---------------------|
| Focus | What WE build | What THEY provide |
| Data | Our schemas | Their schema ↔ Our schema |
| Errors | Our error codes | Their errors → Our errors |
| Limits | Our design choices | Their constraints |
| Failures | Our bugs | Their outages |
| Control | Full | Partial |

---

## After Approval → Full Spec Writing (Phase 22)

Once external integration pre-spec notes are approved, the detailed spec (Phase 22) includes:

- API-SURFACE.md — Their API documented
- OUR-WRAPPER.md — Our functions with signatures
- FIELD-MAPPING.md — Complete field translations
- ERRORS.md — Full error mapping
- CONSTRAINTS.md — Rate limits, quotas, costs
- FAILURE-MODES.md — Detailed failure handling
- EXAMPLES.md — Request/response examples

**Key difference:** Pre-spec notes = *what we need to figure out*. Full specs = *the figured-out answers*.

---

*Created: {{timestamp}}*
*Status: Active*
