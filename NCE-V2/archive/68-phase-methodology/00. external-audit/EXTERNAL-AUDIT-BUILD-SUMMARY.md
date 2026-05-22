# External Audit System - Build Complete (All Phases)

---
Date: January 2025
Status: COMPLETE
Location: 00. external-audit/
Total Files: 48
---

## What Was Built

### Complete Audit System (48 files)

```
00. external-audit/
│
├── core/                          # Core documents
│   ├── SCORING-RUBRIC.md          ✅ How to score
│   └── AUDIT-PROCESS.md           ✅ Step-by-step guide
│
├── 0a-concept/                    # Optional audit
│   ├── 0a-HANDOFF-PROMPT.md       ✅
│   ├── 0a-AUDIT-PROMPT.md         ✅
│   ├── 0a-CHECKLIST.md            ✅
│   ├── 0a-FILE-MANIFEST.md        ✅
│   └── 0a-REPORT-TEMPLATE.md      ✅
│
├── 0b-passphases/                 # Recommended audit
│   ├── 0b-HANDOFF-PROMPT.md       ✅
│   ├── 0b-AUDIT-PROMPT.md         ✅
│   ├── 0b-CHECKLIST.md            ✅
│   ├── 0b-FILE-MANIFEST.md        ✅
│   └── 0b-REPORT-TEMPLATE.md      ✅
│
├── 0c-fullspecs/                  ⭐ MANDATORY
│   ├── 0c-HANDOFF-PROMPT.md       ✅
│   ├── 0c-AUDIT-PROMPT.md         ✅
│   ├── 0c-CHECKLIST.md            ✅
│   ├── 0c-FILE-MANIFEST.md        ✅
│   └── 0c-REPORT-TEMPLATE.md      ✅
│
├── 0d-precode/                    # Optional audit
│   ├── 0d-HANDOFF-PROMPT.md       ✅
│   ├── 0d-AUDIT-PROMPT.md         ✅
│   ├── 0d-CHECKLIST.md            ✅
│   ├── 0d-FILE-MANIFEST.md        ✅
│   └── 0d-REPORT-TEMPLATE.md      ✅
│
├── 0e-frontend/                   # Optional audit
│   ├── 0e-HANDOFF-PROMPT.md       ✅
│   ├── 0e-AUDIT-PROMPT.md         ✅
│   ├── 0e-CHECKLIST.md            ✅
│   ├── 0e-FILE-MANIFEST.md        ✅
│   └── 0e-REPORT-TEMPLATE.md      ✅
│
├── 0f-implementation/             ⭐ MANDATORY
│   ├── 0f-HANDOFF-PROMPT.md       ✅
│   ├── 0f-AUDIT-PROMPT.md         ✅
│   ├── 0f-CHECKLIST.md            ✅
│   ├── 0f-FILE-MANIFEST.md        ✅
│   └── 0f-REPORT-TEMPLATE.md      ✅
│
├── 0g-testing/                    # Recommended audit
│   ├── 0g-HANDOFF-PROMPT.md       ✅
│   ├── 0g-AUDIT-PROMPT.md         ✅
│   ├── 0g-CHECKLIST.md            ✅
│   ├── 0g-FILE-MANIFEST.md        ✅
│   └── 0g-REPORT-TEMPLATE.md      ✅
│
├── 0h-prerelease/                 ⭐ MANDATORY
│   ├── 0h-HANDOFF-PROMPT.md       ✅
│   ├── 0h-AUDIT-PROMPT.md         ✅
│   ├── 0h-CHECKLIST.md            ✅
│   ├── 0h-FILE-MANIFEST.md        ✅
│   └── 0h-REPORT-TEMPLATE.md      ✅
│
├── 0i-rollout/                    # Optional (post-deploy)
│   ├── 0i-HANDOFF-PROMPT.md       ✅
│   ├── 0i-AUDIT-PROMPT.md         ✅
│   ├── 0i-CHECKLIST.md            ✅
│   ├── 0i-FILE-MANIFEST.md        ✅
│   └── 0i-REPORT-TEMPLATE.md      ✅
│
└── templates/
    └── AUDIT-SUMMARY-TEMPLATE.md  ✅
```

**Total: 48 files**

---

## Audit Priority by Section

