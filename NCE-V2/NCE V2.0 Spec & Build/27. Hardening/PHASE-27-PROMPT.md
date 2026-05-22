# PHASE 27: HARDENING

---
Phase: 27
Name: Hardening (review specs for implementation readiness)
Location: NCE-V2/NCE V2.0 Spec & Build/27. Hardening/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/SYSTEMHARDENINGPROMPT.MD (A's hardening prompt absorbed)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You review all completed NCE-V2 specs for implementation readiness — completeness, ambiguity, implementability, test coverage. Produce 4 review passes + 2 summary docs.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../../FileTree-v2.md)
5. All component specs (Phases 21–24)
6. Phase 25 analysis docs (PATTERN, TYPE, ERROR-CODE, DEPENDENCY, GAP)
7. Phase 26 project-wide docs

---

## PURPOSE

Hardening ensures specs are:
- **Complete** — no missing information
- **Unambiguous** — one interpretation only
- **Consistent** — no contradictions
- **Implementable** — Claude Code (or any developer) can build from them without questions
- **Testable** — clear success criteria

---

## TASK

Perform 4 review passes:

| Pass | Focus | Output |
|------|-------|--------|
| 1 | Completeness | `COMPLETENESS-REVIEW.md` |
| 2 | Ambiguity | `AMBIGUITY-REVIEW.md` |
| 3 | Implementability | `IMPLEMENTABILITY-REVIEW.md` |
| 4 | Test Coverage | `TEST-COVERAGE-REVIEW.md` |

Then produce:
- `HARDENING-SUMMARY.md` — overall verdict + metrics
- `IMPLEMENTATION-READY.md` — go/no-go gate

---

## PASS 1 — COMPLETENESS

Verify every component's 10 spec files (Phase 21) and the per-service spec sets (Phase 22) have all required sections filled. Use `COMPLETENESS-REVIEW-TEMPLATE.md`.

NCE-V2-specific completeness checks:
- [ ] Output Form declared on every component
- [ ] D1 binding name declared on every library-touching component
- [ ] Worker secret binding name declared on every integration
- [ ] LOC band declared on every spec (Low/Medium/High)

---

## PASS 2 — AMBIGUITY

Find and flag ambiguous specifications. Use `AMBIGUITY-REVIEW-TEMPLATE.md`.

Patterns to catch:
- Vague language ("should", "might", "usually") → replace with "must", "will"
- Missing edge cases ("validate email" → specify: empty / too long / invalid format)
- Undefined behavior ("returns error" → which error code?)
- Implicit assumptions ("user must exist" → state explicitly, specify error if not)
- Optional without default ("timeout (optional)" → specify default value)
- TypeScript-specific: `any` types without justification — flag

NCE-V2-specific ambiguity flags:
- Library access without `library/Librarian` mediation (must be explicit)
- Output Form ambiguity (JSON vs rendered)
- Async/await missing on D1 calls

---

## PASS 3 — IMPLEMENTABILITY

Ensure specs can be implemented without further questions. Use `IMPLEMENTABILITY-REVIEW-TEMPLATE.md`.

Checks:
- **Data flow:** input sources, output destinations, transformation steps explicit
- **Dependencies:** all exist; circular resolved
- **External services:** API endpoints, auth, rate limits, error mapping complete
- **Storage:** D1 schema matches TS types; queries match access patterns; indexes support queries
- **Configuration:** all config has defaults; Worker bindings + secrets named

NCE-V2-specific implementability checks:
- Worker topology coherent (every system has its Worker grouping declared)
- D1 migrations exist for every library schema declared in STORAGE.md
- `resilience/` patterns referenced where external integrations or critical D1/R2 ops occur
- TypeScript types are concrete (not `any`)

---

## PASS 4 — TEST COVERAGE

Ensure specs define what to test. Use `TEST-COVERAGE-REVIEW-TEMPLATE.md`.

Required coverage:
- Unit tests per public function (FUNCTIONS.md)
- Validation tests per input field (TYPES.md / CONTRACT.md)
- Error tests per error code (ERRORS.md)
- Integration tests per dependency call
- Edge case tests per documented edge case

NCE-V2-specific:
- D1 schema migration tests
- Worker fetch handler tests (request/response shape)
- resilience/ pattern tests (retry, fallback, rate-limit)

---

## OUTPUT LOCATION

```
NCE-V2/specs/hardening/
├── COMPLETENESS-REVIEW.md
├── AMBIGUITY-REVIEW.md
├── IMPLEMENTABILITY-REVIEW.md
├── TEST-COVERAGE-REVIEW.md
├── HARDENING-SUMMARY.md
└── IMPLEMENTATION-READY.md
```

---

## MANDATORY RULES

- Be thorough — check every component
- Be specific — cite exact file locations
- Be actionable — provide resolutions
- Prioritize blocking vs nice-to-have
- Do **NOT** fix issues inline — document for fixing
- Do **NOT** proceed with blocking issues
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## APPROVAL GATE

Phase 27 is COMPLETE when:
- [ ] All 4 review passes complete
- [ ] HARDENING-SUMMARY.md created
- [ ] IMPLEMENTATION-READY.md created
- [ ] All blocking issues resolved
- [ ] Human approves go/no-go

---

## TEMPLATES (enriched for NCE-V2)

- [COMPLETENESS-REVIEW-TEMPLATE.md](./COMPLETENESS-REVIEW-TEMPLATE.md)
- [AMBIGUITY-REVIEW-TEMPLATE.md](./AMBIGUITY-REVIEW-TEMPLATE.md)
- [IMPLEMENTABILITY-REVIEW-TEMPLATE.md](./IMPLEMENTABILITY-REVIEW-TEMPLATE.md)
- [TEST-COVERAGE-REVIEW-TEMPLATE.md](./TEST-COVERAGE-REVIEW-TEMPLATE.md)
- [HARDENING-SUMMARY-TEMPLATE.md](./HARDENING-SUMMARY-TEMPLATE.md)
- [IMPLEMENTATION-READY-TEMPLATE.md](./IMPLEMENTATION-READY-TEMPLATE.md)

---

## NEXT PHASE

After Phase 27 approval → Phase 28 (Full Spec Checklist)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
