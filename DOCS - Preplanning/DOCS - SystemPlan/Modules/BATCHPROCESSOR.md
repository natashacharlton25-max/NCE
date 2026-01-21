# BatchProcessor Module

> **Purpose:** Process large batches of jobs with rate limiting and progress tracking
> **Type:** Service Module
> **Status:** Planning

---

## Overview

BatchProcessor handles large-scale operations that need to be processed in batches - bulk content generation, mass exports, large data migrations. It manages concurrency, rate limits, progress tracking, and failure handling.

```
Large task list → BatchProcessor.process() → Batched execution → Aggregated results
```

**Capabilities:**
- Chunk large jobs into batches
- Concurrent execution with limits
- Progress tracking
- Pause/resume capability
- Failure isolation (one failure doesn't stop batch)
- Result aggregation

---

## Roles & Rules

### BatchProcessor DOES:
- Split large jobs into batches
- Process batches with concurrency limits
- Track progress in real-time
- Handle pause/resume/cancel
- Retry failed items
- Aggregate results

### BatchProcessor does NOT:
- Execute individual job logic (Orchestrator does that)
- Schedule batches (Scheduler does that)
- Handle individual failures (FailureHandler does that)
- Decide batch sizes (configured by caller)

### Boundaries:
- Cannot bypass rate limits
- Cannot exceed max concurrency
- Cannot hide failures (always reports)
- Must track all items to completion or failure

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `process(items, processor, options)` | Process batch of items | items, processor, options | batchResult |
| `createBatch(items, batchSize)` | Split items into batches | items, batchSize | batches |
| `processBatch(batch, processor)` | Process single batch | batch, processor | batchResult |
| `pause(batchId)` | Pause batch processing | batchId | paused |
| `resume(batchId)` | Resume paused batch | batchId | resumed |
| `getProgress(batchId)` | Get current progress | batchId | progress |
| `cancel(batchId)` | Cancel batch processing | batchId | cancelled |
| `retry(batchId, failedOnly)` | Retry failed items | batchId, failedOnly | retryResult |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `BatchProcessor.processWithProgress` | Process with progress updates | createBatch → processBatch (each) → aggregate |
| `BatchProcessor.processWithRetry` | Process with auto-retry on failures | process → retry |

---

## Detailed Function Specs

### process(items, processor, options)

Main batch processing function.

```javascript
// Input
{
  items: [
    { brandId: "brand1", task: "generate-intro" },
    { brandId: "brand2", task: "generate-intro" },
    // ... hundreds more
  ],
  processor: {
    chain: "content-generate",
    paramMapping: {
      brandId: "item.brandId",
      contentType: "item.task"
    }
  },
  options: {
    batchSize: 10,
    concurrency: 3,        // Run 3 items simultaneously
    delayBetweenBatches: 5000,  // 5 second delay between batches
    stopOnError: false,    // Continue despite failures
    retryFailed: true,
    maxRetries: 2
  }
}

// Process
1. Create batches of 10 items each
2. For each batch:
   a. Process up to 3 items concurrently
   b. Track success/failure
   c. Wait delay before next batch
3. Retry failed items (up to 2 times)
4. Aggregate results

// Output
{
  batchId: "batch_abc123",
  status: "completed",
  total: 100,
  processed: 100,
  succeeded: 97,
  failed: 3,
  retried: 5,
  duration: 3600000,  // 1 hour
  results: {
    success: [...],
    failed: [
      { item: {...}, error: "API timeout", attempts: 3 }
    ]
  }
}
```

### getProgress(batchId)

Gets real-time progress of batch.

```javascript
// Input
"batch_abc123"

// Output
{
  batchId: "batch_abc123",
  status: "processing",
  progress: {
    total: 100,
    processed: 45,
    succeeded: 43,
    failed: 2,
    pending: 55,
    percent: 45
  },
  currentBatch: 5,
  totalBatches: 10,
  estimated: {
    remaining: 1980000,  // ~33 minutes
    completionTime: "2026-01-17T15:30:00Z"
  },
  rate: {
    itemsPerMinute: 15,
    successRate: 0.96
  }
}
```

### pause(batchId)

Pauses batch processing.

```javascript
// Input
"batch_abc123"

// Output
{
  paused: true,
  batchId: "batch_abc123",
  pausedAt: "2026-01-17T14:45:00Z",
  progress: {
    processed: 45,
    pending: 55
  },
  resumable: true
}
```

### retry(batchId, failedOnly)

Retries failed items.

```javascript
// Input
{
  batchId: "batch_abc123",
  failedOnly: true
}

// Output
{
  retryBatchId: "batch_abc123_retry1",
  itemsToRetry: 3,
  status: "processing"
}
```

---

## Batch States

| State | Description |
|-------|-------------|
| `created` | Batch created, not started |
| `processing` | Currently processing |
| `paused` | Paused by user |
| `completed` | All items processed |
| `failed` | Batch failed (stopOnError=true) |
| `cancelled` | Cancelled by user |

---

## Rate Limiting

```javascript
// Rate limiting options
{
  rateLimit: {
    maxPerMinute: 60,    // Max 60 items per minute
    maxPerHour: 1000,    // Max 1000 items per hour
    cooldownOnLimit: 60000  // Wait 1 minute if limit hit
  }
}
```

---

## Use Cases

| Use Case | Configuration |
|----------|---------------|
| Bulk content generation | batchSize: 10, concurrency: 3 |
| Mass email sending | batchSize: 100, rateLimit: 100/min |
| Data migration | batchSize: 50, stopOnError: false |
| Image processing | batchSize: 5, concurrency: 2 |

---

## Config

### module.config.json

```json
{
  "module": "BatchProcessor",
  "version": "1.0.0",
  "description": "Process large batches with rate limiting",
  "type": "service",

  "functions": [
    {
      "name": "process",
      "description": "Process batch of items",
      "input": ["items", "processor", "options"],
      "output": "batchResult"
    },
    {
      "name": "createBatch",
      "description": "Split items into batches",
      "input": ["items", "batchSize"],
      "output": "batches"
    },
    {
      "name": "processBatch",
      "description": "Process single batch",
      "input": ["batch", "processor"],
      "output": "batchResult"
    },
    {
      "name": "pause",
      "description": "Pause batch processing",
      "input": ["batchId"],
      "output": "paused"
    },
    {
      "name": "resume",
      "description": "Resume paused batch",
      "input": ["batchId"],
      "output": "resumed"
    },
    {
      "name": "getProgress",
      "description": "Get current progress",
      "input": ["batchId"],
      "output": "progress"
    },
    {
      "name": "cancel",
      "description": "Cancel batch processing",
      "input": ["batchId"],
      "output": "cancelled"
    },
    {
      "name": "retry",
      "description": "Retry failed items",
      "input": ["batchId", "failedOnly"],
      "output": "retryResult"
    }
  ],

  "steps": [
    {
      "name": "processWithProgress",
      "description": "Process with progress updates",
      "sequence": ["createBatch", "processBatch"]
    },
    {
      "name": "processWithRetry",
      "description": "Process with auto-retry",
      "sequence": ["process", "retry"]
    }
  ],

  "config": {
    "defaultBatchSize": 10,
    "defaultConcurrency": 3,
    "maxConcurrency": 10,
    "defaultRetries": 2,
    "progressUpdateInterval": 5000
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Orchestrator | Running chains for each item |
| Logger | Progress logging |
| FileManager | Storing batch state |

---

## Used By

| Module | How |
|--------|-----|
| Bulk operations chains | Mass processing |
| Migration chains | Data migrations |
| Scheduler | Scheduled batch jobs |

---

*Last updated: 2026-01-17*
