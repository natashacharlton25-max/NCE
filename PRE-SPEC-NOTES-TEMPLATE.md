# [Module Name] Module

> **Purpose:** [One sentence: "Generate [WHAT] using [HOW]"]
> **Type:** [Category: AI Services / Visual Design / Data Management / etc.]
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

[2-3 sentences describing what this module does at a high level]

---

## 1. Core Identity

- **Module name:** [Clear name matching ROLE-MATRIX]
- **Purpose statement:** [One sentence describing the core function]
- **Type:** [Category from MODULE-SPEC-CHECKLIST]
- **Status:** Placeholder

---

## 2. Scope & Boundaries

### What It DOES
- [Primary responsibility 1]
- [Primary responsibility 2]
- [Primary responsibility 3]
- [Primary responsibility 4 - optional]
- [Primary responsibility 5 - optional]

### What It DOES NOT Do
- [Adjacent module X handles...]
- [Adjacent module Y handles...]
- [Adjacent module Z handles...]
- [Explicitly NOT responsible for...]
- [Explicitly NOT responsible for...]

---

## 3. Data Flow

**Input sources:**
- [Source 1 - module or config]
- [Source 2 - module or config]
- [Source 3 - database or repo]

**Processing steps:**
1. [Step 1]
2. [Step 2]
3. [Step 3]
4. [Optional step 4]

**Output destinations:**
- [Destination 1 - module or storage]
- [Destination 2 - module or storage]

**Visual flow (optional ASCII diagram):**
```
[Input] → [Processing] → [Output]
```

---

## 4. Dependencies & Relationships

### Depends On (Modules This Calls)

| Module | Why | What It Provides |
|--------|-----|------------------|
| [Module A] | [Reason] | [What it provides] |
| [Module B] | [Reason] | [What it provides] |
| [Module C] | [Reason] | [What it provides] |

### Feeds Into (Modules That Call This)

| Module | How They Use It | Relationship |
|--------|-----------------|--------------|
| [Downstream A] | [Usage] | Direct consumer |
| [Downstream B] | [Usage] | Indirect consumer |
| [Downstream C] | [Usage] | Optional consumer |

### Data Dependencies

- **Repos (git-based):** [List: /repos/brands/, /repos/prompts/, etc.]
- **Databases:** [List: which .db files, own or shared]
- **Config files:** [List any configuration sources]
- **External APIs:** [List providers: OpenAI, Claude, Google, etc.]

---

## 5. Concern Ownership Cross-Check

For each concern that touches this module, document who owns it:

| Concern | Owner Module | How This Module Uses It | Notes |
|---------|--------------|------------------------|-------|
| **Validation** | Validator | Input/output validation | Reference Validator spec |
| **Retry logic** | FailureHandler | Emit error codes | Reference error codes |
| **Logging** | Logger | Call Logger API | Reference Logger spec |
| **Error codes** | ERROR-CODES.md | List codes emitted | Add new codes if needed |
| **[Concern 5]** | [Owner] | [How used] | [Notes] |

---

## 6. Error Handling

### Error Codes to Emit

- [ ] `[MODULE]_[ERROR_TYPE]` - [Description]
- [ ] `[MODULE]_[ERROR_TYPE]` - [Description]
- [ ] `[MODULE]_[ERROR_TYPE]` - [Description]
- [ ] `[MODULE]_[ERROR_TYPE]` - [Description]

**Example format:**
- `API_RATE_LIMIT_EXCEEDED` - when provider throttles
- `VALIDATION_INPUT_INVALID` - when input fails schema check

### Transient vs Permanent

| Error Code | Type | Retry? | Notes |
|------------|------|--------|-------|
| [Code A] | Transient | Yes | Exponential backoff |
| [Code B] | Permanent | No | Fail immediately |
| [Code C] | Transient | Yes | Check retry limit |

### Fallback Strategy

- **On transient failure:** [Retry, fallback provider, queue for later, etc.]
- **On permanent failure:** [Fail loudly, skip, use default, etc.]
- **Partial failures:** [How to handle partial success]

---

## 7. Concern Ownership Clarifications

### Key Ambiguities Resolved

**Q: [Ambiguity 1]**  
A: [Answer with module ownership]

**Q: [Ambiguity 2]**  
A: [Answer with module ownership]

**Q: [Ambiguity 3]**  
A: [Answer with module ownership]

**Q: [Ambiguity 4]**  
A: [Answer with module ownership]

**Q: [Ambiguity 5]**  
A: [Answer with module ownership]

---

## 8. Architecture Considerations

- **Performance patterns:** [Async / Parallel / Batching / Streaming / Polling / etc.]
- **Concurrency limits:** [Per-provider? Per-user? Global? Describe strategy]
- **Storage strategy:** [Local / DB / External (S3, Cloudflare)? / Describe strategy]
- **Fallback chains:** [Primary fails → fallback 1 → fallback 2 → describe chain]
- **Long-running operations:** [How timeouts handled? Job queues? Webhooks? Polling?]
- **State management:** [Stateless / Session state / Persistent state? Describe]

