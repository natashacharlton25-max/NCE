# 00. external-audit

---
Location: 00. external-audit/ (root level, alongside 0. Admin/)
Purpose: External AI audit system for quality assurance
Version: 1.0.0
---

## What This Is

This folder contains everything needed to run **external AI audits** on Claude's work. External audits use another AI (ChatGPT, Gemini, etc.) to review what Claude produced, providing a second opinion.

---

## Folder Structure

```
Project Root/
├── 0. Admin/                  # Section admin files
│   ├── 0a. Concept-to-PassPhases/
│   ├── 0b. PassPhases 0-4/
│   ├── 0c. Full Specs/
│   └── ... etc
│
├── 00. external-audit/        # ← THIS FOLDER
│   ├── core/                  # Core documents (apply to all sections)
│   │   ├── SCORING-RUBRIC.md
│   │   └── AUDIT-PROCESS.md
│   │
│   ├── 0c-fullspecs/          # Section 0c audit files ⭐ MANDATORY
│   │   ├── 0c-HANDOFF-PROMPT.md
│   │   ├── 0c-AUDIT-PROMPT.md
│   │   ├── 0c-CHECKLIST.md
│   │   ├── 0c-FILE-MANIFEST.md
│   │   └── 0c-REPORT-TEMPLATE.md
│   │
│   ├── 0f-implementation/     # Section 0f audit files ⭐ MANDATORY
│   │   └── ...
│   │
│   ├── 0h-prerelease/         # Section 0h audit files ⭐ MANDATORY
│   │   └── ...
│   │
│   ├── templates/             # Reusable templates
│   │   └── AUDIT-SUMMARY-TEMPLATE.md
│   │
│   └── reports/               # Completed audit reports
│
├── 1. Project Overview/
├── 2. Project Intention/
└── ... phase folders
```

---

## Mandatory Audits

Three sections REQUIRE external audit before proceeding:

| Section | Phase | Why |
|---------|-------|-----|
| **0c Full Specs** | 28 | Last chance before code - errors multiply |
| **0f Implementation** | 53 | Code must match specs exactly |
| **0h Prerelease** | 64 | Last gate before production |

---

## How to Run an Audit

### Quick Version

1. Complete the section (0c, 0f, or 0h)
2. Claude prepares handoff automatically (0X-AUDIT-HANDOFF.md)
3. Open ChatGPT or Gemini
4. Paste the audit prompt + handoff
5. Provide requested files
6. Get report, address findings
7. Proceed to next section

### Detailed Version

See `core/AUDIT-PROCESS.md` for step-by-step instructions.

---

## File Types Explained

| File | Purpose | Used By |
|------|---------|---------|
| `*-HANDOFF-PROMPT.md` | Tells Claude how to prepare | Claude |
| `*-AUDIT-PROMPT.md` | Tells external AI how to audit | ChatGPT/Gemini |
| `*-CHECKLIST.md` | What to verify | External AI |
| `*-FILE-MANIFEST.md` | What files exist | External AI |
| `*-REPORT-TEMPLATE.md` | Format for findings | External AI |
| `*-AUDIT-HANDOFF.md` | Claude's prepared package | Human → External AI |

---

## Scoring System

Audits score work in 6 categories:

| Category | Weight | What It Measures |
|----------|--------|------------------|
| Completeness | 25% | All required items present |
| Consistency | 20% | No contradictions |
| Accuracy | 20% | Content is correct |
| Methodology | 15% | Follows Fullspecs rules |
| Quality | 10% | Clear and well-written |
| Readiness | 10% | Ready for next section |

**Verdicts:**
- ✅ PASS (≥4.0, no critical findings) - Proceed
- ⚠️ CONDITIONAL (≥3.0, ≤2 critical) - Fix issues, then proceed
- ❌ FAIL (<3.0 or >2 critical) - Fix issues, re-audit

See `core/SCORING-RUBRIC.md` for full details.

---

## Finding Severity

| Level | Icon | Meaning |
|-------|------|---------|
| Critical | 🔴 | Must fix before proceeding |
| Important | 🟠 | Should fix before proceeding |
| Minor | 🟡 | Fix if time permits |
| Suggestion | 🔵 | Optional improvement |

---

## Where Audit Reports Go

Save completed audit reports in:
```
00. external-audit/reports/0X-audit-report-YYYY-MM-DD.md
```

Or in your project's `/audits/` folder.

---

## Key Principle

**Audits are advisory, not authoritative.**

The external AI gives recommendations. You (the human) make the final decision. You can:
- Accept findings and fix them
- Accept the risk and proceed anyway
- Dispute findings you disagree with

Document your decisions in the audit report.

---

## Related Files

These Claude-*.md files (in 0. Admin/) have been updated to trigger audit handoffs:
- `0. Admin/0c. Full Specs/Claude-fullspecs.md`
- `0. Admin/0f. Implementation/Claude-implementation.md`
- `0. Admin/0h. Prerelease/Claude-prerelease.md`

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | Jan 2025 | Initial release - Phase 1 (0c, 0f, 0h) |

---

*End of README*
