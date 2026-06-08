# System Architecture

> Defines the core pipeline, hierarchy, and separation of concerns.

---

## Pipeline Hierarchy

```
JOB
 └→ Task Manager (what + order)
     └→ Systems (how)
         └→ System Orchestrator (coordinates)
             ├→ Manager A (decides)
             │   ├→ Workers (does)
             │   └→ Functions (utilities)
             ├→ Manager B (decides)
             │   ├→ Workers (does)
             │   └→ Functions (utilities)
             └→ Manager C (decides)
                 ├→ Workers (does)
                 └→ Functions (utilities)
                        │
                        ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                      CROSS-CUTTING SERVICES                                  │
│                                                                              │
│  MANAGERS:                                                                   │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐   │
│  │   API   │ │   DB    │ │  Cache  │ │   Log   │ │  File   │ │ Config  │   │
│  │ Manager │ │ Manager │ │ Manager │ │ Manager │ │ Manager │ │ Manager │   │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘   │
│  ┌─────────┐                                                                │
│  │  Event  │                                                                │
│  │ Manager │                                                                │
│  └─────────┘                                                                │
│                                                                              │
│  VALIDATORS (memory + persist to SQLite):                                   │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐                                        │
│  │API Rate │ │  Token  │ │ Budget  │                                        │
│  └─────────┘ └─────────┘ └─────────┘                                        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
                    │           │           │           │
                    ▼           ▼           ▼           ▼
               External      SQLite      Memory     Filesystem
                 APIs
```

---

## Layer Definitions

| Layer | Role | Responsibility |
|-------|------|----------------|
| **Job** | The goal | High-level request: "Build a brand", "Create social campaign" |
| **Task Manager** | Planner | Breaks job into tasks, determines order, manages dependencies |
| **System** | Domain | Owns a capability, provides API to Task Manager |
| **System Orchestrator** | Coordinator | Routes to managers within system, handles system-level errors |
| **Manager** | Decision maker | Owns a sub-domain, routes to workers, handles domain errors (3-4 per system) |
| **Worker** | Executor | Does the work via cross-cutting services |
| **Function** | Utility | Pure transformation, stateless, no side effects |
| **Cross-Cutting Services** | Gateway | All access to external resources (APIs, DB, files, etc.) |

---

## Separation of Concerns

| Concern | Where | Description |
|---------|-------|-------------|
| **WHAT** (logic) | TypeScript | Behavior, flow, decision logic |
| **HOW** (params) | JSON config | Settings, thresholds, endpoints, parameters |
| **STATE** (runtime) | SQLite | Jobs, tasks, logs, costs, progress |

---

## Layer Details

### Job
- Represents a complete goal or request
- Has a type, status, config, and results
- Stored in SQLite `jobs` table
- Example: "Build brand identity for client X"

### Task Manager
- Receives a Job, breaks it into ordered Tasks
- Reads task chain definitions from JSON config
- Routes each task to the appropriate System
- Tracks task completion and dependencies
- Handles task-level failures and retries

### System
- Owns a single functional domain
- Exposes a clean API to Task Manager
- Contains: Manager + Workers + Functions
- Config stored in `/config/systems/{system}.json`

### Manager
- Multiple per System (typically 3-4)
- Each manager owns a specific domain within the system
- **Queries validators to make decisions** (rate limits, tokens, budget)
- **Can coordinate with other managers** for resource planning
- Decides which Worker(s) to use based on validator inputs
- Handles errors within its domain
- Routes based on config rules + validator state
- Never calls external APIs directly
- Coordinated by System Orchestrator

### Manager-to-Manager Coordination
Managers can talk to each other for resource planning:
- Batch Manager → AI Manager: "Can I reserve 40k tokens for batch #123?"
- AI Manager → Validators: Reserve tokens, worker, rate capacity
- AI Manager → Batch Manager: "Reserved. Proceed."

This enables:
- **Resource reservation** before starting large jobs
- **Priority handling** for batch operations
- **Guaranteed capacity** for critical workflows

### Worker
- Does the actual work - **no decision making**
- Gets assigned by Manager (already validated)
- Executes task and reports result back
- Reports actual usage (tokens, cost) to Manager
- Manager updates validators with actuals

### Function
- Pure utility
- Input → Output
- No side effects
- No API calls
- No I/O
- Shared across workers

---

## Example Flow

```
JOB: "Create brand colour palette"
 │
 └→ Task Manager
     │
     ├→ Task 1: Get brand values
     │   └→ Brand System
     │       ├→ Manager: route to brand data worker
     │       └→ Worker: fetch brand values from DB
     │
     ├→ Task 2: Generate palette
     │   └→ Colour System
     │       ├→ Manager: route to palette worker
     │       ├→ Worker: call AI, generate colours
     │       └→ Function: colour mixing utilities
     │
     ├→ Task 3: Check accessibility
     │   └→ Colour System
     │       ├→ Manager: route to accessibility worker
     │       └→ Function: WCAG contrast checker
     │
     └→ Task 4: Save palette
         └→ Database System
             ├→ Manager: route to write worker
             └→ Worker: insert into SQLite
```

---

## Rules

1. **Managers never call external APIs** - they coordinate, not execute
2. **Workers execute, don't decide** - managers assign after validation
3. **Workers are stateless** - state lives in SQLite
4. **Functions have no side effects** - pure transformation only
5. **Task Manager coordinates task flow** - but managers can coordinate with each other for resources
6. **Config is separate from code** - TypeScript = what, JSON = how
7. **All runtime state in SQLite** - jobs, tasks, logs, costs
8. **All external resources via Cross-Cutting Services** - no direct access
9. **Reservations for batches** - reserve before starting, release on complete
10. **Model consistency in batches** - can switch before start, never during

---

## Cross-Cutting Services

These sit between systems and external resources. Everything routes through them - no direct access.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          CROSS-CUTTING SERVICES                              │
│                                                                              │
│  MANAGERS:                                                                   │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │ API Manager │  │ DB Manager  │  │Cache Manager│  │ Log Manager │         │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                          │
│  │File Manager │  │Config Managr│  │Event Manager│                          │
│  └─────────────┘  └─────────────┘  └─────────────┘                          │
│                                                                              │
│  VALIDATORS (check before action, track state):                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                          │
│  │  API Rate   │  │   Token     │  │   Budget    │                          │
│  │  Validator  │  │  Validator  │  │  Validator  │                          │
│  └─────────────┘  └─────────────┘  └─────────────┘                          │
└─────────────────────────────────────────────────────────────────────────────┘
```

### API Manager
- Manages API configuration (not calls)
- Stores: keys, endpoints, auth methods, timeouts
- Workers get config: `api.getConfig('google')`
- Does NOT make calls - workers do after validator checks

### Validators (Category)
Validators provide state for manager decisions AND check before actions. They:
- Store running totals in **memory** (fast)
- Persist state to **SQLite via Log Manager** (durable)
- **Provide data to managers** for routing decisions
- Return true/false for pre-action checks
- **Support reservations** for batch operations

| Validator | Provides to Manager | Tracks | Reservations |
|-----------|---------------------|--------|--------------|
| **API Rate Validator** | Which providers have capacity? | Requests per minute per provider | Reserve rate slots by batch ID |
| **Token Validator** | Which models have tokens? | Token usage per model | Reserve tokens by batch ID |
| **Budget Validator** | How much budget remains? | Spend per day/week/month | Reserve budget by batch ID |

**Validator Operations:**
```
// Query state
validator.getAvailable()             → Current capacity
validator.getReservations()          → Active reservations
validator.getPriorityQueue()         → Waiting batches

