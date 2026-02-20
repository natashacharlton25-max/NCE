# PASS 0 — Subsystem Viability

Subsystem: GlobalRules
Parent System: orchestration

## One-Sentence Job
Defines and enforces system-wide rules that apply across all operations.

## Distinct Responsibility?
Yes

## Overlap With Siblings?
None — GlobalRules defines cross-cutting rules; PolicyEngine evaluates specific policies; brand/BrandRulesEngine handles brand-specific rules.

## Stability Expectation
Stable

## Size Signal
300–450 LOC

## Verdict
KEEP

**Status:** Approved
