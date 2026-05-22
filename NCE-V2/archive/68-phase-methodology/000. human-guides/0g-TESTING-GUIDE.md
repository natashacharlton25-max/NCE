# Section 0g: Testing

## Human Guide

---
**What this section does:** Makes sure the code works correctly
**Phases:** 54-60
**Time:** A day to several days
**Audit:** Recommended
---

## What Happens in This Section?

You have code. Now you need to make sure it works:

1. **Plan testing** - Decide what to test
2. **Write unit tests** - Test individual pieces
3. **Write integration tests** - Test pieces working together
4. **Write E2E tests** - Test complete user journeys
5. **Run tests** - Execute and verify results

**Think of it like:** Inspecting the house before moving in.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Review test plan | Does it cover the important stuff? |
| Check coverage | Are the critical paths tested? |
| Review results | Did tests pass? |
| Decide on failures | What to do about failing tests? |

---

## The Phases

| Phase | Name | What Happens |
|-------|------|--------------|
| 54 | Test Planning | Define testing strategy |
| 55-56 | Unit Tests | Test individual functions |
| 57-58 | Integration Tests | Test components together |
| 59 | E2E Tests | Test complete user flows |
| 60 | Test Verification | Run all tests, verify coverage |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| TEST-PLAN.md | Testing strategy |
| COVERAGE-TARGETS.md | How much to test |
| TEST-TRACE.md | Maps tests to specs |
| TEST-VERIFICATION.md | Results summary |
| Coverage report | Actual coverage numbers |

---

## Understanding Test Types

| Type | What It Tests | Example |
|------|---------------|---------|
| **Unit** | One function in isolation | "Does the calculateTotal function give the right answer?" |
| **Integration** | Multiple pieces together | "Can the user service talk to the database correctly?" |
| **E2E** | Complete user journey | "Can a user sign up, log in, and make a purchase?" |

All three types are important!

---

## Understanding Coverage

"Coverage" means what percentage of the code is tested.

| Coverage | Meaning |
|----------|---------|
| 100% | Every line of code has a test (rare in practice) |
| 80%+ | Good coverage |
| 60-80% | Acceptable for most projects |
| <60% | Risky - many untested paths |

**Important:** High coverage doesn't guarantee quality. Tests need to actually check the right things.

---

## Understanding TEST-TRACE

This document maps tests back to specs:

```
Spec: User can reset password
  → Test: test_password_reset_sends_email
  → Test: test_password_reset_updates_password
  → Test: test_password_reset_expires_after_24h
```

This ensures every spec requirement has at least one test.

---

## Common Questions

### "What coverage should we aim for?"
Depends on the project. Typically:
- Critical paths: 90%+
- Business logic: 80%+
- UI components: 60-70%

### "A test is failing. Is that bad?"
It depends:
- If it's a real bug → Good! The test caught it. Fix the code.
- If the test is wrong → Update the test.
- If the spec changed → Update both spec and test.

### "What's a 'flaky' test?"
A test that sometimes passes and sometimes fails. Flaky tests are bad because you can't trust them. They should be fixed or removed.

### "This is taking a long time"
Testing does take time, but it's worth it. Bugs found now are cheaper to fix than bugs found by users.

---

## Troubleshooting

### "Coverage is too low"
Ask Claude: "What areas have low coverage? Which specs don't have tests?"

### "Tests keep failing"
Ask Claude to investigate. Say: "Test [name] is failing. Can you diagnose the issue?"

### "There are flaky tests"
Ask Claude: "Test [name] is flaky (passes sometimes, fails sometimes). Can you fix it or explain why?"

### "I don't understand the test results"
Ask Claude: "Explain the test results in plain English. What passed, what failed, and why?"

---

## When You're Done

You'll have:
- ✅ TEST-PLAN.md complete
- ✅ Unit tests written and passing
- ✅ Integration tests written and passing
- ✅ E2E tests written and passing
- ✅ Coverage meets targets
- ✅ TEST-TRACE.md shows all specs covered
- ✅ TEST-VERIFICATION.md complete

**Next:** Move to Section 0h (Prerelease)

---

## Audit (Recommended)

Consider running the 0g audit if:
- You want confidence in test quality
- Coverage seems low
- You're unsure if critical paths are tested

**Audit files:** `00. external-audit/0g-testing/`

**What the audit checks:**
- Does every spec have tests?
- Is coverage adequate?
- Are tests reliable (not flaky)?
- Do tests actually verify the right things?

---

*Testing is your safety net. Good tests catch bugs before users do.*