// Check & record
validator.canProceed(params)         → true/false
validator.record(params)             → Update used

// Reservations (for batches)
validator.reserve(batchId, amount)   → Reserve capacity
validator.release(batchId)           → Release reservation
validator.consume(batchId, actual)   → Use from reservation

// Priority queue (for paused batches)
validator.registerPriority(batchId, amount, priority)  → Queue for next reset
validator.onReset(callback)          → Notify when capacity available
```

**Manager Decision Flow:**
```
1. manager.getState()                    → Query validators
2. apiRateValidator.getAvailable()       → {google: 5/10, openai: 2/60}
3. tokenValidator.getAvailable()         → {gpt4o: 45k/90k, claude: 12k/40k}
4. budgetValidator.getRemaining()        → {daily: $1.50, monthly: $358}
5. manager.decide()                      → Route to best available option
```

**Manager Pre-Action Check Flow:**
```
1. manager calls validator.canProceed(params)  → Check memory → true/false
2. manager calls validator.record(params)      → Update memory (estimated)
3. manager assigns worker
4. worker executes, returns actual
5. manager calls validator.recordActual()      → Update memory (actual)
6. validator.persist()                         → Log Manager → SQLite (async)
```

**Example - API Rate Validator:**
```
Google: 9/10 RPM (AI: 3, Content: 2, Social: 4)
Gemini: 15/15 RPM (full - next caller waits)
```

**Example - Token Validator:**
```
GPT-4o: 45,000/90,000 TPM used
Claude: 12,000/40,000 TPM used
```

**Example - Budget Validator:**
```
Daily: $8.50/$10.00 (85% - alert sent)
Monthly: $142/$500 (28%)
```

---

## Cost Tracking (Shadow Billing)

Track ALL costs from day one, even on free tier. This provides:
- Real cost data for future budgeting
- Quality vs cost analysis
- Job profitability insights
- Accurate forecasting

### Shadow Billing Concept

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  EVERY API CALL LOGS COST - FREE OR PAID                                    │
│                                                                              │
│  Free Tier Call:                                                            │
│    actual_charged: $0.00                                                    │
│    would_cost:     $0.15                                                    │
│    tier: "free"                                                             │
│                                                                              │
│  Paid Tier Call:                                                            │
│    actual_charged: $0.15                                                    │
│    would_cost:     $0.15                                                    │
│    tier: "paid"                                                             │
│                                                                              │
│  Result: Full cost visibility regardless of billing tier                    │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Cost Record Structure

```
Every API call logs to SQLite:
{
  job_id: "job-123",
  task_id: "task-456",
  provider: "openai",
  model: "gpt-4o",

  // Usage
  tokens_input: 1500,
  tokens_output: 800,

  // Cost
  would_cost: 0.15,        // What this WOULD cost at paid rates
  actual_charged: 0.00,    // What was actually charged (free tier = 0)
  tier: "free",            // "free" | "paid" | "reserved_credits"

  // Quality tracking
  quality_score: null,     // Filled in later after review

  timestamp: "2026-01-18T10:30:00Z"
}
```

### Job Cost Summary

```
Job: "Build Brand for Client X"
Status: Complete

┌─────────────────────────────────────────────────────────────────────────────┐
│  COST BREAKDOWN                                                              │
│                                                                              │
│  Would Cost (at paid rates):     $12.45                                     │
│  Actually Charged:               $0.00 (free tier)                          │
│  Savings from free tier:         $12.45                                     │
│                                                                              │
│  By Task:                                                                    │
│  ├── Brand values generation     $2.30 (15 AI calls)                       │
│  ├── Colour palette              $0.85 (6 AI calls)                        │
│  ├── Typography selection        $0.45 (3 AI calls)                        │
│  ├── Logo concepts               $5.20 (12 image generations)              │
│  ├── Content writing             $3.15 (20 AI calls)                       │
│  └── Quality checks              $0.50 (5 AI calls)                        │
│                                                                              │
│  By Provider:                                                                │
│  ├── OpenAI:      $8.20 (65%)                                              │
│  ├── Anthropic:   $2.80 (22%)                                              │
│  ├── DALL-E:      $1.20 (10%)                                              │
│  └── Other:       $0.25 (3%)                                               │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Cost vs Quality Analysis

```
Track quality alongside cost for optimization:

┌─────────────────────────────────────────────────────────────────────────────┐
│  COST vs QUALITY REPORT                                                      │
│                                                                              │
│  Task Type: "Blog Post Generation"                                          │
│  Sample: Last 50 posts                                                      │
│                                                                              │
│  Model         │ Avg Cost │ Avg Quality │ Cost/Quality │ Recommendation    │
│  ──────────────┼──────────┼─────────────┼──────────────┼───────────────────│
│  GPT-5         │ $0.45    │ 9.2/10      │ $0.049       │ Best quality      │
│  Claude-Opus   │ $0.38    │ 9.0/10      │ $0.042       │ Best value ✓      │
│  GPT-4o        │ $0.15    │ 8.1/10      │ $0.019       │ Budget option     │
│  GPT-4o-mini   │ $0.02    │ 6.5/10      │ $0.003       │ Draft only        │
│                                                                              │
│  Insight: Claude-Opus offers best quality-per-dollar for blog posts        │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Forecasting Dashboard

```
Based on current usage patterns:

┌─────────────────────────────────────────────────────────────────────────────┐
│  BUDGET FORECAST                                                             │
│                                                                              │
│  Current Monthly Usage (would-cost):     $342.50                            │
│  Growth Rate:                            +15%/month                         │
│                                                                              │
│  Projected Costs:                                                           │
│  ├── Next month:      $394                                                 │
│  ├── 3 months:        $520                                                 │
│  ├── 6 months:        $790                                                 │
│  └── 12 months:       $1,450                                               │
│                                                                              │
│  Free Tier Status:                                                          │
│  ├── OpenAI:     Using 85% of free quota                                   │
│  ├── Anthropic:  Using 60% of free quota                                   │
│  └── Estimated runway: 2 months before paid tier needed                    │
│                                                                              │
│  Recommended Monthly Budget: $500 (with 25% buffer)                        │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Quality Scoring

