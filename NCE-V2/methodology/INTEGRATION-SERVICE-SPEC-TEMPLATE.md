# {{Provider}} / {{Service Name}} — Integration Service Spec

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Methodology Stage: 2 of 5
Component Type: Integration Service
Parent Provider: {{Provider Name}}
---

## 1. Identity

| Field | Value |
|---|---|
| **Provider** | {{Provider Name}} |
| **Service** | {{Service Name}} |
| **API Version** | {{version}} |
| **Documentation URL** | {{URL}} |
| **FileTree-v2 reference** | `integrations/{{Provider}}Integration.ts` (or related subsystem) |
| **Worker** | `integrations` Worker / parent system Worker |

## 2. Direction

| Field | Value |
|---|---|
| **Direction** | Outbound receiver / Inbound source / Downstream renderer / Combined |
| **Output form (if outbound)** | Rendered HTML / PDF blob / DOCX blob / JSON / Other |
| **Asset sink (if inbound binary)** | R2 via `assets/` system — bucket `{{BUCKET}}` |
| **Library sink (if inbound metadata)** | D1 via `library/` — binding `{{BINDING}}`, library `{{name}}` |

## 3. Purpose

(One paragraph stating why this service is needed for NCE-V2 and what it enables.)

## 4. Scope — What This Integration Handles

- …
- …

## 5. Out of Scope / Non-Goals

What we will NOT use this service for (even if it supports it):
- …
- …

## 6. Consuming Systems

| Internal System | How It Uses This Service |
|---|---|
| {{system}} | |

## 7. Auth

| Field | Value |
|---|---|
| **Auth method** | API key / OAuth 2.0 / Service account / Signed request |
| **Worker secret binding name** | `{{SECRET_NAME}}` |
| **Scopes / Permissions required** | (list specific scopes) |
| **Token refresh required?** | Yes / No / N/A |

## 8. API Surface

For each endpoint we use:

### {{HTTP Method}} {{path}}

| Field | Value |
|---|---|
| **Purpose** | (one line) |
| **Auth scope** | (specific scope or permission required) |
| **Request TS type** | `{{TypeName}}` |
| **Response TS type** | `{{TypeName}}` |
| **Rate limit** | {{n}} req/s |

```typescript
// Request shape
interface {{RequestType}} { /* ... */ }

// Response shape
interface {{ResponseType}} { /* ... */ }
```

---

(Repeat per endpoint used.)

## 9. Our Wrapper

The wrapper function(s) in `src/integrations/{{Provider}}Integration.ts`:

```typescript
async function {{wrapperFn}}(
  input: {{InType}}
): Promise<Result<{{OutType}}, {{ErrorCode}}>>;
```

**Behaviour:**
- Reads secret via `env.{{SECRET_NAME}}`
- Calls `resilience/RateLimiter.check("{{provider}}", "{{service}}")` before request
- Wraps `fetch()` with `resilience/RetryPolicyEngine` (exponential backoff, max 3 retries)
- Wraps with `resilience/FallbackStrategyResolver` for graceful degradation
- Maps external error codes to our error categories

## 10. Field Mapping

External API ↔ our internal types:

| External Field | Our Field | Notes |
|---|---|---|
| `external_field_name` | `ourFieldName` | (transformation if any) |

## 11. Errors

External error codes mapped to our error categories:

| External Code/Status | Our Error Code | Category | resilience/ Pattern |
|---|---|---|---|
| 401 Unauthorized | `{{PROVIDER}}_AUTH_FAILED` | External | (none — escalate) |
| 429 Too Many Requests | `{{PROVIDER}}_RATE_LIMITED` | External | RateLimiter back-off |
| 5xx Server Error | `{{PROVIDER}}_UPSTREAM_ERROR` | External | RetryPolicyEngine + FallbackStrategyResolver |
| Timeout | `{{PROVIDER}}_TIMEOUT` | Worker | RetryPolicyEngine |

## 12. Constraints

