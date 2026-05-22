# License Compliance Report

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
---

## Summary

| Field | Value |
|-------|-------|
| Total Dependencies | {{N}} |
| Permitted | {{N}} |
| Review Required | {{N}} |
| Restricted | {{N}} |
| **Overall Status** | ☐ **PASS** / ☐ **FAIL** |

---

## License Categories

| Category | Licenses | Action |
|----------|----------|--------|
| **Permitted** | MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC, 0BSD, Unlicense, CC0-1.0 | Auto-pass |
| **Review Required** | MPL-2.0, LGPL-2.1, LGPL-3.0, EPL-1.0, EPL-2.0 | Requires explicit approval |
| **Restricted** | GPL-2.0, GPL-3.0, AGPL-3.0, SSPL, CC-BY-NC | Fails unless isolated |

---

## Permitted Licenses

These dependencies have permitted licenses and auto-pass.

| Package | Version | License | Status |
|---------|---------|---------|--------|
| {{package}} | {{version}} | MIT | ✅ Pass |
| {{package}} | {{version}} | Apache-2.0 | ✅ Pass |
| {{package}} | {{version}} | ISC | ✅ Pass |

**Total Permitted:** {{N}}

---

## Review Required Licenses

These dependencies require explicit approval before release.

| Package | Version | License | Approved By | Date | Status |
|---------|---------|---------|-------------|------|--------|
| {{package}} | {{version}} | LGPL-3.0 | | | ☐ Approved |

**Approval Criteria:**
- License terms reviewed by legal/compliance
- Usage complies with license requirements
- Any required attributions documented

**Total Review Required:** {{N}}

---

## Restricted Licenses

These dependencies are restricted and fail compliance unless isolated.

| Package | Version | License | Isolation Status | Notes |
|---------|---------|---------|------------------|-------|
| {{package}} | {{version}} | GPL-3.0 | ☐ Isolated / ☐ Not isolated | |

**Isolation Requirements:**
- Package must run in separate process
- No linking with main application
- Clear boundary documented

**Total Restricted:** {{N}}

---

## Unknown/Missing Licenses

These dependencies have unknown or missing license information.

| Package | Version | Source | Action Required |
|---------|---------|--------|-----------------|
| {{package}} | {{version}} | {{npm/GitHub}} | Investigate |

**Action:** Manually verify license from package source.

**Total Unknown:** {{N}}

---

## Transitive Dependencies

### High-Risk Transitives

| Package | Version | License | Introduced By |
|---------|---------|---------|---------------|
| {{package}} | {{version}} | {{license}} | {{parent-package}} |

---

## Compliance Checklist

| Check | Status |
|-------|--------|
| All production dependencies analyzed | ☐ |
| No restricted licenses in main bundle | ☐ |
| All review-required licenses approved | ☐ |
| Unknown licenses investigated | ☐ |
| Required attributions documented | ☐ |

---

## Attributions Required

The following packages require attribution in documentation or UI:

| Package | License | Attribution Text |
|---------|---------|------------------|
| {{package}} | Apache-2.0 | {{attribution}} |

---

## Tools Used

| Tool | Command | Notes |
|------|---------|-------|
| license-checker | `npx license-checker --summary` | Primary scan |
| license-checker-rseidelsohn | `npx license-checker-rseidelsohn --json` | Detailed output |

---

## Scan Results

```
# Output from license scanning tool
{{SCAN_OUTPUT}}
```

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Reviewer | | | ☐ |
| Legal/Compliance | | | ☐ (if required) |

---

## Final Status

| Result | Selected |
|--------|----------|
| **PASS** - All licenses compliant | ☐ |
| **FAIL** - License issues must be resolved | ☐ |

**If FAIL:**

| Issue | Required Action |
|-------|-----------------|
| | |

---
Generated: {{timestamp}}
Phase: 63 - Performance & Security
Template: LICENSE-REPORT-TEMPLATE.md v1.0
---
