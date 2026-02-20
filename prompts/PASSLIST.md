✅ PASS 1 — SYSTEM GROUNDING CHECKLIST

(SYSTEM.md + ADMIN.md)

Goal: Establish why the system exists and where its edges are.

You are DONE with Pass 1 when:
Purpose & Scope

☐ System purpose is stated in 1–2 sentences

☐ Scope lists what it owns, not how it works

☐ Explicit non-goals are written (this is critical)

Boundaries

☐ Allowed dependencies listed

☐ Forbidden dependencies explicitly stated

☐ You can explain why this system must exist separately

Maturity & Reality

☐ Status set (Concept / Draft / Active / Stable)

☐ Known risks noted (even vaguely)

☐ Open questions parked (not solved)

Sanity Test

☐ Could another developer understand what this system is in 60 seconds?

If no → rewrite SYSTEM.md
If yes → move on

🚫 Do NOT:

Define schemas

Name error codes

Design internals

✅ PASS 2 — SUBSYSTEM PRE-SPECS CHECKLIST

(SUBSYSTEM.md + INDEX.md)

Goal: Lock responsibilities and interactions before detail.

For EACH subsystem/module:
Ownership Clarity

☐ Purpose clearly stated (1 short paragraph)

☐ “Owns” list is explicit

☐ “Does NOT own” list exists (this prevents drift)

Inputs & Outputs

☐ Inputs listed with source system

☐ Outputs listed with destination system

☐ No “magic” data appearing from nowhere

Dependencies

☐ All system dependencies declared

☐ Any questionable dependency challenged

☐ Tech/library assumptions noted (lightly)

Errors (Categories Only)

☐ Error types identified (not codes)

☐ You know which errors are recoverable vs fatal

☐ You know if resilience is involved

Tests (Intent Only)

☐ Happy path described

☐ At least one failure path identified

☐ At least one boundary condition noted

System Index Checks

☐ Every subsystem appears in INDEX.md

☐ Status column filled (Concept / Draft / Stable)

☐ Spec/Test columns honest (❌ is fine)

Sanity Test

☐ Could you prevent another system from stealing this module’s responsibility?

If no → refine pre-spec
If yes → move on

🚫 Do NOT:

Lock schemas

Write full logic

Add error codes

✅ PASS 3 — FULL SPECS & SCHEMAS CHECKLIST

(SPEC.md + schema files)

Goal: Precision without churn.

Data & Schema

☐ Fields fully defined (type, required, meaning)

☐ Ownership of each field is clear

☐ Constraints documented (lengths, enums, ranges)

Behaviour

☐ Normal flow documented step-by-step

☐ Edge cases explicitly handled

☐ No ambiguous “system decides” language

Error Handling

☐ Error codes defined

☐ Each error maps to a category from Pass 2

☐ Retry vs escalate behaviour defined

☐ Resilience integration explicit (if applicable)

Dependencies

☐ Dependencies versioned or pinned

☐ Dependency failure behaviour documented

Observability

☐ Events emitted listed

☐ Logs described

☐ Metrics identified (even basic)

Sanity Test

☐ Could someone implement this without asking you questions?

If no → spec incomplete
If yes → move on

🚫 Do NOT:

Change responsibilities

Add new dependencies casually

✅ PASS 4 — INTEGRATION & HARDENING CHECKLIST

(Project-wide)

Goal: Make the system enforceable, not just correct.

Cross-System Consistency

☐ Dependencies match the global contract

☐ No circular dependencies introduced

☐ Brand / access / resilience respected everywhere

Error & Failure Policy

☐ Error codes registered centrally

☐ Escalation paths consistent

☐ Human intervention points explicit

Indexing & Docs

☐ SYSTEMS-INDEX.md updated

☐ All system INDEX.md files accurate

☐ No orphan modules

Enforcement Readiness

☐ Import rules are enforceable

☐ CI could theoretically block violations

☐ Migration notes added if structure changed

Sanity Test

☐ Would this survive another developer joining the project?

If no → fix before building
If yes → ship confidently

🔐 MASTER RULE (Pin This)

If you feel tempted to jump ahead a pass, it means the current pass isn’t finished.

This checklist order is what keeps:

brand logic from leaking

failure logic from duplicating

schemas from being rewritten

specs from becoming lies