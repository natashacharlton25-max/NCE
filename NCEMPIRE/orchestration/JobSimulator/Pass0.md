# PASS 0 — Subsystem Viability

Subsystem: JobSimulator
Parent System: orchestration

## One-Sentence Job
Simulates job execution for testing, preview, and dry-run scenarios.

## Distinct Responsibility?
Yes

## Overlap With Siblings?
None — JobSimulator simulates jobs; TestHandler manages tests; Orchestrator runs actual workflows.

## Stability Expectation
Stable

## Size Signal
300–450 LOC

## Verdict
KEEP

**Status:** Approved
