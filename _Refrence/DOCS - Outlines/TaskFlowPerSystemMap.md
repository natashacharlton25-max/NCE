# Task: generate-palette

## Flow
```
Task Manager assigns to → Colour System
                              ↓
                         ColourManager decides:
                         - Need brand data first
                         - Use AI for generation
                         - Check accessibility after
                              ↓
                         Routes to PaletteWorker
                              ↓
                         PaletteWorker:
                         1. Calls Brand System → get brand values
                         2. Calls AI System → generate colours
                         3. Calls AccessibilityChecker() → validate
                         4. Returns palette
```

## Cross-System Calls

| Step | From | To | For |
|------|------|----|-----|
| 1 | Colour | Brand | brand values |
| 2 | Colour | AI | generation |

## Functions Used

| Function | Purpose |
|----------|---------|
| AccessibilityChecker | WCAG validation |
| ColourMixer | Blend adjustments |