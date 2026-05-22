# Fullspecs Methodology — Master Project Document

---
Version: v1.7.0
Last Updated: January 2025
Purpose: Complete reference for continuing this project across conversations
---

## Project Intent

### What This Is
**Fullspecs** is a complete top-to-bottom software development methodology — a systematic pipeline that guides projects from initial concept through to live deployment.

It provides:
- **Checklists** — verification gates at each stage
- **Guidance** — clear instructions for what to do and when
- **Templates** — structured formats for every document type
- **Prompts** — ready-to-use instructions for Claude Code in VS Code

### What It Covers
The full development lifecycle:
- **Concept** → Capturing and refining the initial idea
- **Architecture** → Identifying systems, subsystems, integrations
- **Specification** → Detailed specs for every component
- **Infrastructure** → Database, API, repo setup
- **Frontend** → UI/UX specs from mockups
- **Implementation** → Guided code writing
- **Testing** → Test planning and execution
- **Prerelease** → Final checks and CI/CD
- **Rollout** → Deployment and monitoring

### The Problem It Solves
1. **Context loss** — When projects exceed ~2000 lines of specs, Claude loses track of earlier decisions. Fullspecs breaks work into phases with strategic context distribution (PASS-NOTES.md per component).

2. **Inconsistency** — Without structure, different parts of a project get documented differently. Fullspecs provides templates ensuring consistency.

3. **Missing steps** — Easy to skip important work. Fullspecs provides checklists at every gate.

4. **Unclear ownership** — Who decides what? Fullspecs defines clear human/Claude responsibilities.

5. **No repeatable process** — Starting from scratch each time. Fullspecs is reusable across any project.

### Target Stack
**Generic** — The methodology is stack-agnostic. Tech stack decisions are made per-project during Phase 33 (Tech Stack) in 0d PreCode.

Examples of stacks this methodology supports:
- Svelte/Astro + Python + Cloudflare
- Next.js + TypeScript + Vercel
- React + Node + AWS
- Any modern web stack

### Core Constraints
- No single system/subsystem should exceed ~1,500 LOC
- Backend systems are speced before frontend
- Database/API emerge from specs (not pre-assigned)
- Each component gets its own PASS-NOTES.md for context

### How It's Used
1. Start a new project
2. Work through phases in order (0a → 0b → 0c → etc.)
3. Each phase has a PROMPT file — give this to Claude Code
4. Claude uses the TEMPLATE files to produce outputs
5. Human reviews and approves before moving on
6. Checklists verify nothing is missed
7. Continue until project is live

---

## Pipeline Overview

```
0a. Concept to PassPhase (Phases 1-12)    ✅ COMPLETE
0b. PassPhases 0-4 (Phases 13-19)         ✅ COMPLETE
0c. Full Specs (Phases 20-28)             ✅ COMPLETE
    └── 🔍 MANDATORY AUDIT (Phase 28) — Last chance before code
0d. PreCode (Phases 29-36)                ✅ COMPLETE
0e. Frontend (Phases 37-42)               ✅ COMPLETE
0f. Implementation (Phases 43-53)         ✅ COMPLETE
    └── 🔍 MANDATORY AUDIT (Phase 53) — Code must match specs
0g. Testing (Phases 54-60)                🔲 PLANNED
0h. Prerelease (Phases 61-64)             ✅ COMPLETE
    └── 🔍 MANDATORY AUDIT (Phase 64) — Final gate before production
0i. Rollout (Phases 65-68)                ✅ COMPLETE
```

**Audit Checkpoints:** External AI audits (ChatGPT, Gemini, etc.) review Claude's work at mandatory checkpoints. See [External Audit System](#external-audit-system) for details.

---

## Section Details

### 0h. Prerelease (Phases 61-64) ✅ COMPLETE

**Intention:** Prepare the application for production deployment. Hardens for production without changing functionality.

**Core Question:** "Is it ready for production?"

**What It Does:**
- CI/CD pipeline setup and RC creation
- Environment configuration and staging verification
- Performance and security audits
- Documentation and final sign-off

**Key Concepts:**
- **Release Candidate (RC)** — The exact tagged commit that deploys
- **RC Immutability** — Tags never move; failed RC → rc.N+1
- **Safety Pins** — AI enforcement logic for tag immutability, locked items, zombie dependencies
- **Lock Status Tracker** — Progressive locking through phases

**Phases:**

| Phase | Name | Focus | Output |
|-------|------|-------|--------|
| 61 | CI/CD Pipeline | Automation, RC creation | CI-PIPELINE.md |
| 62 | Environment Configuration | Staging, secrets, migrations | ENVIRONMENT-CONFIG.md |
| 63 | Performance & Security | Benchmarks, audits, costs | PERFORMANCE-BASELINE.md, SECURITY-AUDIT.md |
| 64 | Documentation & Final Review | Docs, checklists, sign-off | DEPLOYMENT-GUIDE.md, RUNBOOK.md, 0h-to-0i-HANDOFF.md |

