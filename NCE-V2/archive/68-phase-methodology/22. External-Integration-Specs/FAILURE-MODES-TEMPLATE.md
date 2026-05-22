# {{Provider}} {{Service}} — Failure Modes

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document catalogs all ways this integration can fail and defines our degradation behaviour.

**Key principle:** External services WILL fail. Plan for it.

---

## Failure Mode Summary

| Failure | Likelihood | Impact | Detection | Recovery |
|---------|------------|--------|-----------|----------|
| Provider down | Low | High | Health check | Degrade |
| Rate limited | Medium | Medium | 429 response | Backoff |
| Auth expired | Low | Medium | 401 response | Refresh |
| Timeout | Medium | Low | No response | Retry |
| Data mismatch | Low | High | Validation | Alert |

---

## Failure Categories

### 1. Provider Unavailability

#### Complete Outage

| Field | Value |
|-------|-------|
| **Likelihood** | Low |
| **Impact** | High |
| **Detection** | Health check fails, 5xx responses |
| **Duration** | Minutes to hours |

**Symptoms:**
- All requests return 503
- Health endpoint unreachable
- Status page shows incident

**Our Response:**
1. Detect via health check (every 1 min)
2. Activate circuit breaker after 3 failures
3. Switch to degraded mode
4. Notify operations team
5. Poll for recovery
6. Gradually restore traffic

**Degraded Behaviour:**
- {{What features work without this integration}}
- {{What features are disabled}}
- {{What cached data can be served}}

---

#### Partial Outage

| Field | Value |
|-------|-------|
| **Likelihood** | Medium |
| **Impact** | Medium |
| **Detection** | Some endpoints fail, others work |

**Symptoms:**
- Specific endpoints return errors
- Others work normally
- Intermittent failures

**Our Response:**
1. Identify affected endpoints
2. Disable affected features only
3. Continue using working endpoints
4. Monitor for resolution

---

### 2. Rate Limiting

#### Soft Rate Limit

| Field | Value |
|-------|-------|
| **Likelihood** | Medium |
| **Impact** | Low |
| **Detection** | Low `X-RateLimit-Remaining` |

**Symptoms:**
- Rate limit headers show <20% remaining
- No errors yet

**Our Response:**
1. Slow request rate by 50%
2. Queue non-urgent requests
3. Prioritize critical operations
4. Monitor remaining quota

---

#### Hard Rate Limit

| Field | Value |
|-------|-------|
| **Likelihood** | Medium |
| **Impact** | Medium |
| **Detection** | 429 response |

**Symptoms:**
- 429 Too Many Requests
- `Retry-After` header present

**Our Response:**
1. Stop all requests immediately
2. Wait for `Retry-After` duration
3. Queue incoming requests
4. Resume with reduced rate
5. Gradually increase to normal

**User Impact:**
- Requests delayed by {{n}} seconds
- Non-critical features temporarily unavailable

---

### 3. Authentication Failures

#### Token Expired

| Field | Value |
|-------|-------|
| **Likelihood** | Low (if refresh works) |
| **Impact** | Medium |
| **Detection** | 401 response |

**Symptoms:**
- 401 Unauthorized
- `token_expired` error code

**Our Response:**
1. Attempt token refresh
2. If refresh succeeds, retry original request
3. If refresh fails, escalate to auth failure
4. Queue requests during refresh

---

#### Credentials Invalid

| Field | Value |
|-------|-------|
| **Likelihood** | Very Low |
| **Impact** | High |
| **Detection** | 401 with `invalid_credentials` |

**Symptoms:**
- 401 on all requests
- Token refresh also fails

**Our Response:**
1. Stop all requests
2. Alert operations immediately
3. Activate full degraded mode
4. Require manual intervention

**This is a critical failure requiring human action.**

---

### 4. Network Failures

#### Connection Timeout

| Field | Value |
|-------|-------|
| **Likelihood** | Medium |
| **Impact** | Low |
| **Detection** | No response within timeout |

**Symptoms:**
- Request hangs
- No response received

**Our Response:**
1. Cancel request at timeout ({{n}}s)
2. Retry with exponential backoff
3. After {{n}} retries, fail the request
4. Return `{{PREFIX}}_NETWORK_002`

---

#### DNS Failure

| Field | Value |
|-------|-------|
| **Likelihood** | Very Low |
| **Impact** | High |
| **Detection** | DNS resolution error |

**Symptoms:**
- Cannot resolve provider hostname
- Network-level error

**Our Response:**
1. Retry with backup DNS
2. If persistent, activate circuit breaker
3. Alert operations

---

#### SSL/TLS Failure

| Field | Value |
|-------|-------|
| **Likelihood** | Very Low |
| **Impact** | High |
| **Detection** | Certificate error |

**Symptoms:**
- Certificate validation fails
- Handshake error

**Our Response:**
1. Do NOT bypass certificate validation
2. Alert operations immediately
3. Could indicate MITM attack or provider issue

---

### 5. Data Failures

#### Unexpected Response Format

| Field | Value |
|-------|-------|
| **Likelihood** | Low |
| **Impact** | Medium |
| **Detection** | Parse error, missing fields |

**Symptoms:**
- JSON parse fails
- Required fields missing
- Type mismatch

**Our Response:**
1. Log full response for debugging
2. Attempt graceful degradation
3. Use defaults for missing fields
4. Alert if critical fields missing

