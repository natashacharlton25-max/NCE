# FailSys Service Plan

> **Purpose:** Central failure handling for the entire system.
> **Level:** System Service
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

FailSys is the central failure management system. ALL failures from ANY service are reported to FailSys. It holds all retry rules, tracks patterns, and can halt the system if critical failures occur.

**Key Principle:** FailSys owns ALL failure logic. Services report failures, FailSys decides what to do.

---

## Responsibility

| Does | Does NOT |
|------|----------|
| Receive failure reports from ALL services | Fix failures itself |
| Hold ALL retry rules and logic | Know how to perform retries (routes back) |
| Store failed data for manual review | Validate data (Archivist does) |
| Track failure patterns across system | Write to final storage (Writer does) |
| Detect system-wide issues | Make business decisions |
| Halt system on critical failures | - |
| Route retry requests to correct service | - |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              ANY SERVICE                                     │
│  (Archivist, Parsers, Fetchers, Builders, Librarian, etc.)                  │
└────────────────────────────────────┬────────────────────────────────────────┘
                                     │ Failure occurs
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                               FAILSYS                                        │
│                                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   RECEIVE   │  │    RULES    │  │   STORAGE   │  │   PATTERN   │        │
│  │             │  │   ENGINE    │  │             │  │   DETECTOR  │        │
│  │ • Log       │  │             │  │ • Failed    │  │             │        │
│  │ • Classify  │  │ • Retry?    │  │   data      │  │ • Same      │        │
│  │ • Enrich    │  │ • How many? │  │ • History   │  │   error?    │        │
│  │             │  │ • Escalate? │  │ • Manual    │  │ • System    │        │
│  │             │  │ • Halt?     │  │   review    │  │   health    │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                                              │
└────────────────────────────────────┬────────────────────────────────────────┘
                                     │
              ┌──────────────────────┼──────────────────────┐
              ▼                      ▼                      ▼
       ┌─────────────┐        ┌─────────────┐        ┌─────────────┐
       │   RETRY     │        │   STORE     │        │   HALT      │
       │   (route    │        │   (manual   │        │   (system   │
       │   back)     │        │   review)   │        │   stop)     │
       └─────────────┘        └─────────────┘        └─────────────┘
```

---

## API Design

```typescript
// lib/server/services/failsys.ts

interface FailureReport {
  service: string;           // Which service failed
  operation: string;         // What operation failed
  error: Error | string;     // The error
  data?: unknown;            // Data that caused failure (for retry)
  context?: Record<string, unknown>;  // Additional context
  severity?: FailureSeverity;
  timestamp?: string;        // Auto-filled if not provided
}

type FailureSeverity = 'low' | 'medium' | 'high' | 'critical';

interface FailureRecord extends FailureReport {
  id: string;
  timestamp: string;
  retryCount: number;
  status: FailureStatus;
  resolution?: string;
}

type FailureStatus =
  | 'pending'       // Awaiting decision
  | 'retrying'      // Retry in progress
  | 'stored'        // Stored for manual review
  | 'resolved'      // Fixed
  | 'dismissed';    // Ignored

interface RetryRule {
  service: string;
  operation?: string;        // Specific operation or '*' for all
  maxRetries: number;
  delayMs: number;           // Delay between retries
  backoffMultiplier?: number; // Exponential backoff
  onMaxRetries: 'store' | 'escalate' | 'dismiss';
}

interface SystemHealth {
  status: 'healthy' | 'degraded' | 'critical' | 'halted';
  failureCount: number;
  recentFailures: FailureRecord[];
  patterns: FailurePattern[];
}

interface FailurePattern {
  type: string;
  count: number;
  services: string[];
  suggestion: string;
}

// Core functions
function report(failure: FailureReport): Promise<FailureRecord>;
function getStatus(failureId: string): Promise<FailureRecord | null>;
function resolve(failureId: string, resolution: string): Promise<void>;
function dismiss(failureId: string, reason: string): Promise<void>;

// Rules management
function addRule(rule: RetryRule): void;
function removeRule(service: string, operation?: string): void;
function getRules(): RetryRule[];

// System health
function getHealth(): SystemHealth;
function isHealthy(): boolean;
function halt(reason: string): void;
function resume(): void;

// Query
function getFailures(filter?: FailureFilter): Promise<FailureRecord[]>;
function getFailuresForManualReview(): Promise<FailureRecord[]>;
function getFailureStats(): Promise<FailureStats>;

