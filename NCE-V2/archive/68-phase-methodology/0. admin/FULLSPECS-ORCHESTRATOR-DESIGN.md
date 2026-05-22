# Fullspecs Orchestrator - Design Document

---
**Status:** DRAFT
**Version:** 0.1.0
**Purpose:** Automate the 68-phase Fullspecs workflow with checkpoint approvals
---

## 1. What It Does

The Fullspecs Orchestrator is a tool that:

1. **Runs phases automatically** - Executes phases in sequence without manual prompting
2. **Stops at checkpoints** - Pauses for human review at section boundaries
3. **Tracks progress** - Knows where you are, lets you resume anytime
4. **Manages artifacts** - Creates files in the right places, updates progress docs
5. **Handles context** - Feeds Claude the right inputs for each phase

---

## 2. User Experience

### Starting a New Project

```
fullspecs init "My App"

→ Creates project folder structure
→ Copies templates
→ Initializes progress tracker
→ Ready to run
```

### Running a Section

```
fullspecs run 0a

→ "Starting Section 0a: Concept to PassPhases (Phases 1-12)"
→ "Phase 1: Project Overview - waiting for your project description..."
→ User provides input
→ Claude generates PROJECT-OVERVIEW.md
→ "Phase 1 complete. Continue? (y/n)"
→ ... repeats through Phase 12 ...
→ "Section 0a complete. Review outputs in /admin/"
→ "Approve section 0a? (y/n/review)"
→ User approves
→ Creates 0a-to-0b-HANDOFF.md automatically
```

### Checking Status

```
fullspecs status

PROJECT: My App
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Section    Phases    Status        Progress
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
0a         1-12      ✓ COMPLETE    12/12
0b         13-19     ✓ COMPLETE    7/7
0c         20-28     ▶ IN PROGRESS 5/9
0d         29-36     ○ NOT STARTED 0/8
0e         37-42     ○ NOT STARTED 0/6
0f         43-53     ○ NOT STARTED 0/11
0g         54-60     ○ NOT STARTED 0/7
0h         61-64     ○ NOT STARTED 0/4
0i         65-68     ○ NOT STARTED 0/4
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Current: Phase 25 (Post-Spec Analysis)
Last activity: 2 hours ago
```

### Resuming Work

```
fullspecs resume

→ "Resuming from Phase 25: Post-Spec Analysis"
→ "Context loaded from previous session"
→ Continues where you left off
```

### Running Single Phase (Manual Override)

```
fullspecs phase 23

→ Runs just Phase 23
→ Useful for re-running a specific phase
```

---

## 3. Checkpoints

### Section Boundaries (9 Checkpoints)

| Checkpoint | After Phase | What's Reviewed |
|------------|-------------|-----------------|
| CP-1 | 12 | Project concept, systems, integrations |
| CP-2 | 19 | Coverage passes, dependencies, implementation plan |
| CP-3 | 28 | All component specs |
| CP-4 | 36 | Database, API, repo structure, tech stack |
| CP-5 | 42 | Frontend contract, design system, pages, components |
| CP-6 | 53 | Working code (backend + frontend) |
| CP-7 | 60 | All tests passing |
| CP-8 | 64 | CI/CD, security, performance, docs |
| CP-9 | 68 | Production deployed and stable |

### What Happens at a Checkpoint

1. **Summary generated** - Key outputs listed
2. **Handoff created** - Section-to-section handoff document
3. **Human review** - User can browse outputs
4. **Approval required** - Must approve to continue
5. **Audit option** - Can trigger external audit before proceeding

---

## 4. How It Works (Architecture)

### Components

```
┌─────────────────────────────────────────────────────┐
│                  Fullspecs CLI                       │
│  (fullspecs init, run, status, resume, phase)       │
└─────────────────────┬───────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────┐
│              Orchestrator Engine                     │
│  - Phase sequencer                                   │
│  - Context builder                                   │
│  - Checkpoint handler                                │
│  - Progress tracker                                  │
└─────────────────────┬───────────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
┌───────────┐  ┌───────────┐  ┌───────────┐
│  Claude   │  │   File    │  │  State    │
│    API    │  │  System   │  │   Store   │
└───────────┘  └───────────┘  └───────────┘
```