```
Quality scores added after output review:

Manual Review:
  - Human rates output 1-10
  - Logged against job/task

Automated Metrics:
  - Grammar score (Language Check system)
  - Brand alignment score
  - Readability score
  - SEO score (for content)

Composite Quality:
  quality_score = (manual * 0.5) + (automated * 0.5)
```

### Cost Analytics Operations

```
// Job-level
costAnalytics.getJobCost(jobId)              → {wouldCost, actualCharged, breakdown}
costAnalytics.getJobsByType(type)            → Cost per job type

// Quality correlation
costAnalytics.getCostVsQuality(taskType)     → Model comparison with quality scores
costAnalytics.getBestValueModel(taskType)    → Recommended model for task

// Forecasting
costAnalytics.getMonthlyTrend()              → Usage over time
costAnalytics.getForecast(months)            → Projected costs
costAnalytics.getRunwayEstimate()            → When free tier runs out

// Optimization
costAnalytics.getSavingsOpportunities()      → Where cheaper models would work
costAnalytics.getOverspendAlerts()           → Tasks using expensive models unnecessarily
```

---

## Job Pre-Planning (Token & Cost Estimation)

Before a job starts, estimate total tokens and cost to:
- Check capacity is available
- Reserve resources upfront
- Get budget approval if needed
- Choose optimal model/provider

### Estimation Sources

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  WHERE ESTIMATES COME FROM                                                   │
│                                                                              │
│  1. TASK TEMPLATES (baseline)                                               │
│     Each task type has default token estimates in config                    │
│     e.g., "generate-tagline": { estTokensIn: 500, estTokensOut: 100 }      │
│                                                                              │
│  2. JOB TYPE PROFILES (aggregated)                                          │
│     Job types define which tasks + multipliers                              │
│     e.g., "brand-build": 15 AI tasks × avg 2000 tokens = ~30k tokens       │
│                                                                              │
│  3. HISTORICAL DATA (learned)                                               │
│     Actual usage from past jobs refines estimates                           │
│     e.g., "brand-build" jobs actually average 28k tokens ±15%              │
│                                                                              │
│  4. INPUT ANALYSIS (dynamic)                                                │
│     Analyze actual input to adjust estimate                                 │
│     e.g., "10 blog posts" → 10 × 4000 tokens = 40k tokens                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Task Token Templates

```json
// /config/tasks/token-estimates.json
{
  "tasks": {
    "generate-tagline": {
      "estTokensIn": 500,
      "estTokensOut": 100,
      "model": "any",
      "confidence": 0.9
    },
    "generate-blog-post": {
      "estTokensIn": 1500,
      "estTokensOut": 3000,
      "model": "high-quality",
      "confidence": 0.8
    },
    "analyse-brand-values": {
      "estTokensIn": 2000,
      "estTokensOut": 1500,
      "model": "reasoning",
      "confidence": 0.85
    },
    "generate-colour-palette": {
      "estTokensIn": 800,
      "estTokensOut": 500,
      "model": "any",
      "confidence": 0.9
    }
  }
}
```

### Job Type Profiles

```json
// /config/jobs/job-profiles.json
{
  "brand-build": {
    "tasks": [
      { "type": "analyse-brand-values", "count": 3 },
      { "type": "generate-tagline", "count": 5 },
      { "type": "generate-colour-palette", "count": 2 },
      { "type": "generate-typography", "count": 2 },
      { "type": "generate-content", "count": 8 }
    ],
    "estimatedTotalTokens": 32000,
    "estimatedCost": 1.20,
    "confidence": 0.8,
    "historicalAvg": 28500,
    "historicalStdDev": 4200
  },
  "blog-post-batch": {
    "tasks": [
      { "type": "generate-blog-post", "countPer": 1 }  // multiplied by batch size
    ],
    "tokensPerItem": 4500,
    "costPerItem": 0.15,
    "confidence": 0.85
  }
}
```

### Pre-Flight Estimation Flow

```
Job Request: "Build brand for TechStartup Inc"
Job Type: brand-build

TASK MANAGER (pre-flight):

1. Load job profile:
   - brand-build: ~32k tokens estimated

2. Analyze input for adjustments:
   - Brand name length: normal
   - Industry complexity: tech (moderate)
   - No special requirements
   - Adjustment: +10% buffer

3. Calculate estimate:
   ┌────────────────────────────────────────────────────────┐
   │ JOB ESTIMATE: brand-build                              │
   │                                                        │
   │ Task Breakdown:                                        │
   │ ├── Brand values (3×)      6,000 tokens    $0.22     │
   │ ├── Taglines (5×)          3,000 tokens    $0.11     │
   │ ├── Colour palette (2×)    2,600 tokens    $0.10     │
   │ ├── Typography (2×)        2,400 tokens    $0.09     │
   │ └── Content (8×)          18,000 tokens    $0.65     │
   │                           ─────────────    ─────      │
   │ Subtotal:                  32,000 tokens    $1.17     │
   │ Buffer (+10%):              3,200 tokens    $0.12     │
   │                           ─────────────    ─────      │
   │ TOTAL ESTIMATE:            35,200 tokens    $1.29     │
   │                                                        │
   │ Confidence: 80%                                        │
   │ Range: 28k - 42k tokens ($1.03 - $1.55)              │
   └────────────────────────────────────────────────────────┘

4. Check validators:
   - tokenValidator: 85k available ✓ (need 35k)
   - budgetValidator: $39.50 daily left ✓ (need $1.29)
   - Job budget: $10 max ✓ (need $1.29)

5. Decision: PROCEED
   - Reserve 35,200 tokens
   - Reserve $1.29 budget
```

### Batch Estimation

```
Job Request: "Generate 25 blog posts about AI trends"
Job Type: blog-post-batch
Batch Size: 25

TASK MANAGER (pre-flight):

1. Load job profile:
   - blog-post-batch: 4,500 tokens per item

2. Calculate batch:
   ┌────────────────────────────────────────────────────────┐
   │ BATCH ESTIMATE: 25 blog posts                          │
   │                                                        │
   │ Per Item:                                              │
   │ ├── Input tokens:    1,500                            │
   │ ├── Output tokens:   3,000                            │
   │ └── Total:           4,500 tokens @ $0.15             │
   │                                                        │
   │ Batch Total:                                           │
   │ ├── Items:           25                                │
   │ ├── Tokens:          112,500                          │
   │ ├── Cost:            $3.75                            │
   │ └── Buffer (+15%):   $4.31                            │
   │                                                        │
   │ Resource Check:                                        │
   │ ├── Tokens needed:   112,500                          │
   │ ├── Tokens available: 85,000 ✗ (need 27,500 more)    │
   │                                                        │
   │ Options:                                               │
   │ a) Partial: Do 18 now (81k), 7 after reset           │
   │ b) Wait: Full batch after 4hr token reset            │
   │ c) Split models: 18× GPT-5, 7× Claude-Opus           │
   └────────────────────────────────────────────────────────┘
```

### Historical Learning