**Files:** 16 files (12 admin + 4 phase prompts)

---

### 0i. Rollout (Phases 65-68) ✅ COMPLETE

**Intention:** Execute the production deployment and establish operational monitoring. Deploys exactly what 0h approved.

**Core Question:** "Is it live and healthy?"

**What It Does:**
- Pre-deploy verification and abort criteria
- Production deployment with canary/rollout strategy
- Post-deploy monitoring and verification
- Stabilization period and operational handoff

**Key Concepts:**
- **Golden Hour** — First 60 minutes post-deploy, Release Owner must be available
- **Abort Thresholds** — > 2× baseline error rate = automatic rollback
- **Canary Confidence Score** — Quantified 4-point confidence metric
- **Evidence Before Rollback** — Capture snapshots before any rollback
- **Hotfix Boundary** — No changes during stabilization; exit 0i for hotfix path
- **0i Readiness Review** — 15-minute cross-functional GO before deployment

**v1.4 Additions:**
- 0i Readiness Review (Step 0 in Phase 65)
- Abort 0i Criteria table
- Human Factors Safety Net (fatigue checks)
- Rollback Cost Classification
- Noisy Signal Classification
- Canary Confidence Score
- Explicit Authority Matrix
- Release Fatigue Guardrail

**Phases:**

| Phase | Name | Focus | Output | Collapsible |
|-------|------|-------|--------|-------------|
| 65 | Pre-Deploy Verification | Final checks, abort criteria | PRE-DEPLOY-CHECKLIST.md | Yes |
| 66 | Production Deployment | Deploy RC, canary | DEPLOYMENT-LOG.md | **NEVER** |
| 67 | Post-Deploy Verification | Monitoring, verification | LAUNCH-VERIFICATION.md | Yes |
| 68 | Stabilization & Handoff | Stabilization, ops handoff | RELEASE-COMPLETE.md | **NEVER** |

**Files:** 15 files (11 admin + 4 phase prompts)

**Admin Files:**
1. Master-Rollout-Guide 65to68.md
2. Claude-rollout.md
3. 0i-COMPLETION-CRITERIA.md
4. HOTFIX-PATH.md
5. PRE-DEPLOY-CHECKLIST-TEMPLATE.md
6. DEPLOYMENT-LOG-TEMPLATE.md
7. LAUNCH-VERIFICATION-TEMPLATE.md
8. PRODUCTION-ALERTS-TEMPLATE.md
9. RELEASE-COMPLETE-TEMPLATE.md
10. INCIDENT-REPORT-TEMPLATE.md
11. EVIDENCE-SNAPSHOT-GUIDE.md

**Phase Files:**
12. PHASE-65-PROMPT.md
13. PHASE-66-PROMPT.md
14. PHASE-67-PROMPT.md
15. PHASE-68-PROMPT.md

---

## 0h → 0i Integration

### What 0h Delivers to 0i

| Artifact | Contains |
|----------|----------|
| Signed-off RC | Exact commit to deploy |
| Working CI/CD | Automated test/build/deploy |
| Staging environment | Production-like, IaC-defined |
| Performance baseline | Metrics and targets |
| Security sign-off | Audit results |
| ALERTING-THRESHOLDS.md | Monitoring rules for 0i to implement |
| COST-THRESHOLDS.md | Cloud spend limits |
| DEPLOYMENT-GUIDE.md | How to deploy |
| RUNBOOK.md | Operational procedures |
| 0h-to-0i-HANDOFF.md | Handoff contract |

### What 0i Cannot Change

| Item | Status |
|------|--------|
| RC commit | Deploy this exact commit only |
| CI/CD pipeline | No restructuring |
| Deployment configuration | No env changes |
| Performance baselines | No lowering targets |
| Security requirements | No relaxing standards |
| Alerting thresholds | No raising thresholds |
| Cost thresholds | No raising limits |

**Rule:** Any change to a locked item requires returning to 0h, creating a new RC, and restarting from Phase 61.

---

## External Audit System

External AI audits provide independent verification of Claude's work at critical checkpoints. Another AI (ChatGPT, Gemini, etc.) reviews artifacts to catch issues Claude might have missed.

### Mandatory Audit Checkpoints

| Section | Phase | Why | Consequence of Skip |
|---------|-------|-----|---------------------|
| **0c Full Specs** | 28 | Last chance before code — errors multiply | Spec bugs become code bugs |
| **0f Implementation** | 53 | Code must match specs exactly | Undiscovered drift |
| **0h Prerelease** | 64 | Final gate before production | Production incidents |