### State Management

State stored in `{{project}}/.fullspecs/state.json`:

```json
{
  "project_name": "My App",
  "created_at": "2025-01-15T10:00:00Z",
  "current_section": "0c",
  "current_phase": 25,
  "phase_status": {
    "1": { "status": "complete", "completed_at": "...", "output": "PROJECT-OVERVIEW.md" },
    "2": { "status": "complete", "completed_at": "...", "output": "PROJECT-INTENTION.md" },
    "25": { "status": "in_progress", "started_at": "..." }
  },
  "section_status": {
    "0a": { "status": "approved", "approved_at": "...", "handoff": "0a-to-0b-HANDOFF.md" },
    "0b": { "status": "approved", "approved_at": "...", "handoff": "0b-to-0c-HANDOFF.md" },
    "0c": { "status": "in_progress" }
  },
  "decisions": [],
  "context_cache": {}
}
```

---

## 5. Phase Execution Logic

### For Each Phase

```
1. LOAD phase definition
   - Read PHASE-XX-PROMPT.md (or section prompt file)
   - Identify required inputs
   - Identify expected outputs
   - Identify template to use

2. BUILD context
   - Load outputs from previous phases (as specified in prompt)
   - Load relevant PASS-NOTES.md if exists
   - Load any decision notes
   - Trim context to fit token limits

3. CHECK for human input needed
   - Some phases need user input (mockups, decisions)
   - Prompt user if required
   - Store input in context

4. EXECUTE via Claude
   - Send system prompt (Claude-*.md for section)
   - Send phase prompt
   - Send context
   - Stream response

5. SAVE outputs
   - Parse response for file outputs
   - Save to correct locations per phase spec
   - Update state.json

6. VERIFY
   - Check expected outputs exist
   - Run any automated checks
   - Log completion

7. CONTINUE or CHECKPOINT
   - If section boundary → run checkpoint flow
   - Else → proceed to next phase
```

### Handling Conditional Phases

Some phases are conditional:
- Phases 6-8 (Subsystems) - only if systems have subsystems
- Phases 9-11 (External Integrations) - only if integrations exist

Logic:
```
if phase.condition:
    evaluate condition against project state
    if not met:
        mark phase as "skipped"
        log reason
        continue to next phase
```

---

## 6. Context Management

### The Problem

Claude has limited context. A full project might have:
- 20+ system/subsystem specs
- 50+ component specs
- Hundreds of pages of accumulated docs

Can't fit it all in one prompt.

### The Solution: Scoped Context

Each phase gets **only what it needs**:

| Phase Type | Context Loaded |
|------------|----------------|
| System spec | PASS-NOTES.md for that system only |
| Component spec | Parent system spec + PASS-NOTES.md |
| Implementation | Relevant specs + TECH-STACK.md |
| Testing | Relevant specs + implementation files |

### Context Builder Rules

```
1. Always include: Claude-*.md (section guide)
2. Always include: Phase prompt
3. Include: Outputs specified as "Inputs" in phase prompt
4. Include: Relevant PASS-NOTES.md or DECISION-NOTES.md
5. Exclude: Unrelated components
6. Truncate: If still too large, summarize older context
```

---

## 7. Error Handling

### Phase Fails

```
Phase 23 failed: Claude response didn't include expected output

Options:
1. Retry phase (same inputs)
2. Retry with user guidance
3. Skip phase (mark incomplete)
4. Abort section

Choice:
```

### Claude API Error

```
API error: Rate limited

Waiting 60 seconds...
Retrying (attempt 2 of 3)...
```

### User Wants to Go Back

```
fullspecs rollback 21

→ "Rolling back to Phase 21"
→ "Phases 22-25 will be marked incomplete"
→ "Confirm? (y/n)"
→ Resets state, user can re-run
```

---

## 8. File Structure

### Template Repository (SYSBuildTemplates/)

