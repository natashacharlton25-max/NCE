# PASS 0 — Subsystem Viability

Subsystem: BrandPermissionResolver
Parent System: access

## One-Sentence Job
Resolves effective permissions within a specific brand context, applying brand-scoped constraints to access decisions.

## Distinct Responsibility?
Yes

## Overlap With Siblings?
None — RoleManager owns role definitions, CapabilityManager owns capability enforcement; BrandPermissionResolver owns brand-scoping logic only.

## Stability Expectation
Stable

## Size Signal
500–1000 LOC

## Verdict
KEEP

**Status:** Approved