```
After each job completes, update estimates:

Job Complete: brand-build #234
  Estimated: 35,200 tokens
  Actual:    31,450 tokens
  Variance:  -10.7%

Update Profile:
  - Add to historical data
  - Recalculate average: 28,500 → 28,650
  - Recalculate stdDev: 4,200 → 4,150
  - Adjust confidence: 80% → 81%

Over time, estimates become more accurate.
```

### Estimation Operations

```
// Pre-flight estimation
jobEstimator.estimate(jobType, input)     → {tokens, cost, confidence, breakdown}
jobEstimator.estimateBatch(jobType, count) → {tokens, cost, perItem}

// Check feasibility
jobEstimator.canComplete(estimate)         → {feasible, blockers, options}
jobEstimator.getOptions(estimate)          → {partial, wait, alternatives}

// Historical
jobEstimator.getHistoricalAvg(jobType)     → {avgTokens, avgCost, stdDev}
jobEstimator.recordActual(jobId, actual)   → Updates historical data

// Breakdown
jobEstimator.getTaskBreakdown(jobType)     → Per-task token estimates
jobEstimator.adjustForInput(estimate, input) → Dynamic adjustments
```

### Estimation Confidence Levels

| Confidence | Meaning | Buffer Added |
|------------|---------|--------------|
| 90%+ | Well-known task, stable estimates | +5% |
| 80-89% | Good history, some variance | +10% |
| 70-79% | Moderate history, variable inputs | +15% |
| 60-69% | Limited history, new task type | +20% |
| <60% | Unknown, use conservative estimate | +30% |

---

## Budget Authorization

As system grows from free tier to paid services, budget authorization becomes critical.

### Budget Levels

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BUDGET HIERARCHY                                                            │
│                                                                              │
│  System Daily Budget: $50/day                                               │
│    │                                                                        │
│    ├── Job Budget (per job type)                                           │
│    │     Brand Build: $10 max                                              │
│    │     Blog Post: $0.50 max                                              │
│    │     Social Campaign: $5 max                                           │
│    │                                                                        │
│    ├── Task Budget (inherits from job or explicit)                         │
│    │     AI Call: $0.05-$2.00 depending on model                          │
│    │     Image Gen: $0.02-$0.20 depending on provider                     │
│    │                                                                        │
│    └── Reserved Budget (for batches)                                       │
│          Batch #456: $2.00 reserved                                        │
│                                                                              │
│  Remaining Available: $50 - $8.50 used - $2.00 reserved = $39.50          │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Budget Validator Decision Flow

```
Manager asks: "Can I use GPT-5 for this task? Est cost: $0.15"

Budget Validator checks:
1. Task budget allowed?     → Job allows up to $0.50 per task ✓
2. Job budget remaining?    → Job has $3.20 of $10 left ✓
3. Daily budget remaining?  → $39.50 available ✓
4. Is this a paid service?  → Yes, GPT-5 is paid tier
5. Authorization level?     → Auto-approved under $1.00 ✓

Result: APPROVED
```

### Authorization Levels

| Cost Range | Authorization | Action |
|------------|---------------|--------|
| Free tier | Auto | Always proceed |
| $0.01 - $1.00 | Auto | Proceed if within budget |
| $1.01 - $5.00 | Soft limit | Proceed, log warning, notify if daily > 80% |
| $5.01 - $20.00 | Review | Check if cheaper alternative exists first |
| $20.01+ | Human approval | Queue task, notify human, wait for approval |

### Budget Decision Matrix

```
Manager: "Task needs $0.15, what are my options?"

Budget Validator returns:
┌────────────────────────────────────────────────────────────────────────┐
│ BUDGET DECISION                                                         │
│                                                                         │
│ Requested: $0.15 (GPT-5)                                               │
│                                                                         │
│ Current State:                                                          │
│   Task budget:  $0.50 max     → $0.15 OK ✓                            │
│   Job budget:   $3.20 left    → $0.15 OK ✓                            │
│   Daily budget: $39.50 left   → $0.15 OK ✓                            │
│   Auth level:   Auto (<$1)    → No approval needed ✓                  │
│                                                                         │
│ Alternatives (if budget tight):                                        │
│   GPT-4o-mini: $0.02 (save $0.13)                                     │
│   Claude-Haiku: $0.01 (save $0.14)                                    │
│                                                                         │
│ Decision: PROCEED with GPT-5                                           │
└────────────────────────────────────────────────────────────────────────┘
```

### Budget Constrained Scenarios

```
Scenario 1: TASK OVER JOB BUDGET
─────────────────────────────────
Task needs: $2.00
Job budget left: $0.80

Options:
  a) Use cheaper model ($0.50) → Suggest alternative
  b) Request job budget increase → Human approval
  c) Fail task → Return error to Task Manager

Scenario 2: DAILY BUDGET EXHAUSTED
──────────────────────────────────
Task needs: $0.15
Daily budget left: $0.05

Options:
  a) Wait for daily reset (midnight) → Queue with priority
  b) Use free tier only → Suggest free alternatives
  c) Request budget override → Human approval
  d) Fail task → Return error

Scenario 3: PAID SERVICE, NO AUTHORIZATION
──────────────────────────────────────────
Task needs: $25.00 (large image batch)
Auth level: Human approval required

Options:
  a) Queue task, notify human → Wait for approval
  b) Split into smaller batches under $20 → Auto-approve each
  c) Suggest cheaper provider → Alternative quote
```

### Budget Validator Extended Operations

```
// Query budget state
budgetValidator.getTaskBudget(jobId)           → Max allowed for task
budgetValidator.getJobBudget(jobId)            → Remaining job budget
budgetValidator.getDailyRemaining()            → Daily budget left
budgetValidator.getAuthLevel(amount)           → What approval needed

// Check & authorize
budgetValidator.canProceed(jobId, amount)      → {allowed, reason, alternatives}
budgetValidator.requestApproval(jobId, amount) → Queue for human
budgetValidator.onApproval(callback)           → Notify when approved

// Cheaper alternatives
budgetValidator.getCheaperOptions(task, maxBudget) → Alternative models/providers
budgetValidator.wouldExceedDaily(amount)       → true/false + time to reset
```

### Human Approval Flow

```
Task exceeds auto-approval threshold ($25.00 image batch)

1. Budget Validator: "Needs human approval"
2. Manager: Queue task as "pending-approval"
3. Event Manager: Notify human (email/Slack/dashboard)
4. Human reviews:
   - Task details
   - Cost breakdown
   - Alternatives offered
5. Human approves/rejects/modifies
6. Budget Validator: onApproval triggered
7. Manager: Resume task with approved budget
```

### Caller (per provider)
- Makes actual HTTP calls
- Uses config from API Manager
- Worker calls Caller directly (Manager already validated)
- Flow: `Manager validates → Worker → Caller → External API`

### Database Manager
- All SQLite queries route through here
- Managers call `db.query()` never raw SQL directly
- Centralizes: connections, transactions, migrations, backups
- Flow: `Manager → Database Manager → SQLite`

