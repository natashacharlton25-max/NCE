# System Glossary

> Universal terminology for the NCE architecture

---

## Quick Reference

| Say This | Means This |
|----------|------------|
| Module | Domain unit (canva, ai, prompts) |
| Function | Single job within a module (brick) |
| Step | Sequence of functions within ONE module |
| Chain | Sequence of steps across MULTIPLE modules |
| Parallel | Steps that run simultaneously |
| Module Config | Index of functions and steps in a module |
| System Config | Master index including all chains |
| Job | Single run of a chain |
| Job ID | Tracking number for a job |
| Trace | Log of steps in a job |
| Orchestrator | Coordinates chains |
| Repo | JSON data storage |
| Checkpoint | Safe recovery point |

---

## System Glossary

| Term | Definition | Example |
|------|------------|---------|
| **System** | The entire architecture | Everything |
| **System Config** | Master index of all modules and chains | system.config.json |
| **Module** | A contained unit housing related functions for one domain | token-manager, model-manager, ai-caller |
| **Module Config** | Index of functions and steps within a module | module.config.json |
| **Function** | Single job within a module, one purpose (brick) | estimateTokens.js, checkBudget.js |
| **Step** | Sequence of functions within ONE module | TokenManager.validateAndTrack |
| **Chain** | Sequence of steps across MULTIPLE modules | brand-kit chain in system.config.json |
| **Job** | A single run of a chain with data | job_20260117_143052_brandkit_abc123 |
| **Job ID** | Unique identifier tracking a job through the system | job_20260117_143052_brandkit_abc123 |
| **Orchestrator** | Coordinates chains, indexes all module steps | orchestrator module |
| **Repo** | JSON data storage, accessed via Librarian (search) or FileManager (direct) | model-cards, tones, brand-profiles |
| **Checkpoint** | Safe point in chain for recovery | Step marked checkpoint: true |
| **Trace** | Log of steps a job has passed through | job.trace[] |
| **Agent** | External AI that processes requests | Claude, GPT, Gemini |
| **Agent Call** | Single request to an external AI | AICaller.callWithRetry |
| **Agent Response** | Data returned from an agent call | Generated text, structured JSON |

---

## Hierarchy

```
System
  └── System Config
        │
        ├── Modules (piles of bricks)
        │     └── Module Config
        │           ├── Functions (bricks - single jobs)
        │           └── Steps (sequences of functions within module)
        │
        └── Chains (cross-module workflows)
              └── Steps (module.step references)
```

**Composition Pattern:**
```
Function (brick)     → single job, smallest unit
        ↓
Step (module-level)  → sequence of functions within ONE module
        ↓
Chain (system-level) → sequence of steps across MULTIPLE modules
```

**Example:**
```
TokenManager module:
├── functions/
│   ├── estimateTokens()    ← brick
│   ├── checkBudget()       ← brick
│   └── recordUsage()       ← brick
│
└── steps/
    └── validateAndTrack:   ← step (uses own bricks)
        1. estimateTokens()
        2. checkBudget()
        3. recordUsage()

system.config.json → chains → brand-kit:
  1. Builders.scaffoldBrand
  2. ModelManager.selectWithFallback
  3. TokenManager.validateAndTrack
  4. AICaller.callWithRetry
```

---

## Request Lifecycle Terms

| Term | Definition |
|------|------------|
| **Created** | Request received, ID assigned |
| **In Progress** | Pipeline running |
| **Pending Approval** | Complete but awaiting human review |
| **Approved** | Human approved |
| **Rejected** | Human rejected |
| **Failed** | Error occurred, couldn't complete |
| **Complete** | Successfully finished |

---

## Chain Execution Terms

| Term | Definition |
|------|------------|
| **Sequential** | Steps run one after another (default) |
| **Parallel** | Multiple steps run simultaneously (fan-out) |
| **Fan-out** | Split into multiple parallel paths |
| **Fan-in** | Merge parallel paths back together |
| **Wait All** | Continue only when ALL parallel steps complete |
| **Wait Any** | Continue when FIRST parallel step completes |
| **Wait AllSettled** | Wait for all, even if some fail |
| **Context Merge** | Combine outputs from parallel steps into shared context |

---

## Handler Types

| Handler | Job |
|---------|-----|
| **Request Handler** | Orchestrator - routes requests through pipelines |
| **Failure Handler** | Manages errors, retries, recovery |
| **Success Handler** | Manages completion, approvals |
| **Status Handler** | Tracks real-time progress for UI |
| **Queue Handler** | Manages request priority and throttling |
| **Cache Handler** | Stores repeated data for speed |
| **Notification Handler** | Sends alerts to humans |

---

## Data Terms

| Term | Definition |
|------|------------|
| **Repo** | JSON data storage (model-cards, tones, brand-profiles, etc.) |
| **Librarian** | Searches/recommends from repos (discovery) |
| **FileManager** | Direct lookup from repos (exact ID) |
| **File** | Unstructured data (images, PDFs, uploads) |
| **Asset** | Reusable file (logo, font, template) |
| **Output** | Generated deliverable |
| **Temp** | Temporary file, auto-cleaned |

**Repo access pattern:**
```
Same data, two access methods:
• Librarian.search()    → "Find me a text model"
• FileManager.get()     → "Get gpt-4-turbo"
```

---

## Config File Types

| File | Purpose |
|------|---------|
| **system.config.json** | Master index: modules + chains |
| **module.config.json** | Functions + steps in a module |
| **.rules.json** | Rules for a handler (retry, approval, etc.) |
| **.schema.json** | Validation structure |

---