### Recommended Audits

| Section | Phase | When to Do |
|---------|-------|------------|
| 0b PassPhases | 19 | Architecture decisions lock here |
| 0g Testing | 60 | Test quality affects confidence |

### Audit Process (6 Steps)

1. **Claude prepares handoff** — Creates 0X-AUDIT-HANDOFF.md with summary, scores, concerns, file manifest
2. **Human gathers materials** — Collects handoff, prompt, checklist, project access
3. **Auditor requests files** — External AI requests specific files to review (5-10 files)
4. **Auditor reviews** — May request follow-up files, ask clarifying questions
5. **Auditor produces report** — Scores, findings by severity, verdict (PASS/CONDITIONAL/FAIL)
6. **Human decides** — Resolves findings (Fixed/Accepted/Deferred/Disputed), closes audit

### File Locations

| File Type | Location |
|-----------|----------|
| Audit prompts | `00. external-audit/0X-section/0X-AUDIT-PROMPT.md` |
| Audit checklists | `00. external-audit/0X-section/0X-CHECKLIST.md` |
| Audit handoffs | `00. external-audit/0X-section/0X-AUDIT-HANDOFF.md` |
| Core guides | `00. external-audit/core/` |
| Completed audits | `/audits/0X-audit-report-YYYY-MM-DD.md` |

### Handling Disagreements

When Claude and the external auditor disagree:
1. Document both positions
2. Get both AIs to defend their position
3. **Human makes final decision**
4. Document resolution in audit report

See `00. external-audit/core/CROSS-AI-VALIDATION.md` for full resolution process.

---

## Human Guides

The `000. human-guides/` folder contains plain-English guides for humans operating the Fullspecs methodology. These are NOT Claude instructions — they explain the system to the human operator.

### Section Guides

| Guide | Covers |
|-------|--------|
| 0a-CONCEPT-GUIDE.md | Concept to PassPhase (Phases 1-12) |
| 0b-PASSPHASES-GUIDE.md | PassPhases 0-4 (Phases 13-19) |
| 0c-FULLSPECS-GUIDE.md | Full Specs (Phases 20-28) |
| 0d-PRECODE-GUIDE.md | PreCode (Phases 29-36) |
| 0e-FRONTEND-GUIDE.md | Frontend (Phases 37-42) |
| 0f-IMPLEMENTATION-GUIDE.md | Implementation (Phases 43-53) |
| 0g-TESTING-GUIDE.md | Testing (Phases 54-60) |
| 0h-PRERELEASE-GUIDE.md | Prerelease (Phases 61-64) |
| 0i-ROLLOUT-GUIDE.md | Rollout (Phases 65-68) |

### Master Documents

| Document | Purpose |
|----------|---------|
| MASTER-DOCUMENT-INDEX.md | Complete file inventory and navigation |
| MASTER-METHODOLOGY-OVERVIEW.md | High-level methodology explanation |
| MASTER-QUICKSTART.md | Getting started guide |

### What Human Guides Contain

- Plain-English explanation of section purpose
- When to use each phase
- What to review at each gate
- Common mistakes to avoid
- When to escalate to external audit

---

## Governance Documents

Administrative documents that control methodology execution, failure handling, and risk acceptance.

### Core Governance Files

| Document | Location | Purpose |
|----------|----------|---------|
| FAILURE-RECOVERY-RULES.md | `0. Admin/` | Rollback procedures and re-work rules |
| RISK-ACCEPTANCE-TEMPLATE.md | `0. Admin/` | Template for accepting known risks |
| CROSS-AI-VALIDATION.md | `00. external-audit/core/` | Handling AI disagreements |
| AUDIT-PROCESS.md | `00. external-audit/core/` | Step-by-step audit execution |
| AUDITOR-CONTROL-GUIDE.md | `00. external-audit/core/` | Managing difficult auditors |

### Key Governance Rules

1. **No Fix-Forward** — Problems are fixed at source, not patched in later phases
2. **Human Authority** — Humans make final decisions; AIs provide recommendations
3. **Mandatory Re-Audit** — Rolling back past an audit checkpoint requires re-audit
4. **Documented Risk** — Accepted risks must be formally documented

### When to Use Governance Docs

| Situation | Document |
|-----------|----------|
| Phase fails, need to roll back | FAILURE-RECOVERY-RULES.md |
| Accepting a known risk | RISK-ACCEPTANCE-TEMPLATE.md |
| Claude and auditor disagree | CROSS-AI-VALIDATION.md |
| Running external audit | AUDIT-PROCESS.md |

---

## Immediate Fixes Needed

