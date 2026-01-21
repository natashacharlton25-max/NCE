# PythonRunner Module

> **Purpose:** Python script execution for data processing and ML tasks
> **Type:** Integration Module
> **Status:** Planning (Future)

---

## Overview

PythonRunner enables **Python script execution** within the system. It handles data processing, machine learning tasks, and complex computations that are better suited for Python.

```
Task → PythonRunner.execute() → Python script → Result
```

**PythonRunner enables:**
- Data analysis
- Image processing
- ML model inference
- Complex calculations
- Data transformations

---

## Roles & Rules

### PythonRunner DOES:
- Execute Python scripts
- Pass data to/from scripts
- Manage Python environment
- Handle script timeouts
- Capture output and errors

### PythonRunner does NOT:
- Install packages at runtime (pre-configured)
- Run untrusted scripts
- Access system resources directly
- Bypass security sandbox

### Boundaries:
- Sandboxed execution
- Pre-approved scripts only
- Resource limits enforced
- Timeout strictly enforced

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `execute(script, params)` | Run a Python script | script, params | result |
| `executeFunction(module, function, args)` | Call specific function | module, function, args | result |
| `listScripts()` | List available scripts | - | scripts |
| `getScriptInfo(scriptId)` | Get script metadata | scriptId | info |
| `validateScript(script)` | Validate script safety | script | valid |

---

## Detailed Function Specs

### execute(script, params)

Execute a Python script.

```javascript
// Input
{
  script: "data-analysis/sentiment-analysis",
  params: {
    text: "This product is amazing! Best purchase ever.",
    language: "en"
  },
  options: {
    timeout: 30000,
    memoryLimit: "512MB"
  }
}

// Output
{
  success: true,
  result: {
    sentiment: "positive",
    confidence: 0.95,
    scores: {
      positive: 0.95,
      neutral: 0.04,
      negative: 0.01
    }
  },
  execution: {
    duration: 1500,
    memoryUsed: "128MB"
  }
}
```

### executeFunction(module, function, args)

Call a specific function from a Python module.

```javascript
// Input
{
  module: "image_processing",
  function: "resize_image",
  args: {
    inputPath: "/temp/image.jpg",
    width: 800,
    height: 600,
    outputPath: "/temp/image-resized.jpg"
  }
}

// Output
{
  success: true,
  result: {
    outputPath: "/temp/image-resized.jpg",
    originalSize: { width: 1920, height: 1080 },
    newSize: { width: 800, height: 600 },
    fileSize: 125000
  }
}
```

### listScripts()

List available pre-approved scripts.

```javascript
// Output
{
  scripts: [
    {
      id: "sentiment-analysis",
      category: "data-analysis",
      description: "Analyze text sentiment",
      inputs: ["text", "language"],
      outputs: ["sentiment", "confidence"]
    },
    {
      id: "image-resize",
      category: "image-processing",
      description: "Resize images",
      inputs: ["inputPath", "width", "height"],
      outputs: ["outputPath"]
    },
    {
      id: "color-extraction",
      category: "image-processing",
      description: "Extract dominant colors from image",
      inputs: ["imagePath", "count"],
      outputs: ["colors"]
    }
  ]
}
```

---

## Pre-Approved Scripts

| Category | Scripts | Purpose |
|----------|---------|---------|
| `data-analysis` | sentiment, keyword-extraction | Text analysis |
| `image-processing` | resize, crop, color-extract | Image manipulation |
| `ml-inference` | classification, embedding | ML models |
| `data-transform` | csv-parse, json-transform | Data conversion |

---

## Security

```javascript
{
  security: {
    sandboxed: true,
    allowedPackages: ["numpy", "pandas", "pillow", "nltk"],
    blockedOperations: ["network", "subprocess", "file-system-write"],
    maxExecutionTime: 60000,
    maxMemory: "1GB"
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "PythonRunner",
  "version": "1.0.0",
  "description": "Python script execution",
  "type": "integration",

  "functions": [
    {
      "name": "execute",
      "description": "Run a Python script",
      "input": ["script", "params"],
      "output": "result"
    },
    {
      "name": "executeFunction",
      "description": "Call specific function",
      "input": ["module", "function", "args"],
      "output": "result"
    },
    {
      "name": "listScripts",
      "description": "List available scripts",
      "input": [],
      "output": "scripts"
    },
    {
      "name": "getScriptInfo",
      "description": "Get script metadata",
      "input": ["scriptId"],
      "output": "info"
    },
    {
      "name": "validateScript",
      "description": "Validate script safety",
      "input": ["script"],
      "output": "valid"
    }
  ],

  "config": {
    "scriptsPath": "/repos/python-scripts/",
    "pythonPath": "/usr/bin/python3",
    "defaultTimeout": 30000,
    "maxMemory": "512MB",
    "sandboxEnabled": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Script and data access |
| Logger | Logging execution |

---

## Used By

| Component | How |
|-----------|-----|
| LanguageCheck | Grammar analysis |
| ImageCollections | Image processing |
| AnalyticsHandler | Data analysis |

---

*Last updated: 2026-01-17*
