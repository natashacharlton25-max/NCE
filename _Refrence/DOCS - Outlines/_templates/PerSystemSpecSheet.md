# System: [Name]

## Purpose
[One sentence: what this system does]

## Boundary
- **Does:** [what it handles]
- **Does NOT:** [what other systems handle]

## Manager

| Manager | Decides |
|---------|---------|
| [Name]Manager | [What decisions it makes] |

## Workers

| Worker | Does | Calls |
|--------|------|-------|
| [Name]Worker | [What work it does] | [Cross-cutting services used] |

## Functions

| Function | Input | Output | Pure |
|----------|-------|--------|------|
| [name]() | [type] | [type] | Yes |

## Tasks Handled

| Task Type | Worker | Needs |
|-----------|--------|-------|
| generate-palette | PaletteWorker | brand_id |

## Dependencies

| Depends On | For |
|------------|-----|
| AI System | Making AI calls |
| Brand System | Getting brand data |

## Config
`/config/systems/[name].json`

## Database Tables
| Table | Purpose |
|-------|---------|
| | |