### Folder Renames
- [x] `0c. Precode` → `0c. Full Specs` — DONE
- [x] `0d. Implementation` → `0d. PreCode` — DONE
- [x] `0e. Testing` → `0e. Frontend` — DONE
- [x] `0f. Prerelease` → `0f. Implementation` — DONE
- [x] `0g. Rollout` → `0g. Testing` — DONE
- [x] Create `0h. Prerelease` — DONE
- [x] Create `0i. Rollout` — DONE

### Typo Fixes
- [x] Folder 6: "Indentification" → "Identification" — DONE
- [x] Folder 8: "Subsytem" → "Subsystem" — DONE

### Naming Standardization
- [x] All phase folders: Add space after dot (e.g., `20.Pre-Spec` → `20. Pre-Spec`) — DONE

### Missing Files
- [x] Phase 10: Has prompt + template — FIXED (files were in wrong folders)
- [x] Phase 11: Has prompt + template — FIXED (files were in wrong folders)

### Cleanup
- [x] Delete `/prompts/` folder (legacy) — DONE
- [x] Delete `/0c-fullspecs/` folder (empty) — DONE

### Superseded by 0d PreCode
- [ ] Delete `29. Frontend-Component-Specs` — Templates moved to 0e
- [ ] Delete `30. Database-Schema-Specs` — Replaced by Phase 29 in 0d

---

## What To Build Next (Priority Order)

### Priority 1: 0g Testing ← NEXT
- Claude-testing.md
- Master-Testing-Guide 54to60.md
- TEST-PLAN-TEMPLATE.md
- TEST-TRACE-TEMPLATE.md
- BUG-REPORT-TEMPLATE.md
- Phase 54-60 prompts

### Priority 2: Real Project Testing
- Run one real project end-to-end
- Capture friction in FRICTION-LOG.md
- Trim/tighten based on experience

---

## File Locations

| Item | Location |
|------|----------|
| **Master Documents** | |
| This document | `PROJECT-MASTER.md` (root) |
| Claude Operating Rules | `0. Admin/CLAUDE.md` |
| **Section Admin Docs** | |
| 0a Admin docs | `0. Admin/0a. Concept-to-PassPhases/` |
| 0b Admin docs | `0. Admin/0b. PassPhases 0-4/` |
| 0c Admin docs | `0. Admin/0c. Full Specs/` |
| 0d Admin docs | `0. Admin/0d. PreCode/` |
| 0e Admin docs | `0. Admin/0e. Frontend/` |
| 0f Admin docs | `0. Admin/0f. Implementation/` |
| 0g Admin docs | `0. Admin/0g. Testing/` |
| 0h Admin docs | `0. Admin/0h. Prerelease/` |
| 0i Admin docs | `0. Admin/0i. Rollout/` |
| **Phase Folders** | |
| Phase folders | Root level (1-68) |
| **External Audit System** | |
| Audit core guides | `00. external-audit/core/` |
| 0c audit files | `00. external-audit/0c-fullspecs/` |
| 0f audit files | `00. external-audit/0f-implementation/` |
| 0h audit files | `00. external-audit/0h-prerelease/` |
| Completed audits | `/audits/` (project folder) |
| **Human Guides** | |
| Human guides | `000. human-guides/` |
| Master index | `000. human-guides/MASTER-DOCUMENT-INDEX.md` |
| **Governance** | |
| Failure recovery | `0. Admin/FAILURE-RECOVERY-RULES.md` |
| Risk acceptance | `0. Admin/RISK-ACCEPTANCE-TEMPLATE.md` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | Jan 2025 | Initial creation |
| 1.1.0 | Jan 2025 | Added 0c Full Specs admin docs, methodology rules |
| 1.2.0 | Jan 2025 | **0d PreCode complete** — 21 files (4 admin + 17 phase files) |
| 1.3.0 | Jan 2025 | **0e Frontend complete** — 32 files (23 core + 9 optional deep specs). 0f Implementation planned (19 files). |
| 1.4.0 | Jan 2025 | **0f Implementation complete** — 20 files (7 admin + 13 phase files). 0g Testing planned (14 files, phases 54-60). |
| 1.5.0 | Jan 2025 | **0h Prerelease complete** — 16 files (12 admin + 4 phase prompts). v4.2 with Safety Pins, Lock Status Tracker, enterprise features. |
| 1.6.0 | Jan 2025 | **0i Rollout complete** — 15 files (11 admin + 4 phase prompts). v1.4 with Readiness Review, Abort Criteria, Authority Matrix, Canary Confidence Score. |
| 1.7.0 | Jan 2025 | **External Audit System documented** — Added External Audit System section, Human Guides section, Governance Documents section. Updated Pipeline Overview with audit checkpoints. Updated File Locations. |

---