### Cache Manager
- All caching routes through here
- Managers call `cache.get()` `cache.set()` never direct memory
- Centralizes: TTL, tiers (hot/warm/cold), invalidation, size limits
- Flow: `Manager → Cache Manager → Memory`

### Log Manager
- All logging routes through here
- Everything calls `log.info()` `log.error()` never `console.log()`
- Centralizes: formats, levels, destinations, queries, retention
- Flow: `Anything → Log Manager → Files/SQLite/Console`

### File Manager
- All file operations route through here
- Workers call `file.read()` `file.write()` never `fs` directly
- Centralizes: paths, permissions, temp cleanup, size limits
- Flow: `Worker → File Manager → Filesystem`

### Config Manager
- All config/secrets access routes through here
- Anything calls `config.get('api_key')` never `process.env` directly
- Centralizes: env vars, secrets, feature flags, defaults
- Flow: `Anything → Config Manager → Env/Files`

### Event Manager
- All system events route through here
- Managers emit events, others subscribe
- Centralizes: pub/sub, notifications, triggers
- Flow: `Manager → Event Manager → Subscribers`

---

## Cross-Cutting Summary

| Service | Controls | Never Do Directly |
|---------|----------|-------------------|
| **MANAGERS** | | |
| API Manager | API keys, endpoints, config | Hardcode API details |
| Database Manager | SQLite queries | Raw SQL |
| Cache Manager | Memory caching | Direct memory objects |
| Log Manager | All logging | `console.log()` |
| File Manager | Filesystem ops | `fs.read()`, `fs.write()` |
| Config Manager | Secrets, settings | `process.env` |
| Event Manager | System events | Direct function calls between systems |
| **VALIDATORS** | | |
| API Rate Validator | Requests per provider | Call without checking rate |
| Token Validator | Token usage per model | Call without checking tokens |
| Budget Validator | Spend per period | Call without checking budget |

---

## Flow Example

```
AI Manager receives task: "generate tagline"

MANAGER (decides):
1. Query validators:
   - apiRateValidator.getAvailable()    → {openai: 2/60, anthropic: 58/60, gemini: 0/15}
   - tokenValidator.getAvailable()      → {gpt4o: 45k/90k, claude: 38k/40k, gemini: full}
   - budgetValidator.getRemaining()     → {daily: $1.50}
2. Decide: "Use Anthropic - has capacity and budget"
3. Validate action allowed:
   - apiRateValidator.canProceed('anthropic')    → true
   - tokenValidator.canProceed('claude', est)    → true
   - budgetValidator.canProceed(estCost)         → true
4. Record estimated usage (validators update memory)
5. Assign task to ClaudeWorker

WORKER (executes - no decisions):
1. Receive assignment from Manager
2. Make API call via Caller
3. Return result + actual usage to Manager

MANAGER (updates):
1. Receive result from Worker
2. Update validators with actual usage:
   - tokenValidator.recordActual(actualTokens)
   - budgetValidator.recordActual(actualCost)
3. Validators persist to SQLite via Log Manager
4. Return result to System Orchestrator
```

Worker never touches external resources directly. Everything through cross-cutting services.

---

## Batch Coordination Example

```
Batch Manager receives: "Generate 10 blog posts using GPT-5"

BATCH MANAGER → AI MANAGER (pre-flight check):
1. "Can I reserve 40,000 tokens (10 posts × 4,000 each) for batch #456?"
2. "Need GPT-5 model, high priority"

AI MANAGER (checks & reserves):
1. Query validators:
   - tokenValidator.getAvailable('gpt5')     → 85,000/100,000 available
   - budgetValidator.getRemaining()          → $50 daily remaining
   - apiRateValidator.getAvailable('openai') → 45/60 RPM available
2. Calculate: 40k tokens ≈ $2.00, rate OK
3. Reserve resources:
   - tokenValidator.reserve('batch-456', 40000, 'gpt5')
   - budgetValidator.reserve('batch-456', 2.00)
   - apiRateValidator.reserve('batch-456', 'openai', priority: 'high')
4. Reserve worker: GPT5Worker assigned to batch-456
5. Return to Batch Manager: "Reserved. Proceed."

BATCH MANAGER (executes):
1. For each of 10 posts:
   - AI Manager assigns GPT5Worker (reserved for batch-456)
   - Worker executes, returns result + actual tokens
   - AI Manager: tokenValidator.consume('batch-456', actualTokens)
2. On batch complete:
   - tokenValidator.release('batch-456')    → Release unused
   - budgetValidator.release('batch-456')
   - apiRateValidator.release('batch-456')

RESERVATION STATE (during batch):
┌────────────────────────────────────────────────────────────┐
│ Token Validator - GPT-5                                    │
│                                                            │
│ Total: 100,000 TPM                                         │
│ Used: 45,000                                               │
│ Reserved:                                                  │
│   - batch-456: 40,000 (high priority)                     │
│   - batch-789: 10,000 (normal priority)                   │
│ Available: 5,000 (for non-reserved requests)              │
└────────────────────────────────────────────────────────────┘
```

**Batch Options When Resources Limited:**

```
Batch Manager: "Need 40k tokens for GPT-5, only 20k available"

Option 1: USE EQUIVALENT MODEL (before any content created)
- Ask AI Manager: "What models equal GPT-5 rating?"
- AI Manager: "Claude-Opus (9.5), Gemini-Ultra (9.3) - both have capacity"
- Switch entire batch to alternative model
- RULE: Never change model DURING content creation

Option 2: PARTIAL BATCH
- "Do 5 posts now (20k tokens)"
- Reserve what's available
- Mark batch as partial-complete
- Schedule remainder for later (same model)

Option 3: BATCH-TILL-LIMIT-THEN-WAIT
- Start batch, process until limit hit
- Pause batch, set timer (e.g., 8 hours for token reset)
- Register priority-1 reservation for GPT-5 after reset
- Auto-resume with priority when tokens available

Option 4: WAIT-FOR-FULL
- Don't start until full capacity available
- Register priority reservation for next reset
- Notify when ready to proceed
```

**Model Equivalency Query:**
```
Batch Manager → AI Manager:
  "Need model with rating >= 9.0 for blog posts"
  "GPT-5 unavailable, what alternatives?"

AI Manager → Model Registry:
  getEquivalentModels('gpt5', minRating: 9.0)

Returns:
  ┌─────────────────────────────────────────────────────┐
  │ Model           │ Rating │ Tokens Available │ Cost  │
  ├─────────────────┼────────┼──────────────────┼───────┤
  │ gpt-5           │ 9.5    │ 20k/100k (20%)   │ $0.03 │
  │ claude-opus     │ 9.5    │ 80k/100k (80%)   │ $0.02 │
  │ gemini-ultra    │ 9.3    │ 95k/100k (95%)   │ $0.02 │
  └─────────────────────────────────────────────────────┘

Batch Manager: "Use claude-opus for entire batch"
```