```
SYSBuildTemplates/
├── 0. admin/
│   ├── 0a. Concept-to-PassPhases/
│   │   ├── Claude-prebuild.md          ← Section guide
│   │   └── MASTER-BUILD-GUIDE.md
│   ├── 0b. PassPhases 0-4/
│   │   └── Claude-passphases.md
│   └── ... (other sections)
├── 1. Project Overview/
│   ├── PROJECT-OVERVIEW-PROMPT.md      ← Phase prompt
│   └── PROJECT-OVERVIEW-TEMPLATE.md    ← Output template
├── 2. Project Intention/
│   └── ...
└── ... (phases 3-68)
```

### Project Created by Orchestrator

```
my-app/
├── .fullspecs/
│   ├── state.json                      ← Progress tracking
│   ├── context-cache/                  ← Cached context for resume
│   └── logs/                           ← Execution logs
├── admin/
│   ├── PROJECT-OVERVIEW.md             ← Phase 1 output
│   ├── PROJECT-INTENTION.md            ← Phase 2 output
│   ├── 0a-PROGRESS.md                  ← Section progress
│   ├── 0a-DECISION-NOTES.md            ← Section decisions
│   ├── 0a-to-0b-HANDOFF.md             ← Checkpoint handoff
│   └── ...
├── {{system}}/
│   ├── SYSTEM-NOTES.md
│   ├── PASS-NOTES.md
│   └── spec/
│       ├── INDEX.md
│       └── ...
└── ...
```

---

## 9. Commands Reference

| Command | Purpose |
|---------|---------|
| `fullspecs init <name>` | Create new project |
| `fullspecs run <section>` | Run entire section (e.g., `0a`, `0b`) |
| `fullspecs run all` | Run all sections with checkpoints |
| `fullspecs phase <n>` | Run single phase |
| `fullspecs status` | Show current progress |
| `fullspecs resume` | Continue from last position |
| `fullspecs rollback <n>` | Go back to phase n |
| `fullspecs audit <section>` | Trigger external audit for section |
| `fullspecs export` | Export project for handoff |

---

## 10. Implementation Phases

### Phase 1: Foundation (MVP)

- [ ] CLI scaffolding (init, status)
- [ ] State management (state.json read/write)
- [ ] Phase definitions loader
- [ ] Basic phase execution (single phase)

### Phase 2: Section Execution

- [ ] Section runner (all phases in section)
- [ ] Checkpoint handling
- [ ] Handoff generation
- [ ] Context builder (basic)

### Phase 3: Full Workflow

- [ ] Full run mode (all sections)
- [ ] Resume capability
- [ ] Rollback capability
- [ ] Conditional phase handling

### Phase 4: Polish

- [ ] Better error handling
- [ ] Progress visualization
- [ ] Context optimization
- [ ] Audit integration

---

## 11. Open Questions

1. **Claude API vs Claude Code?**
   - Direct API calls give more control
   - Claude Code integration would leverage existing tooling
   - Recommendation: Start with API, add Claude Code later

2. **Local vs Cloud state?**
   - Local (file-based) is simpler, works offline
   - Cloud would enable team collaboration
   - Recommendation: Local first, cloud later

3. **How to handle large projects?**
   - Projects with 10+ systems could have huge context
   - May need smarter summarization
   - Recommendation: Build, then optimize

4. **Approval UX?**
   - CLI-only? Web UI? VS Code extension?
   - Recommendation: CLI first, VS Code extension would be nice

---

## 12. Success Criteria

The orchestrator is successful if:

1. **Time saved**: Running a section takes <50% of manual time
2. **No lost progress**: Can always resume exactly where left off
3. **Correct outputs**: Files created in right places with right content
4. **Human in control**: Never proceeds past checkpoint without approval
5. **Debuggable**: Clear logs showing what happened and why

---

## Next Steps

1. Review this design
2. Identify any missing requirements
3. Decide on tech stack (Node.js recommended)
4. Build Phase 1 (MVP)

---

*Design document created January 2025*
