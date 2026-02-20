# PASS 0 — Subsystem Viability

Subsystem: RoleManager
Parent System: access

## One-Sentence Job
Defines user roles, manages role assignments, and owns role hierarchy inheritance logic.

## Distinct Responsibility?
Yes

## Overlap With Siblings?
Possible — inheritance boundary with CapabilityManager requires clarification on whether role hierarchy feeds into capability composition.

## Stability Expectation
Stable

## Size Signal
500–1000 LOC

## Verdict
KEEP

**Status:** Approved