**RULE: Model Consistency**
```
✓ Can switch model BEFORE batch starts
✗ Never switch model DURING batch (content consistency)
✓ Paused batch resumes with SAME model it started with
```

**Priority After Wait:**
```
Batch #456 hit limit at 5/10 posts
  → Paused, waiting for token reset
  → Registered: priority-1 for GPT-5 (needs 20k tokens)

8 hours later, tokens reset:
  → Token Validator: "batch-456 has priority-1, reserving 20k"
  → Batch Manager notified: "Resume batch-456"
  → Remaining 5 posts processed with guaranteed capacity
```

**Benefits:**
- Flexible: partial, pause, or wait options
- No wasted time waiting when partial work possible
- Priority queue ensures paused batches resume first
- Reserved capacity can't be stolen by other requests
- Unused reservations released on completion

---

## Failure Handling

Failures are handled at each level with escalation paths.

### Failure Strategy by Level

| Level | Strategy | Example | Escalation |
|-------|----------|---------|------------|
| **Worker** | Retry 3x with exponential backoff | API timeout, rate limit | → Task failure |
| **Task** | Try alternative worker/provider | Model unavailable | → Job partial failure |
| **Job** | Partial complete or full fail | 8/10 tasks done | → Human notification |
| **System** | Circuit breaker pattern | Provider completely down | → Disable provider, use fallback |

### Retry Strategy

```
Worker Retry (configurable per worker):
  Attempt 1: Immediate
  Attempt 2: Wait 1s
  Attempt 3: Wait 4s (exponential backoff)
  Attempt 4: Fail task

Backoff Formula: delay = baseDelay × (2 ^ attemptNumber)
```

### Task Failure Handling

```
Task fails after retries:
  1. Check if alternative available:
     - Different worker? (e.g., ClaudeWorker instead of OpenAIWorker)
     - Different model? (Manager decision based on validator state)
  2. If alternative exists → retry with alternative
  3. If no alternative → mark task failed
  4. Check if task is "required":
     - Required: Job fails
     - Optional: Job continues without this task
```

### Job Failure Modes

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  JOB FAILURE SCENARIOS                                                       │
│                                                                              │
│  1. REQUIRED TASK FAILS                                                     │
│     → Job status = "failed"                                                 │
│     → All incomplete tasks cancelled                                        │
│     → Completed task outputs preserved                                      │
│     → Human notified with error details                                     │
│                                                                              │
│  2. OPTIONAL TASK FAILS                                                     │
│     → Task status = "failed", job continues                                 │
│     → Job result marked with "partial: true"                                │
│     → Human notified of partial completion                                  │
│                                                                              │
│  3. MULTIPLE OPTIONAL TASKS FAIL                                            │
│     → If failures > threshold (configurable) → Job fails                    │
│     → Otherwise → Job completes as partial                                  │
│                                                                              │
│  4. BUDGET/RESOURCE EXHAUSTED                                               │
│     → Job status = "paused"                                                 │
│     → Wait for resources OR human decision                                  │
│     → Can resume or cancel                                                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Circuit Breaker Pattern

```
Provider experiencing consistent failures:

1. Track failures per provider (last 5 minutes)
2. If failure rate > 50% over 10+ requests:
   - Trip circuit breaker
   - Mark provider as "unhealthy"
   - Route all requests to fallback providers
3. After cooldown period (5 minutes):
   - Allow single test request
   - If success → close breaker, resume normal
   - If failure → extend cooldown

State: CLOSED (normal) → OPEN (blocked) → HALF-OPEN (testing) → CLOSED
```

---

## Recovery / Restart

What happens when system crashes or restarts.

### Startup Recovery Sequence

```
SYSTEM STARTUP:

1. RESTORE VALIDATOR STATE
   - Load latest validator_snapshots from SQLite
   - Load provider_rate_limits current values
   - Restore budget/token counts to memory

2. FIND IN-PROGRESS WORK
   - Query: SELECT * FROM jobs WHERE status = 'running'
   - Query: SELECT * FROM batches WHERE status = 'processing'
   - Query: SELECT * FROM tasks WHERE status = 'running'

3. DETERMINE RECOVERY ACTION
   For each running job:
     - Check last completed task
     - Resume from next task in sequence

   For each running batch:
     - Check which tasks in batch completed
     - Resume incomplete tasks (same model)

   For each running task:
     - Check if output exists → mark complete
     - If no output → reset to pending, retry

4. RESUME PRIORITY QUEUE
   - Load waiting batches from priority_queue
   - Check if resources now available
   - Resume eligible batches

5. MARK SYSTEM READY
   - Accept new jobs
```

### Checkpoint Strategy

```
Checkpoints are implicit based on database state:

- Task completes → output saved to tasks.output
- Batch checkpoint → batch.status + completed task_ids
- Job progress → job.progress percentage
- Validator state → periodic snapshots (every 1 minute)

No explicit transaction logs needed - SQLite IS the checkpoint.
```

### Crash Scenarios

| Scenario | State on Restart | Recovery Action |
|----------|------------------|-----------------|
| Crash during task execution | Task has no output | Reset task to pending, retry |
| Crash after task complete | Task has output | Skip to next task |
| Crash during batch | Some tasks complete | Resume incomplete tasks |
| Crash during API call | No response recorded | Retry the call |
| Crash after API response | Response in task output | Continue normally |

### Idempotency

```
All operations should be idempotent (safe to repeat):

✓ Generating content: May produce different output, but that's OK
✓ Database writes: Upsert patterns prevent duplicates
✓ Cost tracking: Based on actual API response, not pre-recorded
✗ External publishing: NOT idempotent - needs special handling
  → Check published_content table before retry
  → Use content_hash + platform for duplicate detection
  → Track external_id from platform response
```

---

## Archiver

The Archiver runs on job completion to manage data retention and long-term storage.

### Archiver Responsibilities

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  ARCHIVER - TRIGGERED ON JOB COMPLETION                                      │
│                                                                              │
│  1. SNAPSHOT JOB DATA                                                       │
│     - Copy job record to archived_jobs                                      │
│     - Denormalize: total_tasks, costs, tokens                               │
│     - Embed tasks_snapshot (all tasks with outputs)                         │
│     - Embed cost_records_snapshot (all costs for job)                       │
│                                                                              │
│  2. CLEAN UP ACTIVE TABLES                                                  │
│     - Delete from jobs table (now in archive)                               │
│     - Delete from tasks table (in snapshot)                                 │
│     - Delete from task_queue (completed tasks)                              │
│     - Keep batches for X days (configurable)                                │
│                                                                              │
│  3. MANAGE TEMP FILES                                                       │
│     - Delete /storage/temp/{job-id}/ folder                                 │
│     - Move final outputs to /storage/output/{job-id}/                       │
│                                                                              │
│  4. LOG ARCHIVE OPERATION                                                   │
│     - Record in archive_metadata table                                      │
│     - Track what was archived and when                                      │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Archive Trigger Flow