---

#### Data Corruption

| Field | Value |
|-------|-------|
| **Likelihood** | Very Low |
| **Impact** | High |
| **Detection** | Checksum mismatch, invalid data |

**Symptoms:**
- Data doesn't match expected format
- Checksum validation fails

**Our Response:**
1. Reject corrupted data
2. Retry request
3. If persistent, alert operations
4. Do NOT use corrupted data

---

### 6. Business Logic Failures

#### Resource Not Found

| Field | Value |
|-------|-------|
| **Likelihood** | Medium |
| **Impact** | Low |
| **Detection** | 404 response |

**Symptoms:**
- Requested resource doesn't exist
- May have been deleted externally

**Our Response:**
1. Return clear error to caller
2. Remove from local cache if cached
3. Log for audit trail

---

#### Validation Rejected

| Field | Value |
|-------|-------|
| **Likelihood** | Medium |
| **Impact** | Low |
| **Detection** | 400 response |

**Symptoms:**
- Provider rejects our input
- Validation rules not met

**Our Response:**
1. Parse validation errors
2. Return actionable error to caller
3. Do NOT retry (permanent failure)

---

## Circuit Breaker

### States

```
CLOSED (normal) → OPEN (failing) → HALF-OPEN (testing)
       ↑                                    │
       └────────────────────────────────────┘
```

| State | Behaviour |
|-------|-----------|
| **CLOSED** | Normal operation, requests pass through |
| **OPEN** | All requests fail immediately, no calls to provider |
| **HALF-OPEN** | Limited test requests to check recovery |

### Thresholds

| Transition | Condition |
|------------|-----------|
| CLOSED → OPEN | {{n}} failures in {{n}} seconds |
| OPEN → HALF-OPEN | {{n}} seconds elapsed |
| HALF-OPEN → CLOSED | {{n}} consecutive successes |
| HALF-OPEN → OPEN | Any failure |

---

## Degraded Mode Behaviour

### What Still Works

| Feature | Behaviour in Degraded Mode |
|---------|---------------------------|
| {{feature}} | Uses cached data |
| {{feature}} | Returns default values |
| {{feature}} | Queues for later |

### What Doesn't Work

| Feature | Behaviour in Degraded Mode |
|---------|---------------------------|
| {{feature}} | Returns error, disabled |
| {{feature}} | Returns error, disabled |

### User Communication

| Scenario | Message |
|----------|---------|
| Feature degraded | "This feature is temporarily limited" |
| Feature disabled | "This feature is temporarily unavailable" |
| Data stale | "Showing data from {{time}}" |

---

## Recovery Procedures

### Automatic Recovery

| Trigger | Action |
|---------|--------|
| Health check passes | Begin recovery |
| Circuit breaker half-open | Test with limited traffic |
| Test requests succeed | Gradually restore |

### Recovery Steps

1. **Detect recovery** — Health check passes
2. **Test** — Send test request
3. **Validate** — Confirm response is valid
4. **Gradual restore** — 10% → 25% → 50% → 100%
5. **Monitor** — Watch for regressions
6. **Close circuit** — Return to normal

### Manual Recovery

| Scenario | Action Required |
|----------|-----------------|
| Credentials invalid | Regenerate API key |
| Account suspended | Contact provider |
| Breaking API change | Update integration |

---

## Monitoring & Alerting

### Health Checks

| Check | Frequency | Timeout | Failure Action |
|-------|-----------|---------|----------------|
| Provider health | 1 min | 5s | Increment failure counter |
| Auth validity | 5 min | 10s | Refresh token |
| Quota status | 15 min | 10s | Alert if >80% |

### Alerts

| Condition | Severity | Notification |
|-----------|----------|--------------|
| Circuit open | Critical | Page on-call |
| Rate limit hit | Warning | Slack alert |
| Auth failure | Critical | Page on-call |
| Error rate >5% | Warning | Slack alert |
| Error rate >20% | Critical | Page on-call |

---

## Failure Response Matrix

| Failure Type | Retry | Circuit Breaker | Degrade | Alert |
|--------------|-------|-----------------|---------|-------|
| Provider 5xx | Yes | Yes | Yes | Warning |
| Provider down | No | Yes | Yes | Critical |
| Rate limited | Wait | No | Partial | Warning |
| Auth expired | Once | No | No | Info |
| Auth invalid | No | Yes | Yes | Critical |
| Timeout | Yes | Yes | No | Warning |
| Validation | No | No | No | Debug |
| Not found | No | No | No | Debug |

---

## Testing Failure Modes

### Chaos Testing

| Test | Method | Expected Result |
|------|--------|-----------------|
| Provider down | Block endpoint | Circuit opens, degrades |
| Rate limit | Exhaust quota | Backoff activates |
| Slow response | Add latency | Timeout handling works |
| Bad response | Return invalid JSON | Error handled gracefully |

### Failure Injection

```typescript
// Test mode flags
{{PREFIX}}_SIMULATE_DOWN=true
{{PREFIX}}_SIMULATE_RATE_LIMIT=true
{{PREFIX}}_SIMULATE_TIMEOUT=true
{{PREFIX}}_SIMULATE_AUTH_FAIL=true
```

---

## Notes

- Test all failure modes before production
- Document any new failure modes discovered
- Review after each incident

---
Source: PRE-SPEC-NOTES.md section 9
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
