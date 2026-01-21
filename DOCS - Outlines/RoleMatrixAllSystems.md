# System Role Matrix

## Managers (Decide)

| System | Manager | Decides |
|--------|---------|---------|
| AI | AIManager | Which model, which provider, fallbacks |
| Brand | BrandManager | Which phase, which worker |
| Colour | ColourManager | Palette approach, accessibility level |

## Workers (Do)

| System | Worker | Does | Uses |
|--------|--------|------|------|
| AI | GeminiWorker | Calls Gemini API | API Manager |
| AI | OpenAIWorker | Calls OpenAI API | API Manager |
| Brand | BrandWorker | Fetches/saves brand | DB Manager |
| Colour | PaletteWorker | Generates palette | AI System |

## Functions (Transform)

| System | Function | Input → Output |
|--------|----------|----------------|
| Colour | ColourMixer | colours[] → blended colour |
| Colour | AccessibilityChecker | colour pair → WCAG result |
| Prompt | VariableResolver | template + vars → string |