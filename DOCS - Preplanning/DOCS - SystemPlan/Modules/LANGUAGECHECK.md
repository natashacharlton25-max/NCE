# LanguageCheck Module

> **Purpose:** Grammar, spelling, and language quality checking (Python-based)
> **Type:** Service Module
> **Status:** Planning

---

## Overview

LanguageCheck uses Python libraries (LanguageTool, spaCy, etc.) to check grammar, spelling, and language quality. It runs as a Python service that other modules call. Used to validate AI-generated content before delivery.

```
Text content → LanguageCheck.check() → Issues found → Suggestions
```

**Capabilities:**
- Grammar checking
- Spelling checking
- Style suggestions
- Readability scoring
- Tone analysis
- Language detection

---

## Roles & Rules

### LanguageCheck DOES:
- Check grammar and spelling
- Analyze readability scores
- Analyze tone and sentiment
- Detect language
- Suggest corrections
- Support custom dictionaries

### LanguageCheck does NOT:
- Modify content directly (only suggests)
- Generate content (checks existing)
- Make content decisions (reports only)
- Store content (stateless analysis)

### Boundaries:
- Cannot auto-correct content (only suggests)
- Cannot access content outside requests
- Cannot make final approval decisions
- Python service runs separately

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `check(text, options)` | Full language check | text, options | checkResult |
| `checkGrammar(text)` | Grammar check only | text | grammarIssues |
| `checkSpelling(text, dictionary)` | Spelling check with custom dictionary | text, dictionary | spellingIssues |
| `analyzeReadability(text)` | Readability scores | text | readabilityScore |
| `analyzeTone(text)` | Tone/sentiment analysis | text | toneAnalysis |
| `detectLanguage(text)` | Detect language | text | language |
| `suggest(text, issues)` | Generate corrections | text, issues | suggestions |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `LanguageCheck.fullCheck` | Complete language analysis | check → analyzeReadability → analyzeTone |
| `LanguageCheck.quickCheck` | Fast grammar/spelling only | checkGrammar → checkSpelling |

---

## Detailed Function Specs

### check(text, options)

Full language check with all options.

```javascript
// Input
{
  text: "Their going to love this knew feature. Its really good.",
  options: {
    language: "en-GB",
    checkGrammar: true,
    checkSpelling: true,
    checkStyle: true,
    customDictionary: ["brandname", "acme"]
  }
}

// Output
{
  issues: [
    {
      type: "grammar",
      severity: "error",
      message: "Incorrect word: 'Their' should be 'They're'",
      offset: 0,
      length: 5,
      replacements: ["They're"],
      rule: "THEIR_VS_THEYRE"
    },
    {
      type: "spelling",
      severity: "error",
      message: "Misspelling: 'knew' should be 'new'",
      offset: 26,
      length: 4,
      replacements: ["new", "know"]
    },
    {
      type: "grammar",
      severity: "error",
      message: "Incorrect word: 'Its' should be 'It's'",
      offset: 40,
      length: 3,
      replacements: ["It's"]
    }
  ],
  stats: {
    totalIssues: 3,
    grammarIssues: 2,
    spellingIssues: 1,
    styleIssues: 0
  },
  correctedText: "They're going to love this new feature. It's really good."
}
```

### analyzeReadability(text)

Calculates readability scores.

```javascript
// Input
"Acme Corp delivers innovative technology solutions for modern businesses."

// Output
{
  scores: {
    fleschKincaid: 12.3,      // Grade level
    fleschReadingEase: 45.2,  // 0-100, higher = easier
    gunningFog: 14.1,         // Grade level
    smog: 11.8,               // Grade level
    colemanLiau: 13.2         // Grade level
  },
  interpretation: {
    level: "college",
    difficulty: "fairly difficult",
    recommendation: "Consider simplifying for broader audience"
  },
  stats: {
    words: 9,
    sentences: 1,
    syllables: 18,
    avgWordsPerSentence: 9,
    avgSyllablesPerWord: 2.0
  }
}
```

### analyzeTone(text)

Analyzes tone and sentiment.

```javascript
// Input
"We're thrilled to announce our revolutionary new platform!"

// Output
{
  sentiment: {
    score: 0.85,  // -1 to 1
    label: "positive"
  },
  tone: {
    primary: "enthusiastic",
    secondary: ["promotional", "confident"],
    formality: 0.4  // 0 = casual, 1 = formal
  },
  emotions: {
    joy: 0.7,
    surprise: 0.3,
    trust: 0.5
  }
}
```

---

## Python Service

LanguageCheck runs as a Python microservice:

```python
# language_check_service.py
from flask import Flask, request, jsonify
import language_tool_python
import spacy
from textstat import flesch_reading_ease

app = Flask(__name__)
tool = language_tool_python.LanguageTool('en-GB')
nlp = spacy.load('en_core_web_sm')

@app.route('/check', methods=['POST'])
def check():
    text = request.json['text']
    options = request.json.get('options', {})

    matches = tool.check(text)
    # Process and return results

@app.route('/readability', methods=['POST'])
def readability():
    text = request.json['text']
    # Calculate scores
```

---

## Custom Dictionaries

Brand-specific terms stored in repos:

```json
{
  "id": "acme-dictionary",
  "brandId": "acme",
  "terms": [
    { "word": "AcmeCloud", "type": "product" },
    { "word": "SmartSync", "type": "feature" },
    { "word": "Acme", "type": "brand" }
  ],
  "ignore": ["SaaS", "API", "SDK"]
}
```

---

## Config

### module.config.json

```json
{
  "module": "LanguageCheck",
  "version": "1.0.0",
  "description": "Grammar, spelling, and language quality checking",
  "type": "service",
  "runtime": "python",

  "functions": [
    {
      "name": "check",
      "description": "Full language check",
      "input": ["text", "options"],
      "output": "checkResult"
    },
    {
      "name": "checkGrammar",
      "description": "Grammar check only",
      "input": ["text"],
      "output": "grammarIssues"
    },
    {
      "name": "checkSpelling",
      "description": "Spelling check with custom dictionary",
      "input": ["text", "dictionary"],
      "output": "spellingIssues"
    },
    {
      "name": "analyzeReadability",
      "description": "Readability scores",
      "input": ["text"],
      "output": "readabilityScore"
    },
    {
      "name": "analyzeTone",
      "description": "Tone/sentiment analysis",
      "input": ["text"],
      "output": "toneAnalysis"
    },
    {
      "name": "detectLanguage",
      "description": "Detect language",
      "input": ["text"],
      "output": "language"
    },
    {
      "name": "suggest",
      "description": "Generate corrections",
      "input": ["text", "issues"],
      "output": "suggestions"
    }
  ],

  "steps": [
    {
      "name": "fullCheck",
      "description": "Complete language analysis",
      "sequence": ["check", "analyzeReadability", "analyzeTone"]
    },
    {
      "name": "quickCheck",
      "description": "Fast grammar/spelling only",
      "sequence": ["checkGrammar", "checkSpelling"]
    }
  ],

  "config": {
    "serviceUrl": "http://localhost:5001",
    "defaultLanguage": "en-GB",
    "maxTextLength": 50000
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Loading custom dictionaries |
| (Python service) | Actual checking logic |

---

## Used By

| Module | How |
|--------|-----|
| Content | Validating generated content |
| Email | Checking email copy |
| AICaller | Post-generation validation |

---

*Last updated: 2026-01-17*