```
Job completes (status = "completed" or "failed")
       │
       ▼
Event Manager: emit("job.completed", {jobId})
       │
       ▼
Archiver subscribes, receives event
       │
       ▼
1. Query all job data (job, tasks, costs)
       │
       ▼
2. Build archive record with denormalized data
       │
       ▼
3. Insert into archived_jobs
       │
       ▼
4. Delete from active tables (transactional)
       │
       ▼
5. Clean temp files, move outputs
       │
       ▼
6. Log to archive_metadata
```

### Retention Policy

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  DATA RETENTION RULES                                                        │
│                                                                              │
│  Active Tables (system.db):                                                  │
│  ├── jobs: Only while running (archived on complete)                        │
│  ├── tasks: Only while job active                                           │
│  ├── batches: 7 days after completion                                       │
│  ├── logs: 30 days (then purge)                                             │
│  ├── error_log: 90 days (then purge)                                        │
│  ├── cost_records: 30 days (then roll up to monthly)                        │
│  └── token_usage: 7 days (then purge)                                       │
│                                                                              │
│  Archive Tables:                                                             │
│  ├── archived_jobs: Forever (contains full job history)                     │
│  └── archive_metadata: Forever (audit trail)                                │
│                                                                              │
│  Files:                                                                      │
│  ├── /storage/temp/{job-id}/: Deleted on archive                           │
│  └── /storage/output/{job-id}/: Configurable (default: 1 year)             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Archiver Configuration

```json
// config/systems/archiver.json
{
  "enabled": true,
  "triggerOn": ["job.completed", "job.failed", "job.cancelled"],

  "retention": {
    "logs": "30d",
    "errorLogs": "90d",
    "batches": "7d",
    "costRecords": "30d",
    "tokenUsage": "7d",
    "tempFiles": "immediate",
    "outputFiles": "365d"
  },

  "archiveOptions": {
    "includeTaskSnapshots": true,
    "includeCostRecords": true,
    "compressSnapshots": false
  },

  "cleanup": {
    "runDaily": true,
    "dailyCleanupTime": "03:00",
    "batchSize": 100
  }
}
```

### Daily Cleanup Job

```
Scheduled at 03:00 daily:

1. Find old data past retention period
2. Archive/delete in batches (prevent lock contention)
3. Vacuum database (reclaim space)
4. Log cleanup summary
```

---

## Job States

Complete state diagram for jobs.

### State Diagram

```
                    ┌─────────────────────────────────────────────┐
                    │                                             │
                    ▼                                             │
┌─────────┐    ┌─────────────┐    ┌─────────┐    ┌───────────┐   │
│ pending │───▶│ validating  │───▶│ running │───▶│ completing│───┤
└─────────┘    └─────────────┘    └─────────┘    └───────────┘   │
                    │                  │              │           │
                    │                  │              │           │
                    ▼                  ▼              ▼           │
               ┌─────────┐       ┌─────────┐    ┌───────────┐    │
               │ rejected│       │ paused  │    │ completed │    │
               └─────────┘       └─────────┘    └───────────┘    │
                                      │              │           │
                                      │              ▼           │
                                      │         ┌─────────┐      │
                                      └────────▶│ failed  │◀─────┘
                                                └─────────┘
```

### State Definitions

| State | Meaning | Next States |
|-------|---------|-------------|
| **pending** | Job created, waiting to start | validating |
| **validating** | Pre-flight checks (budget, capacity) | running, rejected |
| **rejected** | Failed pre-flight (over budget, no capacity) | - (terminal) |
| **running** | Tasks being executed | completing, paused, failed |
| **paused** | Waiting for resources or approval | running, failed, cancelled |
| **completing** | Final tasks running, wrapping up | completed, failed |
| **completed** | All required tasks done | - (terminal) |
| **failed** | Required task failed, unrecoverable | - (terminal) |
| **cancelled** | Manually stopped by user | - (terminal) |

### Pause Triggers

```
Job enters PAUSED when:
  - Daily budget exhausted
  - Rate limit exceeded (waiting for reset)
  - Awaiting human approval (cost > threshold)
  - External dependency unavailable

Job exits PAUSED when:
  - Resources become available
  - Human approves/rejects
  - Manual resume command
  - Timeout expires → failed
```

---

## Task Dependencies

Tasks can depend on multiple other tasks.

### Dependency Rules

```
1. Task runs when ALL dependencies complete successfully
2. If ANY dependency fails:
   - Required dependency → Task cannot run → Job fails
   - Optional dependency → Task runs with partial input
3. Tasks with no dependencies run immediately
4. Tasks with same dependencies can run in parallel
```

### Example: Complex Dependencies

```
Job: "Create Social Post with AI Image"

┌──────────────┐
│ get-brand    │──────────┐
└──────────────┘          │
                          ▼
┌──────────────┐    ┌──────────────┐
│ get-audience │───▶│ write-caption│──────┐
└──────────────┘    └──────────────┘      │
                                          │
┌──────────────┐    ┌──────────────┐      │    ┌──────────────┐
│ get-brand    │───▶│ gen-image    │──────┼───▶│ compose-post │
└──────────────┘    └──────────────┘      │    └──────────────┘
                                          │
┌──────────────┐    ┌──────────────┐      │
│ get-topic    │───▶│ gen-hashtags │──────┘
└──────────────┘    └──────────────┘

compose-post depends on: [write-caption, gen-image, gen-hashtags]
All three must complete before compose-post starts.
```

### Task Chain Config Example

```json
{
  "tasks": [
    { "id": "get-brand", "depends_on": [] },
    { "id": "get-audience", "depends_on": [] },
    { "id": "get-topic", "depends_on": [] },
    { "id": "write-caption", "depends_on": ["get-brand", "get-audience"] },
    { "id": "gen-image", "depends_on": ["get-brand"] },
    { "id": "gen-hashtags", "depends_on": ["get-topic"] },
    { "id": "compose-post", "depends_on": ["write-caption", "gen-image", "gen-hashtags"] }
  ]
}
```

---

## Concurrency

How tasks run in parallel.

### Concurrency Rules

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  CONCURRENCY MODEL                                                           │
│                                                                              │
│  1. Tasks with no shared dependencies → RUN IN PARALLEL                     │
│  2. Tasks waiting on same dependency → QUEUE, run when ready                │
│  3. Max concurrent tasks per job → Configurable (default: 5)                │
│  4. Max concurrent jobs → Configurable (default: 3)                         │
│  5. Rate limits are the REAL constraint, not concurrency                    │
│                                                                              │
│  Example: Job with 10 independent tasks, max_concurrent = 5                 │
│    → First 5 tasks start immediately                                        │
│    → As each completes, next task starts                                    │
│    → But if all 5 need same API provider, rate limit kicks in               │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Configuration

```json
// config/systems/orchestration.json
{
  "concurrency": {
    "maxConcurrentJobs": 3,
    "maxConcurrentTasksPerJob": 5,
    "maxConcurrentTasksGlobal": 15,
    "maxWorkersPerSystem": 3
  }
}
```

