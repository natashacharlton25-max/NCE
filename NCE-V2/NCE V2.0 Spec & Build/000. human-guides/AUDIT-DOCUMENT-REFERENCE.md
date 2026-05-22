# External Audit System: Complete Document Reference

---
**Location:** `00. external-audit/`
**Purpose:** Second-opinion quality checks using another AI
---

## Folder Structure

```
00. external-audit/
├── core/                      ← Core audit documents
├── templates/                 ← Shared templates
├── 0a-concept/               ← Optional audit
├── 0b-passphases/            ← Recommended audit
├── 0c-fullspecs/             ← ⭐ MANDATORY audit
├── 0d-precode/               ← Optional audit
├── 0e-frontend/              ← Optional audit
├── 0f-implementation/        ← ⭐ MANDATORY audit
├── 0g-testing/               ← Recommended audit
├── 0h-prerelease/            ← ⭐ MANDATORY audit
├── 0i-rollout/               ← Optional (post-deploy)
└── 00-external-audit-README.md
```

---

## Core Documents

**Location:** `00. external-audit/core/`

| Document | What It Does | When To Use |
|----------|--------------|-------------|
| **EXTERNAL-AUDIT-GUIDE.md** | Master guide explaining the audit system. | Read first |
| **AUDIT-PRINCIPLES.md** | Core principles (advisory not authoritative, etc.). | Reference |
| **CROSS-AI-VALIDATION.md** | How to handle disagreements between AIs. | When AIs disagree |

---

## Template Documents

**Location:** `00. external-audit/templates/`

| Document | What It Does | When To Use |
|----------|--------------|-------------|
| **HANDOFF-TEMPLATE.md** | Generic template for audit handoff. | If section-specific missing |
| **REPORT-TEMPLATE.md** | Generic template for audit report. | If section-specific missing |
| **FINDINGS-TEMPLATE.md** | Template for documenting findings. | For all audits |

---

## Per-Section Audit Files

Each section folder has 5 files:

| File | What It Does | Who Uses It |
|------|--------------|-------------|
| **0X-HANDOFF-PROMPT.md** | Instructions for Claude to prepare audit handoff | Claude |
| **0X-AUDIT-PROMPT.md** | Instructions for external AI to run audit | You paste to external AI |
| **0X-CHECKLIST.md** | List of things to verify in this section | External AI |
| **0X-FILE-MANIFEST.md** | List of all available files for this section | Reference for you/auditor |
| **0X-REPORT-TEMPLATE.md** | Structure for audit report | External AI |

---

## How To Run An Audit

### Step 1: Claude Creates Handoff
Claude automatically creates `0X-AUDIT-HANDOFF.md` when a section completes.

### Step 2: Open External AI
Open ChatGPT or Google Gemini in a new tab.

### Step 3: Start the Audit
Paste two things:
1. `0X-AUDIT-PROMPT.md` (from the audit folder)
2. `0X-AUDIT-HANDOFF.md` (that Claude created)

### Step 4: Provide Files
The auditor will ask for files. Use `0X-FILE-MANIFEST.md` to find them.

### Step 5: Get Report
The auditor produces a report with scores, findings, and verdict.

### Step 6: Address Findings
Give the report to Claude. Fix issues based on severity.

---

## Section-by-Section Audit Files

### 0a Concept (Optional)

**Location:** `00. external-audit/0a-concept/`

| File | What It Does |
|------|--------------|
| **0a-HANDOFF-PROMPT.md** | Claude instructions to summarize 0a work |
| **0a-AUDIT-PROMPT.md** | External AI instructions for 0a audit |
| **0a-CHECKLIST.md** | Verify project definition, systems, subsystems, integrations |
| **0a-FILE-MANIFEST.md** | Lists: PROJECT-OVERVIEW, PROJECT-INTENTION, SYSTEM-NOTES, etc. |
| **0a-REPORT-TEMPLATE.md** | Structure for 0a audit report |

---

### 0b PassPhases (Recommended)

**Location:** `00. external-audit/0b-passphases/`

| File | What It Does |
|------|--------------|
| **0b-HANDOFF-PROMPT.md** | Claude instructions to summarize passes |
| **0b-AUDIT-PROMPT.md** | External AI instructions for architecture audit |
| **0b-CHECKLIST.md** | Verify contracts, dependencies, circular dependency check |
| **0b-FILE-MANIFEST.md** | Lists: CONTRACT.md files, DEPENDENCY-MAP.md, etc. |
| **0b-REPORT-TEMPLATE.md** | Structure for 0b audit report |

---

### 0c Full Specs (⭐ MANDATORY)

**Location:** `00. external-audit/0c-fullspecs/`

| File | What It Does |
|------|--------------|
| **0c-HANDOFF-PROMPT.md** | Claude instructions to summarize all specs |
| **0c-AUDIT-PROMPT.md** | External AI instructions for spec audit |
| **0c-CHECKLIST.md** | Verify completeness, consistency, implementability |
| **0c-FILE-MANIFEST.md** | Lists all 10 spec files per component, analysis files, standards |
| **0c-REPORT-TEMPLATE.md** | Structure for 0c audit report |

---

### 0d PreCode (Optional)

**Location:** `00. external-audit/0d-precode/`

