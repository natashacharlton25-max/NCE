# {{Provider}} {{Service}} — Overview

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

(2-3 sentences explaining why we integrate with this provider and what capability it gives us)

---

## What This Integration Provides

### Capabilities We Use

| Capability | Why We Need It | Alternative |
|------------|----------------|-------------|
| {{capability}} | {{reason}} | {{build in-house / other provider / none}} |
| {{capability}} | {{reason}} | {{alternative}} |
| {{capability}} | {{reason}} | {{alternative}} |

### Capabilities We Do NOT Use

| Capability | Why Not |
|------------|---------|
| {{capability}} | {{reason}} |
| {{capability}} | {{reason}} |

---

## Integration Scope

### This Integration DOES:

- {{Responsibility 1}}
- {{Responsibility 2}}
- {{Responsibility 3}}

### This Integration Does NOT:

- {{What adjacent system handles}}
- {{What we explicitly exclude}}
- {{Provider feature we don't use}}

---

## Provider Context

### About {{Provider}}

| Field | Value |
|-------|-------|
| **Company** | {{provider company name}} |
| **Service** | {{specific service/API}} |
| **Established** | {{year or "N/A"}} |
| **Market Position** | Leader / Established / Emerging |

### Why This Provider

| Criteria | Assessment | Notes |
|----------|------------|-------|
| Feature fit | Good / Partial / Poor | {{notes}} |
| Pricing | Competitive / Expensive / Cheap | {{notes}} |
| Reliability | High / Medium / Low | {{notes}} |
| Documentation | Good / Partial / Poor | {{notes}} |
| Support | Good / Partial / Poor | {{notes}} |

### Alternatives Considered

| Provider | Why Not Chosen |
|----------|----------------|
| {{provider}} | {{reason}} |
| Build in-house | {{reason}} |

---

## Consuming Systems

### Who Uses This Integration

| System | What They Need | How Critical |
|--------|---------------|--------------|
| {{system}} | {{capability}} | Critical / Important / Nice-to-have |
| {{system}} | {{capability}} | Critical / Important / Nice-to-have |

### Data Flow

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│                 │      │                 │      │                 │
│  Our System A   │─────▶│  Our Wrapper    │─────▶│  {{Provider}}   │
│                 │      │                 │      │     API         │
└─────────────────┘      └────────┬────────┘      └────────┬────────┘
                                  │                        │
                                  │◀───────────────────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │                 │
                         │  Our System B   │
                         │                 │
                         └─────────────────┘
```

---

## Integration Boundaries

### What Crosses the Boundary

| Direction | What | Format | Sensitive? |
|-----------|------|--------|------------|
| **Out** | {{data we send}} | {{format}} | Yes / No |
| **Out** | {{data we send}} | {{format}} | Yes / No |
| **In** | {{data we receive}} | {{format}} | Yes / No |
| **In** | {{data we receive}} | {{format}} | Yes / No |

### Data Sensitivity

| Data | Classification | Handling |
|------|----------------|----------|
| {{data type}} | Public / Internal / Confidential / PII | {{how we handle}} |
| {{data type}} | {{classification}} | {{handling}} |

---

## Guarantees

### What We Guarantee to Consumers

| Guarantee | Description |
|-----------|-------------|
| **Availability** | {{what we promise}} |
| **Latency** | {{expected response time}} |
| **Data freshness** | {{how current data is}} |
| **Error handling** | {{how errors are surfaced}} |

### What We Do NOT Guarantee

| Non-Guarantee | Reason |
|---------------|--------|
| {{thing}} | Depends on provider |
| {{thing}} | Outside our control |

---

## Dependencies

### This Integration Depends On

| Dependency | Type | Why |
|------------|------|-----|
| {{provider}} | External | The integration itself |
| APIKeyManager | Internal | Credential management |
| RateLimiter | Internal | Rate limit handling |
| FailureHandler | Internal | Error handling |
| Logger | Internal | Logging |

### Nothing Else Should Depend On

This integration wrapper is the ONLY way to access {{provider}}.

**Forbidden:** Direct calls to {{provider}} API from other systems.

---

## Lifecycle

### Integration States

| State | Description |
|-------|-------------|
| **Healthy** | All systems nominal |
| **Degraded** | Partial functionality |
| **Down** | Integration unavailable |
| **Recovering** | Coming back online |

### Initialization

1. Load credentials from APIKeyManager
2. Validate credentials with test request
3. Initialize rate limiter
4. Mark as ready

### Shutdown

1. Complete in-flight requests
2. Flush any queued requests
3. Close connections
4. Mark as stopped

---

## Risk Assessment

### Vendor Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Provider goes down | Low | High | Circuit breaker, degraded mode |
| Price increase | Medium | Medium | Monitor, have alternative |
| API breaking change | Low | High | Version pinning, monitoring |
| Provider EOL | Very Low | High | Identify alternatives |

### Data Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Data breach at provider | Low | High | Minimize sensitive data sent |
| Data loss | Very Low | Medium | Don't use as primary store |

---

## Compliance

| Requirement | Status | Notes |
|-------------|--------|-------|
| Data residency | Compliant / Partial / N/A | {{notes}} |
| GDPR | Compliant / Partial / N/A | {{notes}} |
| Data retention | Compliant / Partial / N/A | {{notes}} |

---

## Cost Context

| Metric | Value | Budget Impact |
|--------|-------|---------------|
| Expected monthly calls | {{n}} | ${{n}} |
| Expected monthly cost | ${{n}} | {{%}} of budget |
| Cost per user action | ${{n}} | {{notes}} |

---

## Notes

(Any additional context, assumptions, or clarifications)

---
Source: PRE-SPEC-NOTES.md sections 1, 2, 4
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
