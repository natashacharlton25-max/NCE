# PolicyEngine Module

> **Purpose:** "Can this run?" gate - check permissions, costs, safety before execution
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

PolicyEngine is the **gatekeeper** that runs FIRST before any chain executes. It answers one question: "Can this run?" by checking permissions, cost limits, safety rules, and data availability.

```
Request → PolicyEngine.canRun() → Approved/Denied → Orchestrator proceeds or blocks
```

**PolicyEngine runs BEFORE:**
- Any chain starts
- Any AI call is made
- Any cost is incurred
- Any resource is consumed

**No bypass allowed.** Every job must pass PolicyEngine.

---

## Roles & Rules

### PolicyEngine DOES:
- Check if user/brand has permission to run chain
- Check if cost budget allows the operation
- Check if required data exists (via Trackers)
- Check safety rules and content policies
- Check rate limits haven't been exceeded
- Return clear approval/denial with reasons
- Log all policy decisions for audit

### PolicyEngine does NOT:
- Execute chains (Orchestrator does that)
- Track costs (CostBudgetManager does that)
- Track completion status (Trackers do that)
- Enforce rate limits (RateLimiter does that)
- Make business decisions (just enforces rules)

### Boundaries:
- Read-only access to all data sources
- Cannot modify any data
- Cannot skip checks
- Must return decision within timeout
- All denials must include reason

---

## Policy Categories

### 1. Permission Policies
Who can do what?

| Policy | Description | Example |
|--------|-------------|---------|
| `brand.access` | Can user access this brand? | User owns brand "acme" |
| `chain.access` | Can user run this chain? | User has "content-generation" access |
| `module.access` | Can chain use this module? | Chain can use AICaller |
| `feature.access` | Is feature enabled? | "batch-processing" is enabled |

### 2. Cost Policies
Is there budget?

| Policy | Description | Example |
|--------|-------------|---------|
| `cost.daily` | Daily cost limit | Max $50/day |
| `cost.monthly` | Monthly cost limit | Max $500/month |
| `cost.per_job` | Per-job cost limit | Max $5/job |
| `cost.per_brand` | Per-brand cost limit | Brand "acme" max $100/month |

### 3. Data Policies
Is required data available?

| Policy | Description | Example |
|--------|-------------|---------|
| `brand.complete` | Required brand sections exist | Core, typography, visuals complete |
| `data.exists` | Required data files exist | Template exists |
| `data.valid` | Data passes validation | Brand data is valid JSON |

### 4. Safety Policies
Is content allowed?

| Policy | Description | Example |
|--------|-------------|---------|
| `content.safe` | Content passes safety check | No prohibited content |
| `content.compliant` | Meets compliance rules | GDPR compliant |
| `rate.limit` | Within rate limits | Not exceeding API limits |

### 5. System Policies
Is system ready?

| Policy | Description | Example |
|--------|-------------|---------|
| `system.healthy` | Required services available | AI APIs responding |
| `system.capacity` | Queue not overloaded | Less than 100 pending jobs |
| `maintenance.window` | Not in maintenance | Outside 2am-4am window |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `canRun(request)` | Main gate - check all policies | request | decision |
| `checkPermissions(userId, resource)` | Check permission policies | userId, resource | allowed |
| `checkCost(jobEstimate, brandId)` | Check cost policies | estimate, brandId | allowed |
| `checkData(requirements)` | Check data availability | requirements | available |
| `checkSafety(content)` | Check safety policies | content | safe |
| `checkSystem()` | Check system policies | - | ready |
| `getPolicy(policyId)` | Get policy definition | policyId | policy |
| `explainDenial(decision)` | Human-readable denial reason | decision | explanation |

---

## Detailed Function Specs

### canRun(request)

Main entry point - checks all applicable policies.

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc",
  chainName: "generate-blog-post",
  brandId: "acme",
  userId: "user_123",
  params: {
    templateId: "blog-post-standard",
    topic: "AI in marketing"
  },
  estimatedCost: 0.15,
  requiredData: ["brand.core", "brand.voice", "template.blog-post"]
}

// Process
1. Check permission policies (user, brand, chain access)
2. Check cost policies (budget available)
3. Check data policies (required data exists)
4. Check safety policies (content allowed)
5. Check system policies (system ready)
6. Return consolidated decision