| Section | Audit Type | When to Run |
|---------|------------|-------------|
| 0a Concept | Optional | Complex projects |
| 0b PassPhases | Recommended | Architecture decisions |
| **0c Full Specs** | ⭐ MANDATORY | After Phase 28 |
| 0d PreCode | Optional | If 0c audit found issues |
| 0e Frontend | Optional | Complex frontend |
| **0f Implementation** | ⭐ MANDATORY | After Phase 53 |
| 0g Testing | Recommended | Test confidence |
| **0h Prerelease** | ⭐ MANDATORY | After Phase 64 |
| 0i Rollout | Optional | Post-deploy review |

---

## Supporting Files Created

| File | Purpose |
|------|---------|
| `CLAUDE-CODE-PROMPT-add-audit-to-sections.md` | Prompt to update Claude-*.md files with audit triggers |
| `00-external-audit-README.md` | README for the audit folder |
| `PLANNING-FINAL-v4_2-external-audit.md` | Complete planning document |

---

## Integration with Fullspecs

### Claude Instructions Updated

The Claude Code prompt updates these files to auto-trigger audits:

| File | What It Does |
|------|--------------|
| `0. Admin/0c. Full Specs/Claude-fullspecs.md` | Creates 0c-AUDIT-HANDOFF.md after Phase 28 |
| `0. Admin/0f. Implementation/Claude-implementation.md` | Creates 0f-AUDIT-HANDOFF.md after Phase 53 |
| `0. Admin/0h. Prerelease/Claude-prerelease.md` | Creates 0h-AUDIT-HANDOFF.md after Phase 64 |

### Workflow After Integration

```
Claude completes 0c/0f/0h
       ↓
Claude auto-creates AUDIT-HANDOFF.md
       ↓
Claude tells human: "Audit required"
       ↓
Human runs audit with external AI
       ↓
Findings addressed
       ↓
Proceed to next section
```

---

## How to Use

### Step 1: Run the Claude Code Prompt

Give `CLAUDE-CODE-PROMPT-add-audit-to-sections.md` to Claude Code in VS Code. It will update the three mandatory section Claude-*.md files.

### Step 2: Work Through Fullspecs Normally

When you reach the end of 0c, 0f, or 0h, Claude will:
1. Prepare the audit handoff automatically
2. Tell you an audit is required
3. Point you to the audit prompt

### Step 3: Run the External Audit

1. Open ChatGPT or Gemini
2. Paste the audit prompt from `00. external-audit/0X-section/0X-AUDIT-PROMPT.md`
3. Paste Claude's handoff
4. Provide requested files
5. Get report
6. Address findings

---

## File Locations Summary

| Item | Location |
|------|----------|
| Audit system | `00. external-audit/` (root level) |
| Section admin files | `0. Admin/0X. Section/` |
| Phase folders | `1. Project Overview/`, etc. |
| Audit reports (when created) | `00. external-audit/reports/` |

---

## Quick Reference

| Section | Audit Type | Prompt Location |
|---------|------------|-----------------|
| 0a Concept | Optional | `00. external-audit/0a-concept/0a-AUDIT-PROMPT.md` |
| 0b PassPhases | Recommended | `00. external-audit/0b-passphases/0b-AUDIT-PROMPT.md` |
| **0c Full Specs** | ⭐ MANDATORY | `00. external-audit/0c-fullspecs/0c-AUDIT-PROMPT.md` |
| 0d PreCode | Optional | `00. external-audit/0d-precode/0d-AUDIT-PROMPT.md` |
| 0e Frontend | Optional | `00. external-audit/0e-frontend/0e-AUDIT-PROMPT.md` |
| **0f Implementation** | ⭐ MANDATORY | `00. external-audit/0f-implementation/0f-AUDIT-PROMPT.md` |
| 0g Testing | Recommended | `00. external-audit/0g-testing/0g-AUDIT-PROMPT.md` |
| **0h Prerelease** | ⭐ MANDATORY | `00. external-audit/0h-prerelease/0h-AUDIT-PROMPT.md` |
| 0i Rollout | Optional | `00. external-audit/0i-rollout/0i-AUDIT-PROMPT.md` |

---

## Build Complete

All 48 files built. System is ready for use.

---

*External Audit System - Complete*
