> **Superseded by `NCE-V2/NCE V2.0 Spec & Build/15. Pass-2-Contracts/PASS-2-PROMPT.md` (merged Pass-2 prompt for NCE-V2, 2026-05-22); retained for history.**

You are acting as a senior systems architect.Do NOT invent functionality. If information is missing, leave TODO markers.


We are working inside the system: <SYSTEM_NAME>
Target subsystem/module: <SUBSYSTEM_NAME>

Your task is to produce a SUBSYSTEM pre-spec.

Rules:
- Do NOT define schemas
- Do NOT invent error codes
- Do NOT expand scope beyond this subsystem
- Clearly state what this subsystem does NOT own

Produce a SUBSYSTEM.md containing:
- Purpose (1 paragraph)
- Owns / Does NOT Own
- Inputs (with source systems)
- Outputs (with destination systems)
- Dependencies (systems + libraries, if assumed)
- Error categories (types only)
- Test intent (happy path, failure, boundary)

Output as clean Markdown, ready to commit.