// Output - Approved
{
  approved: true,
  jobId: "job_20260117_143052_content_abc",
  checks: {
    permissions: { passed: true },
    cost: { passed: true, remaining: 45.50 },
    data: { passed: true },
    safety: { passed: true },
    system: { passed: true }
  },
  timestamp: "2026-01-17T14:30:52Z"
}

// Output - Denied
{
  approved: false,
  jobId: "job_20260117_143052_content_abc",
  reason: "cost_limit_exceeded",
  details: "Daily cost limit ($50) would be exceeded. Current: $49.90, Estimated: $0.15",
  checks: {
    permissions: { passed: true },
    cost: { passed: false, reason: "daily_limit", current: 49.90, limit: 50.00 },
    data: { passed: true },
    safety: { passed: true },
    system: { passed: true }
  },
  suggestion: "Wait until tomorrow or increase daily limit",
  timestamp: "2026-01-17T14:30:52Z"
}
```

### checkPermissions(userId, resource)

Check if user has permission to access resource.

```javascript
// Input
{
  userId: "user_123",
  resource: {
    type: "chain",
    id: "generate-blog-post",
    brandId: "acme"
  }
}

// Output
{
  allowed: true,
  permissions: ["brand.acme.owner", "chain.generate-blog-post.execute"],
  checked: ["brand.access", "chain.access"]
}

// Or denied
{
  allowed: false,
  reason: "no_brand_access",
  required: "brand.acme.access",
  userHas: ["brand.globex.owner"]
}
```

### checkCost(jobEstimate, brandId)

Check if cost budget allows the operation.

```javascript
// Input
{
  jobEstimate: 0.15,
  brandId: "acme"
}

// Output
{
  allowed: true,
  budgets: {
    daily: { used: 12.50, limit: 50.00, remaining: 37.50 },
    monthly: { used: 150.00, limit: 500.00, remaining: 350.00 },
    brand: { used: 45.00, limit: 100.00, remaining: 55.00 }
  },
  estimatedAfter: {
    daily: 12.65,
    monthly: 150.15,
    brand: 45.15
  }
}
```

### checkData(requirements)

Check if required data exists via Trackers.

```javascript
// Input
{
  requirements: [
    { type: "brand", id: "acme", sections: ["core", "voice"] },
    { type: "template", id: "blog-post-standard" }
  ]
}

// Output
{
  available: true,
  items: [
    { type: "brand", id: "acme", sections: { core: "complete", voice: "complete" } },
    { type: "template", id: "blog-post-standard", exists: true }
  ]
}

// Or unavailable
{
  available: false,
  missing: [
    { type: "brand", id: "acme", sections: { typography: "not_started" } }
  ],
  reason: "Brand 'acme' missing required section: typography"
}
```

### checkSafety(content)

Check content against safety policies.

```javascript
// Input
{
  content: {
    topic: "AI in marketing",
    targetAudience: "business professionals"
  },
  chainName: "generate-blog-post"
}

// Output
{
  safe: true,
  checks: ["prohibited_content", "compliance"],
  flags: []
}

// Or unsafe
{
  safe: false,
  reason: "prohibited_content",
  flags: ["medical_claims"],
  suggestion: "Remove medical claims or use medical-content chain with review"
}
```

---

## Policy Configuration

Policies are defined in system config:

```json
{
  "policies": {
    "cost": {
      "daily_limit": 50.00,
      "monthly_limit": 500.00,
      "per_job_limit": 5.00,
      "per_brand": {
        "default": 100.00,
        "premium": 500.00
      }
    },

    "permissions": {
      "default_chains": ["generate-content", "generate-email"],
      "premium_chains": ["batch-processing", "advanced-analytics"]
    },

    "data_requirements": {
      "generate-content": ["brand.core", "brand.voice"],
      "generate-social": ["brand.core", "brand.voice", "brand.visuals"],
      "export-brand-kit": ["brand.core", "brand.voice", "brand.typography", "brand.visuals"]
    },

    "safety": {
      "prohibited_topics": ["medical_advice", "financial_advice", "legal_advice"],
      "require_review": ["political", "controversial"],
      "content_filters": ["profanity", "violence", "adult"]
    },

    "system": {
      "max_queue_size": 100,
      "maintenance_windows": ["02:00-04:00"],
      "required_services": ["ai_api", "database"]
    }
  }
}
```

---

## Integration with Trackers

PolicyEngine queries Trackers for data availability:

```javascript
// PolicyEngine asks BrandTracker
const brandStatus = await BrandTracker.canProceed("acme", ["core", "voice", "typography"]);