### Concurrency vs Rate Limits

```
Concurrency limits: How many things running at once
Rate limits: How fast you can call external APIs

You might have 5 concurrent tasks, but if they all need OpenAI:
  - Concurrency allows all 5 to run
  - Rate limit (60 RPM) means they queue at API level
  - Validator tracks this, Manager routes accordingly

The smarter approach: Manager checks rate limits BEFORE assigning
  → If OpenAI at limit, route to Claude instead
  → Or queue task until capacity available
```

---

## Event Types

Events emitted by Event Manager for system coordination.

### Core Events

| Event | When Emitted | Typical Subscribers |
|-------|--------------|---------------------|
| `job.created` | New job submitted | Log Manager, Analytics |
| `job.started` | Job begins execution | Log Manager |
| `job.completed` | Job finished successfully | Notification, Analytics, Cost |
| `job.failed` | Job failed | Notification, Log Manager |
| `job.paused` | Job waiting for resources | Priority Queue, Notification |
| `job.resumed` | Paused job continuing | Log Manager |

### Task Events

| Event | When Emitted | Typical Subscribers |
|-------|--------------|---------------------|
| `task.started` | Task begins | Log Manager |
| `task.completed` | Task finished | Job Manager, Analytics |
| `task.failed` | Task failed after retries | Failure Handler, Log Manager |
| `task.retrying` | Task retry attempt | Log Manager |

### Batch Events

| Event | When Emitted | Typical Subscribers |
|-------|--------------|---------------------|
| `batch.created` | Batch grouped | Log Manager |
| `batch.started` | Batch execution begins | Log Manager |
| `batch.paused` | Batch hit resource limit | Priority Queue |
| `batch.resumed` | Batch continuing | Log Manager |
| `batch.completed` | All batch tasks done | Job Manager, Cost |

### Resource Events

| Event | When Emitted | Typical Subscribers |
|-------|--------------|---------------------|
| `budget.threshold` | Daily budget at 80% | Notification |
| `budget.exhausted` | Daily budget depleted | Job Manager, Notification |
| `rate_limit.approaching` | Provider at 90% RPM | AI Manager |
| `rate_limit.reset` | Minute/day counter reset | Priority Queue |
| `tokens.low` | Model tokens at 80% | AI Manager |

### System Events

| Event | When Emitted | Typical Subscribers |
|-------|--------------|---------------------|
| `system.startup` | System started | All Managers |
| `system.shutdown` | Graceful shutdown initiated | All Managers |
| `provider.unhealthy` | Circuit breaker tripped | AI Manager, Log Manager |
| `provider.recovered` | Circuit breaker closed | AI Manager, Log Manager |
| `approval.required` | Cost needs human approval | Notification |
| `approval.granted` | Human approved cost | Budget Validator |

### Event Payload Structure

```typescript
interface SystemEvent {
  type: string;           // "job.completed", "budget.threshold", etc.
  timestamp: string;      // ISO 8601
  source: string;         // Which component emitted
  data: {
    jobId?: string;
    taskId?: string;
    batchId?: string;
    [key: string]: any;   // Event-specific data
  };
}
```

---

## Config vs Database

Why things are stored where they are.

### Decision Matrix

| Question | Answer | Store In |
|----------|--------|----------|
| Does it change per job? | Yes | SQLite |
| Does it change per run? | Yes | SQLite |
| Same for all jobs of this type? | Yes | JSON Config |
| Needs querying/aggregation? | Yes | SQLite |
| Set once, rarely changes? | Yes | JSON Config |
| Is it a secret? | Yes | Environment (.env) |

### Examples

| Data | Storage | Reason |
|------|---------|--------|
| API endpoint URLs | JSON Config | Same for all jobs, rarely changes |
| API keys | Environment | Secret, never in repo |
| Job progress | SQLite | Changes per run, needs querying |
| Task outputs | SQLite | Changes per job, may need later |
| Model list | JSON Config | Same for all, occasionally updated |
| Cost per job | SQLite | Changes per run, needs aggregation |
| Rate limit config | JSON Config | Same for all, set once |
| Rate limit current usage | SQLite | Changes constantly, needs recovery |

### The Rule

```
JSON Config = HOW the system behaves (settings, parameters)
SQLite = WHAT the system did (state, results, history)
Environment = WHO has access (secrets, keys)
```

---

## Quick Reference

### Key Rules

```
1.  Managers never call external APIs - they coordinate, not execute
2.  Workers execute, don't decide - managers assign after validation
3.  Workers are stateless - state lives in SQLite
4.  Functions have no side effects - pure transformation only
5.  Task Manager coordinates task flow - managers coordinate for resources
6.  Config is separate from code - TypeScript = WHAT, JSON = HOW
7.  All runtime state in SQLite - jobs, tasks, logs, costs
8.  All external resources via Cross-Cutting Services - no direct access
9.  Reservations for batches - reserve before starting, release on complete
10. Model consistency in batches - can switch before start, never during
```

### NOT Responsible For

| Component | NOT Responsible For |
|-----------|---------------------|
| Manager | Making API calls, writing to disk |
| Worker | Deciding which model/provider, checking budgets |
| Function | API calls, database access, any side effects |
| Task Manager | Choosing which worker, rate limit checking |
| Validator | Making decisions, executing tasks |

---

## Glossary

| Term | Definition |
|------|------------|
| **Job** | High-level goal requested by user (e.g., "Build brand identity") |
| **Task** | Single unit of work within a job (e.g., "Generate colour palette") |
| **Batch** | Group of similar tasks processed together for efficiency |
| **Worker** | Component that executes a specific type of task |
| **Manager** | Component that routes tasks to appropriate workers |
| **System** | Functional domain containing managers, workers, functions |
| **Validator** | Component tracking resource usage (rate limits, tokens, budget) |
| **Shadow Billing** | Tracking what free-tier usage WOULD cost at paid rates |
| **Reservation** | Pre-allocated resources for a batch to guarantee availability |
| **Circuit Breaker** | Pattern that stops calling a failing provider temporarily |
| **Cross-Cutting Service** | Shared service used by all systems (DB, Cache, Log, etc.) |
| **Task Chain** | Sequence of tasks with dependencies that form a job |
| **Pre-flight Check** | Validation before job starts (budget, capacity, estimates) |
| **Archiver** | Component that moves completed jobs to archive, manages data retention |

---

## Next Documents

- [02-SYSTEMS.md](02-SYSTEMS.md) - Complete list of all systems
- [03-STORAGE.md](03-STORAGE.md) - Database schema and config structure
- [04-FILE-STRUCTURE.md](04-FILE-STRUCTURE.md) - Project folder layout
- [05-API.md](05-API.md) - Interface layer (CLI, API, Svelte webapp)
- [06-BUILD-PLAN.md](06-MIGRATION-PLAN.md) - What to build, existing projects as reference
