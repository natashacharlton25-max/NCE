> **Superseded by NCE-V2's merged `PASS-1-PROMPT.md` (Phase 14 Grounding) and `PASS-2-PROMPT.md` (Phase 15 Contracts) which absorb SUBSYSTEM pre-spec production; retained for history (2026-05-22).**

You are writing a PRE-SPEC for a subsystem/module.
Do NOT invent functionality. If information is missing, leave TODO markers.

Context:
- This locks responsibility and boundaries.
- This is NOT a full spec.
- No schemas, no error codes, no algorithms.

Subsystem:
- Name: <SUBSYSTEM_NAME>
- Parent system: <SYSTEM_NAME>

Task:
Write SUBSYSTEM.md that defines:
- Purpose (1 short paragraph)
- What this module owns
- What it explicitly does NOT own
- Inputs (with source system)
- Outputs (with destination system)
- System dependencies
- Tech/library assumptions (light)
- Error categories (types only)
- Test intent (happy path, failure, boundary)

Rules:
- Be strict about ownership.
- If something is ambiguous, state it as a note.

Output:
Markdown only.