| Constraint | Value | Notes |
|---|---|---|
| Rate limit | {{n}} req/s | Worker CPU/memory interaction |
| Payload size (request) | <{{n}} KB | |
| Payload size (response) | <{{n}} KB | Worker 128 MB memory cap |
| Quota | {{n}} req/day | |
| Latency p99 | <{{n}}ms | Worker 5-min CPU cap consideration |
| Cost per call | ${{n}} | Cost monitoring |

## 13. Failure Modes

| Failure | Our Handling | resilience/ Pattern |
|---|---|---|
| Service unavailable | | FallbackStrategyResolver |
| Auth failure / token expired | | resilience/EscalationRouter |
| Rate limit exceeded | | RateLimiter back-off |
| Timeout | | RetryPolicyEngine |
| Invalid request (4xx) | | Return Result.error — caller decides |
| Unexpected response format | | Log + return Result.error |

## 14. Sink Targets

For inbound data:
- **Binary assets** (images, PDFs, etc.) → `assets/` system → R2 bucket `{{BUCKET}}`
- **Metadata** (asset IDs, captions, attribution) → library `{{library-name}}` via `library/Writer` (writer = `{{OwnerModule}}`)

For outbound data:
- **Rendered artefact** prepared by NCE-V2 before send (per output-boundary rule)
- NEVER raw NCE-V2 JSON expecting downstream rendering (unless this provider IS a downstream renderer like Astro)

## 15. Examples

### Happy path (outbound)

```typescript
const result = await {{wrapperFn}}({ /* valid input */ });
// expect: Result.ok({ /* shape of {{OutType}} */ })
```

### Rate-limit recovery

```typescript
// Many concurrent calls — RateLimiter throttles
const promises = Array(100).fill(null).map(() => {{wrapperFn}}(input));
const results = await Promise.allSettled(promises);
// expect: some queued via RateLimiter; none fail with `{{PROVIDER}}_RATE_LIMITED` (handled internally)
```

### Service down (graceful degradation)

```typescript
// Provider returns 503; FallbackStrategyResolver kicks in
const result = await {{wrapperFn}}(input);
// expect: Result.ok with fallback data OR Result.error after retries exhausted
```

## 16. Rules / Non-negotiables

- Worker secret accessed only via `env.{{SECRET_NAME}}` (never logged, never returned to caller)
- All outbound `fetch()` calls go through `resilience/` patterns
- Inbound binary data NEVER stored anywhere except R2 via `assets/`
- Inbound metadata NEVER written anywhere except library D1 via `library/Writer`
- Maximum 1 wrapper file per service (≤2000 LOC)

## 17. LOC Estimate

| Field | Value |
|---|---|
| **Estimated LOC** | ~{{n}} |
| **Size Band** | Low (<1500) / Medium (1500–2000) / High (>2000) |

## 18. Hardening Self-Check (mandatory before APPROVED status)

| Pass | Check | Status |
|---|---|---|
| Completeness | All 17 sections above have content | ☐ |
| Completeness | All endpoints we use are listed in API Surface | ☐ |
| Completeness | All errors mapped to our codes + categories | ☐ |
| Ambiguity | TS types fully specified | ☐ |
| Ambiguity | Direction (inbound/outbound/renderer) is unambiguous | ☐ |
| Implementability | Wrapper function signature is concrete | ☐ |
| Implementability | resilience/ patterns referenced where required | ☐ |
| Implementability | Sink targets correct (assets/R2 for binary; library/D1 for metadata) | ☐ |
| Test coverage | Happy path example present | ☐ |
| Test coverage | Rate-limit example present | ☐ |
| Test coverage | Failure-mode example present | ☐ |
| Output-boundary | Outbound calls send the right form (rendered when needed) | ☐ |
| Security | Secret binding name set; no credentials in repo | ☐ |
| Size | Estimated LOC ≤2000 OR exception logged | ☐ |

If any check ☐ unchecked → revise; do not mark Status: APPROVED.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Spec author | Claude | {{date}} |
| Spec reviewer | Human | |

---
