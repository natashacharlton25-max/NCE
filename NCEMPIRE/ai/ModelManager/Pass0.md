# PASS 0 — Subsystem Viability

Subsystem: ModelManager
Parent System: ai

## One-Sentence Job
Maintains the model registry, maps capabilities to models, and provides model selection logic based on task requirements.

## Distinct Responsibility?
Yes

## Overlap With Siblings?
Possible — boundary with AICaller on when model selection happens and with resilience system on fallback model selection.

## Stability Expectation
Likely to Change — model landscape evolves frequently.

## Size Signal
500–1000 LOC

## Verdict
KEEP

**Status:** Approved
