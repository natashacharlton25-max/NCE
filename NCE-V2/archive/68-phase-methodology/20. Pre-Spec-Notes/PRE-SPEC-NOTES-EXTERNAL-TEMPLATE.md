# [Provider] — [Service] Integration
Do NOT invent functionality. If information is missing, leave TODO markers.

> **Purpose:** [One sentence: "Integrate with [PROVIDER] to [CAPABILITY]"]
> **Provider:** [e.g., Google, Canva, Stripe, OpenAI]
> **Service:** [e.g., Drive, Design, Payments, Chat Completions]
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

[2-3 sentences describing what this integration provides to the project]

---

## 1. Core Identity

- **Integration name:** [integration-{{provider}}-{{service}}]
- **Provider:** [External company/service]
- **Service/API:** [Specific API or service being used]
- **Purpose statement:** [One sentence: what capability this gives us]
- **Status:** Placeholder

---

## 2. Scope & Boundaries

### What This Integration DOES
- [Capability 1 we're using]
- [Capability 2 we're using]
- [Capability 3 we're using]

### What This Integration DOES NOT Do
- [Provider capability we're NOT using]
- [Provider capability we're NOT using]
- [Adjacent integration handles...]
- [Internal system handles...]

### Their API vs Our Wrapper
- **Their API:** [What the provider exposes]
- **Our wrapper:** [What we expose to our systems]

---

## 3. Data Flow

**Input from our systems:**
- [System A provides...]
- [System B provides...]

**Data sent to provider:**
- [What we send to their API]

**Data received from provider:**
- [What they return]

**Output to our systems:**
- [System C receives...]
- [System D receives...]

**Visual flow:**
```
[Our System] → [Our Wrapper] → [Their API] → [Their Response] → [Our Wrapper] → [Our System]
```

---

## 4. Consuming Systems

### Systems That Use This Integration

| System | What They Need | How They Call It |
|--------|---------------|------------------|
| [System A] | [Capability] | [Method/function] |
| [System B] | [Capability] | [Method/function] |
| [System C] | [Capability] | [Method/function] |

### Data Flow Per Consumer

| Consumer | Sends | Receives |
|----------|-------|----------|
| [System A] | [Input data] | [Output data] |
| [System B] | [Input data] | [Output data] |

---

## 5. Provider API Surface

### Endpoints We Use

| Endpoint | Method | Purpose | Rate Limit |
|----------|--------|---------|------------|
| [/api/v1/resource] | GET/POST | [What it does] | [X/min] |
| [/api/v1/other] | GET/POST | [What it does] | [X/min] |

### Authentication

- **Auth type:** [OAuth2 / API Key / Bearer Token / etc.]
- **Scopes required:** [List scopes]
- **Credential storage:** [Via APIKeyManager / Environment / etc.]
- **Token refresh:** [How/when tokens refresh]

### Provider Documentation

- **API docs:** [URL]
- **Rate limits:** [URL or summary]
- **Status page:** [URL]

---

## 6. Field Mapping

### Our Fields → Their Fields

| Our Field | Their Field | Transform | Notes |
|-----------|-------------|-----------|-------|
| [ourField1] | [theirField1] | [none/format/convert] | [Notes] |
| [ourField2] | [theirField2] | [none/format/convert] | [Notes] |
| [ourField3] | [theirField3] | [none/format/convert] | [Notes] |

### Their Fields → Our Fields

| Their Field | Our Field | Transform | Notes |
|-------------|-----------|-----------|-------|
| [theirField1] | [ourField1] | [none/format/convert] | [Notes] |
| [theirField2] | [ourField2] | [none/format/convert] | [Notes] |

### Unmapped Fields

- **Their fields we ignore:** [List fields we don't use]
- **Our fields with no equivalent:** [List fields we can't send]

---

## 7. Error Handling

### Their Error Codes → Our Error Codes

| Their Code | Their Message | Our Code | Our Handling |
|------------|---------------|----------|--------------|
| [400] | [Bad Request] | [INT_VALIDATION_*] | [Fail permanently] |
| [401] | [Unauthorized] | [INT_AUTH_*] | [Refresh token] |
| [429] | [Rate Limited] | [INT_RATE_*] | [Backoff + retry] |
| [500] | [Server Error] | [INT_PROVIDER_*] | [Retry 3x] |

### Transient vs Permanent

| Error Type | Transient? | Retry Strategy |
|------------|------------|----------------|
| Rate limit | Yes | Exponential backoff |
| Auth expired | Yes | Refresh + retry once |
| Invalid input | No | Fail immediately |
| Provider down | Yes | Retry with timeout |

### Fallback Strategy

- **On transient failure:** [Retry / Queue / etc.]
- **On permanent failure:** [Fail / Use cached / etc.]
- **On provider outage:** [Degrade gracefully / Block feature / etc.]

---

## 8. Constraints & Limits

### Rate Limits

| Limit Type | Value | Per | Notes |
|------------|-------|-----|-------|
| Requests | [X] | minute/hour/day | [Notes] |
| Throughput | [X] | MB/s | [Notes] |
| Concurrent | [X] | connections | [Notes] |

### Quotas

| Quota Type | Value | Period | Overage |
|------------|-------|--------|---------|
| [API calls] | [X] | month | [Cost / Block] |
| [Storage] | [X] GB | — | [Cost / Block] |

### Cost

| Operation | Cost | Unit | Budget Concern |
|-----------|------|------|----------------|
| [API call] | [$X] | per 1000 | [Yes/No] |
| [Storage] | [$X] | per GB/month | [Yes/No] |

---

## 9. Failure Modes

### What Can Fail

| Failure | Likelihood | Impact | Detection |
|---------|------------|--------|-----------|
| Provider down | Low | High | Health check |
| Rate limited | Medium | Medium | 429 response |
| Auth expired | Low | Medium | 401 response |
| Timeout | Medium | Low | No response |
| Data mismatch | Low | High | Validation |

### Degradation Behavior

- **If provider slow:** [Queue / Timeout / etc.]
- **If provider down:** [Cache / Skip / Block / etc.]
- **If rate limited:** [Backoff / Queue / etc.]
- **If auth fails:** [Refresh / Alert / etc.]

---

## 10. Concern Ownership

| Concern | Owner | Notes |
|---------|-------|-------|
| API key storage | APIKeyManager | Never in code |
| Rate limiting | RateLimiter | Per-provider limits |
| Retry logic | FailureHandler | Emit codes, let FH decide |
| Cost tracking | CostTracker | Emit cost events |
| Logging | Logger | Sanitize sensitive data |
| Validation | Validator | Input/output validation |

---

## 11. Architecture Considerations

- **Sync vs Async:** [Blocking / Non-blocking / Queue-based]
- **Batching:** [Does provider support batch? Do we use it?]
- **Caching:** [What can be cached? TTL?]
- **Webhooks:** [Does provider push? Do we listen?]
- **Polling:** [Do we poll? Frequency?]
- **Circuit breaker:** [When do we stop calling?]

---

## 12. Summary

| Item | Status | Notes |
|------|--------|-------|
| **Core Identity** | ✅ / ⚠️ / ❌ | [Notes] |
| **Scope & Boundaries** | ✅ / ⚠️ / ❌ | [Notes] |
| **Data Flow** | ✅ / ⚠️ / ❌ | [Notes] |
| **Consuming Systems** | ✅ / ⚠️ / ❌ | [Notes] |
| **API Surface** | ✅ / ⚠️ / ❌ | [Notes] |
| **Field Mapping** | ✅ / ⚠️ / ❌ | [Notes] |
| **Error Handling** | ✅ / ⚠️ / ❌ | [Notes] |
| **Constraints** | ✅ / ⚠️ / ❌ | [Notes] |
| **Failure Modes** | ✅ / ⚠️ / ❌ | [Notes] |
| **Concern Ownership** | ✅ / ⚠️ / ❌ | [Notes] |
| **Architecture** | ✅ / ⚠️ / ❌ | [Notes] |

**Ready for approval?** [Yes / No]
**Reviewer notes:** [Any concerns or questions before spec writing]

---

*Created: [Date]*
*Version: 0.0.0*
*Status: Placeholder*
