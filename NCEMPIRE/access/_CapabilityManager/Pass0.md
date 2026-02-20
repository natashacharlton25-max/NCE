# PASS 0 — Subsystem Viability

Subsystem: CapabilityManager
Parent System: access

## One-Sentence Job
Defines action-level capabilities and enforces final permission decisions by resolving effective grants against requested actions.

## Distinct Responsibility?
Yes

## Overlap With Siblings?
Possible — boundary with RoleManager on inheritance logic requires explicit ownership decision (role hierarchy vs capability composition).

## Stability Expectation
Stable

## Size Signal
>1000 LOC

## Verdict
KEEP

**Status:** Approved