---

## 9. Data Schemas (Draft)

**Note:** Document the *shape* of data only. Zod validation goes in `/src/schemas/` after spec approval.

### Input Schema

```javascript
{
  // Required fields
  requiredField1: string,
  requiredField2: number,
  
  // Optional fields
  optionalField1?: string,
  optionalField2?: boolean,
  
  // Nested structures
  options?: {
    option1: string,
    option2: number
  }
}
```

### Output Schema

```javascript
{
  // Core outputs
  result: [type],
  metadata?: {
    timestamp: string,
    provider: string,
    [other metadata]
  },
  
  // Optional fields
  additionalData?: [type]
}
```

### Database Tables (if applicable)

| Table | Purpose | Key Fields |
|-------|---------|-----------|
| [table_name] | [Purpose] | id, [field1], [field2] |
| [table_name] | [Purpose] | id, [field1], [field2] |

### Config Structure

```javascript
{
  setting1: string,
  setting2: boolean,
  nested: {
    subsetting1: number
  }
}
```

---

## 10. Dependencies on External Systems

### External APIs

| Provider | Purpose | Rate Limits | Cost Implications |
|----------|---------|-------------|------------------|
| [API 1] | [What for] | [Limits] | [Cost per operation] |
| [API 2] | [What for] | [Limits] | [Cost per operation] |

### Auth & Credentials

- **Credentials management:** [Via APIKeyManager]
- **Storage strategy:** [Environment variables / Config files / Secure vault]
- **Rotation policy:** [How/when are keys rotated]

### Latency & Performance

- **Expected response times:** [Typical latency ranges]
- **Timeout strategy:** [Hard timeout? Graceful degradation?]
- **Affects queueing?** [Yes/No - explain]

---

## 11. Relationships to Adjacent Modules

### Similar/Related Modules

| Adjacent Module | Relationship | Notes |
|-----------------|-------------|-------|
| [Module X] | [Calls / Called by / Parallel concern] | [How they interact] |
| [Module Y] | [Calls / Called by / Parallel concern] | [How they interact] |
| [Module Z] | [Calls / Called by / Parallel concern] | [How they interact] |

**Clarification examples:**
- "AIImageCreation *generates* images; ImageBrowser *searches* existing images"
- "TemplateEngine *reads* templates; TemplateFactory *writes* them"

---

## 12. Technology-Agnostic Boundaries

**What DOES belong in pre-spec notes:**
- ✅ Module purpose and responsibilities
- ✅ Dependencies (which modules needed)
- ✅ Data flow (what moves where)
- ✅ Error codes and recovery strategy
- ✅ Boundaries (what module cannot do)
- ✅ Design rationale (why architecture chosen)

**What does NOT belong here (goes in TECH-STACK.md or implementation):**
- ❌ Library names (axios, better-sqlite3, etc.)
- ❌ Retry counts or timing (5 retries, exponential backoff starting at 100ms)
- ❌ Logging statements or levels
- ❌ Validation implementation details
- ❌ Configuration file paths
- ❌ Language or runtime specifics

---

## Before Approval Checklist

- [ ] All 12 sections completed
- [ ] No blanks or "TBD" items remaining
- [ ] Concern ownership clear for every cross-cutting concern
- [ ] Can list 3+ things this module does NOT do
- [ ] Can draw the dependency graph (A → This → B → C)
- [ ] All error codes documented with MODULE_ prefix
- [ ] No technology implementation details
- [ ] Data flow is clear enough for someone to implement without asking "what goes where?"
- [ ] No contradictions with MODULE-SPEC-CHECKLIST.md guidelines
- [ ] Adjacent modules documented with clear relationship boundaries

---

## Summary

| Item | Status | Notes |
|------|--------|-------|
| **Core Identity** | ✅ / ⚠️ / ❌ | [Notes] |
| **Scope & Boundaries** | ✅ / ⚠️ / ❌ | [Notes] |
| **Data Flow** | ✅ / ⚠️ / ❌ | [Notes] |
| **Dependencies** | ✅ / ⚠️ / ❌ | [Notes] |
| **Concern Ownership** | ✅ / ⚠️ / ❌ | [Notes] |
| **Error Handling** | ✅ / ⚠️ / ❌ | [Notes] |
| **Architecture** | ✅ / ⚠️ / ❌ | [Notes] |
| **Data Schemas** | ✅ / ⚠️ / ❌ | [Notes] |
| **External Systems** | ✅ / ⚠️ / ❌ | [Notes] |
| **Adjacent Modules** | ✅ / ⚠️ / ❌ | [Notes] |
| **Technology Boundaries** | ✅ / ⚠️ / ❌ | [Notes] |

**Ready for approval?** [Yes / No]  
**Reviewer notes:** [Any concerns or questions before spec writing]

---

*Created: [Date]*
*Version: 0.0.0*
*Status: Placeholder*