// Cleanup
function clearResolved(olderThan?: number): Promise<number>;
```

---

## Functions

### report(failure)

Report a failure from any service.

```typescript
// From Archivist when validation fails
await FailSys.report({
  service: 'archivist',
  operation: 'validate',
  error: 'Missing required field: name',
  data: invalidData,
  context: { category: 'brand-config' },
  severity: 'medium'
});
```

### Rules Engine

FailSys applies rules to determine what to do with failures:

```typescript
// Default rules (configurable)
const DEFAULT_RULES: RetryRule[] = [
  {
    service: 'fetcher',
    operation: '*',
    maxRetries: 3,
    delayMs: 1000,
    backoffMultiplier: 2,  // 1s, 2s, 4s
    onMaxRetries: 'store'
  },
  {
    service: 'archivist',
    operation: 'validate',
    maxRetries: 0,         // Don't retry validation failures
    delayMs: 0,
    onMaxRetries: 'store'  // Store for manual review
  },
  {
    service: 'parser',
    operation: '*',
    maxRetries: 1,
    delayMs: 500,
    onMaxRetries: 'store'
  }
];
```

### Pattern Detection

FailSys detects patterns across the system:

```typescript
// If 5+ failures from same service in 1 minute → degraded
// If 10+ failures across any services in 1 minute → critical
// If same error message 3+ times → pattern detected

const health = FailSys.getHealth();
// {
//   status: 'degraded',
//   failureCount: 7,
//   patterns: [
//     {
//       type: 'repeated_error',
//       count: 5,
//       services: ['fetcher'],
//       suggestion: 'Check network connectivity'
//     }
//   ]
// }
```

### System Halt

FailSys can halt the entire system:

```typescript
// Automatic halt conditions:
// - 3+ critical failures in 1 minute
// - Same error 10+ times in 5 minutes
// - Manual halt requested

FailSys.halt('Too many API failures - possible rate limiting');

// Check before operations
if (!FailSys.isHealthy()) {
  throw new Error('System halted: ' + FailSys.getHealth().status);
}
```

---

## Failure Flow

### 1. Failure Reported

```typescript
// Service detects failure
try {
  await archivist.store(data);
} catch (error) {
  await FailSys.report({
    service: 'generator',
    operation: 'store-section',
    error,
    data,
    severity: 'medium'
  });
}
```

### 2. FailSys Processes

```
1. Log failure
2. Classify severity
3. Check patterns
4. Look up rules
5. Decide action:
   - Retry → Route back to service
   - Store → Save for manual review
   - Escalate → Notify / alert
   - Halt → Stop system
```

### 3. Retry (if applicable)

```typescript
// FailSys routes retry back to service
// Service must register a retry handler

FailSys.registerRetryHandler('generator', 'store-section', async (data) => {
  // Re-attempt the operation
  return await archivist.store(data);
});
```

---

## Storage

FailSys has its own storage for failed data:

```
/_system/failsys/
  failures/
    2026-01-16/
      fail_abc123.json     # Failure record
      fail_abc123.data.json # Original data (if included)
  rules.json               # Configured rules
  health.json              # Current health status
  patterns.json            # Detected patterns
```

### Failure Record

```json
{
  "id": "fail_abc123",
  "service": "archivist",
  "operation": "validate",
  "error": "Missing required field: name",
  "severity": "medium",
  "timestamp": "2026-01-16T10:30:00Z",
  "retryCount": 0,
  "status": "stored",
  "context": {
    "category": "brand-config",
    "brandPath": "/brands/acme"
  },
  "dataRef": "fail_abc123.data.json"
}
```

---

## Manual Review

Failures stored for manual review can be:

1. **Viewed** - See what failed and why
2. **Retried** - Attempt again after fixing
3. **Resolved** - Mark as fixed (with notes)
4. **Dismissed** - Ignore (with reason)

```typescript
// Get failures needing review
const pending = await FailSys.getFailuresForManualReview();

// After fixing, resolve
await FailSys.resolve('fail_abc123', 'Fixed missing name field');

// Or dismiss if not important
await FailSys.dismiss('fail_abc124', 'Duplicate entry, already processed');
```

---

## Implementation Notes

### File Location

```
lib/server/services/failsys.ts
```

### Dependencies

- Writer (for failure storage)
- No external packages

### Size Estimate

~400-500 lines of code

---

## Build Phases

### F1: Core Reporting
- `report()` function
- Failure record creation
- Basic logging
- Storage to file

### F2: Rules Engine
- Rule types and storage
- `addRule()`, `getRules()`
- Rule matching logic
- Default rules

### F3: Pattern Detection
- Pattern types
- Detection algorithms
- Health status calculation
- `getHealth()`, `isHealthy()`

### F4: Retry System
- Retry handler registration
- Retry execution
- Backoff logic
- Max retry handling

### F5: System Control
- `halt()`, `resume()`
- Auto-halt conditions
- Query functions
- Cleanup

---

## Testing

| Test | Description |
|------|-------------|
| Report failure | Creates record, stores |
| Apply retry rule | Retries correct number of times |
| No retry rule | Stores immediately |
| Pattern detection | Detects repeated errors |
| Health degraded | Multiple failures trigger degraded |
| Health critical | Many failures trigger critical |
| Auto halt | Critical triggers halt |
| Manual resolve | Marks as resolved |
| Manual dismiss | Marks as dismissed |
| Query failures | Filters by service/status |
| Cleanup old | Removes resolved failures |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [ARCHIVIST-PLAN.md](./ARCHIVIST-PLAN.md) - Reports validation failures
- [WRITER-PLAN.md](./WRITER-PLAN.md) - Used for failure storage

---

*FailSys is the safety net - no failure goes untracked.*
