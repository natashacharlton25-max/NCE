# Phase 2: Validators + Estimator

## 2.1 Budget Validator
**Priority:** 1
**Summary:** Tracks spend per day/week/month. Blocks operations when budget exceeded. Supports shadow billing for free tier.

---

## 2.2 Token Validator
**Priority:** 2
**Summary:** Tracks token usage per model. Enforces token limits per request and per period.

---

## 2.3 API Rate Validator
**Priority:** 3
**Summary:** Tracks requests per minute per provider. Prevents rate limit errors by blocking before limit hit.

---

## 2.4 Provider Rate Limiter
**Priority:** 4
**Summary:** Global rate limiting across ALL systems per provider. Coordinates limits when multiple systems use same API.

---

## 2.5 Job Estimator
**Priority:** 5
**Summary:** Pre-flight estimation of token usage and cost before job runs. Used for budget approval requests.