if (!brandStatus.canProceed) {
  return {
    approved: false,
    reason: "incomplete_brand",
    details: `Missing: ${brandStatus.missing.join(", ")}`,
    suggestion: "Complete brand profile before generating content"
  };
}
```

---

## Integration with Cost Tracking

PolicyEngine queries CostBudgetManager for budget status:

```javascript
// PolicyEngine asks CostBudgetManager
const budgetStatus = await CostBudgetManager.checkBudget({
  brandId: "acme",
  estimatedCost: 0.15
});

if (!budgetStatus.allowed) {
  return {
    approved: false,
    reason: "cost_limit_exceeded",
    details: budgetStatus.reason,
    budgets: budgetStatus.budgets
  };
}
```

---

## Example Flow

```
1. User requests: "Generate blog post for Acme"
   ↓
2. Orchestrator creates job, calls PolicyEngine.canRun()
   ↓
3. PolicyEngine checks:
   ├── Permissions: User owns Acme? ✓
   ├── Permissions: Chain allowed? ✓
   ├── Cost: Budget available? ✓ ($37.50 remaining)
   ├── Data: Brand complete? ✗ (missing typography)
   ├── Safety: Content safe? (skipped - data check failed)
   └── System: Ready? (skipped - data check failed)
   ↓
4. PolicyEngine returns:
   {
     approved: false,
     reason: "incomplete_brand",
     details: "Brand 'acme' missing: typography",
     suggestion: "Complete typography section first"
   }
   ↓
5. Orchestrator blocks job, returns error to user
   ↓
6. User sees: "Cannot generate content - please complete typography section"
```

---

## Denial Handling

When PolicyEngine denies a request:

```javascript
// FailureHandler receives denial
{
  type: "policy_denial",
  reason: "cost_limit_exceeded",
  level: 4,  // Human intervention required
  action: "notify_user",
  message: "Daily cost limit reached. Wait until tomorrow or adjust limits."
}
```

Policy denials are typically **Level 4** (human intervention) because:
- They represent business rules, not technical failures
- User needs to take action (add budget, complete data, etc.)
- Auto-retry won't help

---

## Audit Logging

All policy decisions are logged:

```json
{
  "timestamp": "2026-01-17T14:30:52Z",
  "jobId": "job_20260117_143052_content_abc",
  "decision": "denied",
  "reason": "incomplete_brand",
  "checks": {
    "permissions": "passed",
    "cost": "passed",
    "data": "failed",
    "safety": "skipped",
    "system": "skipped"
  },
  "userId": "user_123",
  "brandId": "acme",
  "chainName": "generate-blog-post"
}
```

---

## Config

### module.config.json

```json
{
  "module": "PolicyEngine",
  "version": "1.0.0",
  "description": "Can this run? gate - permissions, costs, safety",
  "type": "system-service",

  "functions": [
    {
      "name": "canRun",
      "description": "Main gate - check all policies",
      "input": ["request"],
      "output": "decision"
    },
    {
      "name": "checkPermissions",
      "description": "Check permission policies",
      "input": ["userId", "resource"],
      "output": "allowed"
    },
    {
      "name": "checkCost",
      "description": "Check cost policies",
      "input": ["jobEstimate", "brandId"],
      "output": "allowed"
    },
    {
      "name": "checkData",
      "description": "Check data availability",
      "input": ["requirements"],
      "output": "available"
    },
    {
      "name": "checkSafety",
      "description": "Check safety policies",
      "input": ["content"],
      "output": "safe"
    },
    {
      "name": "checkSystem",
      "description": "Check system policies",
      "input": [],
      "output": "ready"
    },
    {
      "name": "getPolicy",
      "description": "Get policy definition",
      "input": ["policyId"],
      "output": "policy"
    },
    {
      "name": "explainDenial",
      "description": "Human-readable denial reason",
      "input": ["decision"],
      "output": "explanation"
    }
  ],

  "config": {
    "timeout": 5000,
    "cachePolicy": true,
    "cacheTTL": 60000,
    "failClosed": true,
    "logAllDecisions": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| BrandTracker | Checking brand completion status |
| CostBudgetManager | Checking budget availability |
| RateLimiter | Checking rate limit status |
| FileManager | Reading policy configs |
| Logger | Audit logging decisions |

---

## Used By

| Component | How |
|-----------|-----|
| Orchestrator | First check before any chain runs |
| FailureHandler | Understanding policy denials |

---

*Last updated: 2026-01-17*