| File | What It Does |
|------|--------------|
| **0d-HANDOFF-PROMPT.md** | Claude instructions to summarize consolidation |
| **0d-AUDIT-PROMPT.md** | External AI instructions for consolidation audit |
| **0d-CHECKLIST.md** | Verify DATABASE-SCHEMA matches STORAGE.md files, API matches FUNCTIONS.md |
| **0d-FILE-MANIFEST.md** | Lists: DATABASE-SCHEMA.md, API-SURFACE.md, source STORAGE/FUNCTIONS files |
| **0d-REPORT-TEMPLATE.md** | Structure for 0d audit report |

---

### 0e Frontend (Optional)

**Location:** `00. external-audit/0e-frontend/`

| File | What It Does |
|------|--------------|
| **0e-HANDOFF-PROMPT.md** | Claude instructions to summarize frontend specs |
| **0e-AUDIT-PROMPT.md** | External AI instructions for frontend audit |
| **0e-CHECKLIST.md** | Verify CONTRACT.md, accessibility (WCAG A/AA), API mappings |
| **0e-FILE-MANIFEST.md** | Lists: PAGE-SPEC files, COMPONENT-SPEC files, CONTRACT, A11Y |
| **0e-REPORT-TEMPLATE.md** | Structure for 0e audit report |

---

### 0f Implementation (⭐ MANDATORY)

**Location:** `00. external-audit/0f-implementation/`

| File | What It Does |
|------|--------------|
| **0f-HANDOFF-PROMPT.md** | Claude instructions to summarize implementation |
| **0f-AUDIT-PROMPT.md** | External AI instructions for code audit |
| **0f-CHECKLIST.md** | Verify spec compliance, security, code quality, deviations documented |
| **0f-FILE-MANIFEST.md** | Lists: Source code, DEVIATION-REPORT, specs for comparison |
| **0f-REPORT-TEMPLATE.md** | Structure for 0f audit report |

---

### 0g Testing (Recommended)

**Location:** `00. external-audit/0g-testing/`

| File | What It Does |
|------|--------------|
| **0g-HANDOFF-PROMPT.md** | Claude instructions to summarize testing |
| **0g-AUDIT-PROMPT.md** | External AI instructions for test audit |
| **0g-CHECKLIST.md** | Verify traceability, coverage, no flaky tests |
| **0g-FILE-MANIFEST.md** | Lists: TEST-TRACE.md, TEST-PLAN.md, test code, coverage report |
| **0g-REPORT-TEMPLATE.md** | Structure for 0g audit report |

---

### 0h Prerelease (⭐ MANDATORY)

**Location:** `00. external-audit/0h-prerelease/`

| File | What It Does |
|------|--------------|
| **0h-HANDOFF-PROMPT.md** | Claude instructions to summarize prerelease |
| **0h-AUDIT-PROMPT.md** | External AI instructions for final audit |
| **0h-CHECKLIST.md** | Verify RC immutable, locks respected, performance/security met |
| **0h-FILE-MANIFEST.md** | Lists: RC tag, PERFORMANCE-BASELINE, SECURITY-AUDIT, DEPLOYMENT-GUIDE |
| **0h-REPORT-TEMPLATE.md** | Structure for 0h audit report |

---

### 0i Rollout (Optional, Post-Deploy)

**Location:** `00. external-audit/0i-rollout/`

| File | What It Does |
|------|--------------|
| **0i-HANDOFF-PROMPT.md** | Claude instructions to summarize deployment |
| **0i-AUDIT-PROMPT.md** | External AI instructions for retrospective |
| **0i-CHECKLIST.md** | Verify deployment executed correctly, incidents documented |
| **0i-FILE-MANIFEST.md** | Lists: DEPLOYMENT-LOG, MONITORING-LOG, INCIDENT-REPORT |
| **0i-REPORT-TEMPLATE.md** | Structure for 0i audit report |

---

## Audit Priority Summary

| Section | Required? | When to Run |
|---------|-----------|-------------|
| 0a Concept | Optional | Complex projects |
| 0b PassPhases | Recommended | Architecture decisions |
| **0c Full Specs** | **⭐ MANDATORY** | After Phase 28 |
| 0d PreCode | Optional | If 0c had issues |
| 0e Frontend | Optional | Complex UI |
| **0f Implementation** | **⭐ MANDATORY** | After Phase 53 |
| 0g Testing | Recommended | Test confidence |
| **0h Prerelease** | **⭐ MANDATORY** | After Phase 64 |
| 0i Rollout | Optional | Post-deploy review |

---

## Understanding the Report

### Scores (1-5)
- **5** = Excellent
- **4** = Good
- **3** = Acceptable
- **2** = Needs work
- **1** = Major problems

### Severity
| Icon | Level | Action |
|------|-------|--------|
| 🔴 | Critical | Must fix now |
| 🟠 | Important | Should fix |
| 🟡 | Minor | Nice to fix |
| 🔵 | Suggestion | Consider |

### Verdicts
| Verdict | Meaning | Action |
|---------|---------|--------|
| ✅ PASS | Good to go | Continue |
| ⚠️ CONDITIONAL | Mostly good | Fix critical, continue |
| ❌ FAIL | Problems | Fix, may re-audit |

---

*The audit is advisory, not authoritative. You make the final decisions.*
