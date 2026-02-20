You are writing a FULL SPECIFICATION for a subsystem.
Do NOT invent functionality. If information is missing, leave TODO markers.

Context:
- Responsibilities are already locked.
- This spec must be implementation-ready.
- Precision is required.

Subsystem:
- Name: <SUBSYSTEM_NAME>
- Parent system: <SYSTEM_NAME>

Task:
Write SPEC.md including:
- Overview
- Data model (fields, types, required)
- Validation rules
- Normal behaviour flow
- Edge cases
- Error handling (codes + categories)
- Retry / escalation rules
- Dependencies (confirmed)
- Observability (events, logs, metrics)
- Versioning notes

Rules:
- No ambiguity.
- No responsibility creep.
- Assume resilience/brand/access contracts already exist.

Output:
Markdown only.
