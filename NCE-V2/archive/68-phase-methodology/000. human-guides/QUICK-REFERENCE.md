# Fullspecs Quick Reference Card

---
**Print this out or keep it handy**
---

## Section Overview

| Section | What It Does | Your Main Job | Audit |
|---------|--------------|---------------|-------|
| **0a** | Capture project idea | Describe what you want | Optional |
| **0b** | Define architecture | Review contracts & dependencies | Recommended |
| **0c** | Write detailed specs | Review specs | ⭐ REQUIRED |
| **0d** | Prepare for coding | Approve tech choices | Optional |
| **0e** | Spec the frontend | Provide designs, review | Optional |
| **0f** | Write the code | Review code, approve deviations | ⭐ REQUIRED |
| **0g** | Test everything | Review test results | Recommended |
| **0h** | Prepare for launch | Sign off, final approval | ⭐ REQUIRED |
| **0i** | Deploy & monitor | Watch the launch | Optional |

---

## Key Documents Per Section

### 0a Concept
- PROJECT-OVERVIEW.md - What you're building
- PROJECT-INTENTION.md - What success looks like
- SYSTEM-*.md - Main system descriptions

### 0b PassPhases
- CONTRACT.md - How pieces connect
- DEPENDENCY-MAP.md - Build order
- PASS-NOTES.md - Context summaries

### 0c Full Specs
- 10 files per component (INDEX, OVERVIEW, FUNCTIONS, TYPES, ERRORS, STORAGE, VALIDATION, SECURITY, EVENTS, BOUNDARIES)
- HARDENING-SUMMARY.md - Stress test results

### 0d PreCode
- DATABASE-SCHEMA.md - Database structure
- API-SURFACE.md - All API endpoints
- TECH-STACK.md - Technology choices
- CODING-STANDARDS.md - Code rules

### 0e Frontend
- FRONTEND-BACKEND-CONTRACT.md - How frontend talks to backend
- PAGES.md / COMPONENTS.md - UI specs
- A11Y-CHECKLIST.md - Accessibility

### 0f Implementation
- IMPLEMENTATION-LOG.md - Build record
- DEVIATION-REPORT.md - Changes from specs

### 0g Testing
- TEST-TRACE.md - Spec to test mapping
- Coverage report - What's tested

### 0h Prerelease
- RC tag - The final version
- DEPLOYMENT-GUIDE.md - How to deploy
- RUNBOOK.md - Problem handling

### 0i Rollout
- DEPLOYMENT-LOG.md - What happened
- RELEASE-COMPLETE.md - Final sign-off

---

## Audit Quick Reference

### When to Run

| Audit | After Phase | Skip If |
|-------|-------------|---------|
| 0c | 28 | Never skip |
| 0f | 53 | Never skip |
| 0h | 64 | Never skip |

### How to Run

1. Claude creates handoff (automatic)
2. Open ChatGPT or Gemini
3. Paste audit prompt + handoff
4. Provide files when asked
5. Get report
6. Give findings to Claude
7. Fix issues, continue

### Verdicts

- ✅ PASS → Continue
- ⚠️ CONDITIONAL → Fix critical issues, then continue
- ❌ FAIL → Fix issues, consider re-audit

---

## Severity Levels

| Icon | Level | Action |
|------|-------|--------|
| 🔴 | Critical | Must fix now |
| 🟠 | Important | Should fix |
| 🟡 | Minor | Nice to fix |
| 🔵 | Suggestion | Consider |

---

## Golden Rules

1. **Specs are truth** - Code must match specs
2. **No fix forward** - If conflicts found, go back to 0c
3. **Document deviations** - If code differs from spec, document it
4. **Audits catch errors** - Trust the process
5. **You decide** - Claude advises, you approve

---

## Common Commands for Claude

| When You Want To | Say |
|------------------|-----|
| Check progress | "What phase are we on?" |
| Get a summary | "Summarize what we've done in section 0X" |
| Simplify | "Explain this in plain English" |
| Fix something | "The audit found [issue]. Please fix it." |
| Change something | "I want to change [X] to [Y]" |
| Go back | "We need to go back to 0c and fix [issue]" |

---

## Help Resources

| Guide | What It Covers |
|-------|----------------|
| FULLSPECS-HUMAN-GUIDE.md | Overall methodology |
| AUDIT-SYSTEM-HUMAN-GUIDE.md | How to run audits |
| 0X-SECTION-GUIDE.md | Specific section details |

---

## Governance Documents

| Document | Location | Purpose |
|----------|----------|---------|
| FAILURE-RECOVERY-RULES.md | 0. Admin/ | When to roll back, how far |
| RISK-ACCEPTANCE-TEMPLATE.md | 0. Admin/ | Accept findings without fixing |
| PROJECT-RESUMPTION-CHECKLIST.md | 0. Admin/ | Resume after pause |
| TEAM-MODE-GUIDE.md | 0. Admin/ | Multi-person projects |
| AUDITOR-CONTROL-GUIDE.md | 00. external-audit/core/ | Control audit AI |
| START-HERE.md | 000. human-guides/ | First-time users |

---

## Folder Locations

| Item | Location |
|------|----------|
| Admin docs | `0. Admin/` |
| Audit system | `00. external-audit/` |
| Phase work | Numbered folders (1, 2, 3...) |
| Human guides | `human-guides/` |

---

*Keep this handy. You've got this!*
