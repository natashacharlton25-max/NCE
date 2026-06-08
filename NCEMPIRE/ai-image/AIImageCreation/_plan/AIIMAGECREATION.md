# AIImageCreation Module

> **Purpose:** Generate images using AI (DALL-E, Midjourney, Stable Diffusion)
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

AIImageCreation manages AI image generation. It handles prompt engineering for images, provider selection, style consistency, and post-processing. It's the creative director for AI-generated visuals.

```
Image brief → AIImageCreation.generate() → AI provider → Generated images
```

**Providers:**
- OpenAI DALL-E 3
- Midjourney (via API/bot)
- Stable Diffusion
- Adobe Firefly

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `generate(brief, options)` | Generate image from brief | brief, options | images |
| `buildPrompt(brief, style)` | Build AI image prompt | brief, style | prompt |
| `selectProvider(requirements)` | Choose best provider | requirements | provider |
| `applyStyle(prompt, styleId)` | Apply brand style to prompt | prompt, styleId | styledPrompt |
| `postProcess(image, adjustments)` | Post-process generated image | image, adjustments | processedImage |
| `generateVariations(imageId, count)` | Generate variations | imageId, count | variations |
| `upscale(imageId, factor)` | Upscale image | imageId, factor | upscaledImage |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `AIImageCreation.generateBrandImage` | Generate brand-consistent image | buildPrompt → applyStyle → selectProvider → generate → postProcess |
| `AIImageCreation.iterateDesign` | Iterate on design | generate → generateVariations |

---

## Detailed Function Specs

### generate(brief, options)

Generates images from a brief.

```javascript
// Input
{
  brief: {
    subject: "Modern office workspace",
    style: "minimalist photography",
    mood: "professional, bright, optimistic",
    elements: ["standing desk", "laptop", "plants", "natural light"],
    avoid: ["people", "faces", "cluttered"],
    colors: ["#1a73e8", "#ffffff", "natural tones"]
  },
  options: {
    provider: "dalle3",
    count: 4,
    size: "1024x1024",
    quality: "hd",
    brandId: "acme"
  }
}

// Process
1. buildPrompt(brief)
2. applyStyle(prompt, brandStyleId)
3. Send to provider
4. Post-process results

// Output
{
  success: true,
  images: [
    {
      id: "img_gen_001",
      url: "https://...",
      localPath: "/generated/img_gen_001.png",
      prompt: "A minimalist photograph of a modern office workspace...",
      provider: "dalle3",
      size: "1024x1024",
      cost: 0.04
    },
    // ... 3 more
  ],
  totalCost: 0.16,
  metadata: {
    provider: "dalle3",
    model: "dall-e-3",
    generated: "2026-01-17T14:30:00Z"
  }
}
```

### buildPrompt(brief, style)

Constructs optimized prompt for AI image generation.

```javascript
// Input
{
  brief: {
    subject: "Abstract representation of data flow",
    style: "3D render",
    mood: "futuristic, dynamic",
    colors: ["blue", "purple", "white"]
  },
  style: "tech-visualization"
}

// Process
1. Analyze brief
2. Apply prompt engineering best practices
3. Add style modifiers
4. Add technical specifications

// Output
{
  prompt: "A stunning 3D render of abstract data flow visualization, featuring dynamic streams of blue and purple light particles moving through a futuristic digital space, high-tech aesthetic, volumetric lighting, octane render, 8k resolution, clean minimalist background with subtle gradients, professional tech industry style",
  negativePrompt: "text, watermark, low quality, blurry, distorted, amateur",
  style: "tech-visualization",
  engineered: true
}
```

### applyStyle(prompt, styleId)

Applies brand visual style to prompt.

```javascript
// Input
{
  prompt: "Modern office workspace with natural light",
  styleId: "acme-visual-style"
}

// Process
1. Load brand visual style
2. Extract style keywords, color preferences
3. Append to prompt
4. Return enhanced prompt

// Output
{
  originalPrompt: "Modern office workspace with natural light",
  styledPrompt: "Modern office workspace with natural light, corporate professional photography style, primary blue accent color #1a73e8, clean minimalist aesthetic, bright and optimistic mood, premium quality, brand-consistent visual language",
  appliedStyle: "acme-visual-style",
  styleModifiers: [
    "corporate professional photography style",
    "primary blue accent color #1a73e8",
    "clean minimalist aesthetic"
  ]
}
```

### selectProvider(requirements)

Selects best AI image provider for the task.

```javascript
// Input
{
  requirements: {
    style: "photorealistic",
    quality: "high",
    speed: "normal",
    budget: 0.20,
    features: ["variations", "inpainting"]
  }
}

// Output
{
  provider: "dalle3",
  reason: "Best for photorealistic images within budget",
  alternatives: [
    { provider: "midjourney", reason: "Higher quality but slower" },
    { provider: "stable-diffusion", reason: "Cheaper but less photorealistic" }
  ],
  estimatedCost: 0.04,
  estimatedTime: 15000
}
```

---

## Provider Configuration

### DALL-E 3

```json
{
  "provider": "dalle3",
  "apiKeyRef": "OPENAI_API_KEY",
  "endpoint": "https://api.openai.com/v1/images/generations",
  "model": "dall-e-3",
  "capabilities": ["generate", "variations"],
  "sizes": ["1024x1024", "1792x1024", "1024x1792"],
  "quality": ["standard", "hd"],
  "pricing": {
    "standard_1024": 0.04,
    "hd_1024": 0.08
  }
}
```

### Stable Diffusion (API)

```json
{
  "provider": "stable-diffusion",
  "apiKeyRef": "STABILITY_API_KEY",
  "endpoint": "https://api.stability.ai/v1/generation",
  "capabilities": ["generate", "variations", "inpainting", "upscale"],
  "models": ["stable-diffusion-xl-1024-v1-0"],
  "pricing": {
    "per_image": 0.01
  }
}
```

---

## Image Style Profiles

```json
{
  "id": "acme-visual-style",
  "brandId": "acme",
  "photography": {
    "style": "corporate professional",
    "lighting": "bright, natural",
    "mood": "optimistic, innovative",
    "colorTones": "cool blues, clean whites"
  },
  "illustration": {
    "style": "modern minimalist",
    "lineWeight": "medium",
    "colorPalette": "brand colors + neutrals"
  },
  "3d": {
    "style": "clean tech visualization",
    "rendering": "high quality, octane",
    "materials": "glass, metal, soft plastics"
  },
  "promptModifiers": {
    "always": ["professional quality", "brand-appropriate"],
    "avoid": ["cluttered", "amateur", "low quality"]
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "AIImageCreation",
  "version": "1.0.0",
  "description": "Generate images using AI providers",
  "type": "pipeline",

  "functions": [
    {
      "name": "generate",
      "description": "Generate image from brief",
      "input": ["brief", "options"],
      "output": "images"
    },
    {
      "name": "buildPrompt",
      "description": "Build AI image prompt",
      "input": ["brief", "style"],
      "output": "prompt"
    },
    {
      "name": "selectProvider",
      "description": "Choose best provider",
      "input": ["requirements"],
      "output": "provider"
    },
    {
      "name": "applyStyle",
      "description": "Apply brand style to prompt",
      "input": ["prompt", "styleId"],
      "output": "styledPrompt"
    },
    {
      "name": "postProcess",
      "description": "Post-process generated image",
      "input": ["image", "adjustments"],
      "output": "processedImage"
    },
    {
      "name": "generateVariations",
      "description": "Generate variations",
      "input": ["imageId", "count"],
      "output": "variations"
    },
    {
      "name": "upscale",
      "description": "Upscale image",
      "input": ["imageId", "factor"],
      "output": "upscaledImage"
    }
  ],

  "steps": [
    {
      "name": "generateBrandImage",
      "description": "Generate brand-consistent image",
      "sequence": ["buildPrompt", "applyStyle", "selectProvider", "generate", "postProcess"]
    },
    {
      "name": "iterateDesign",
      "description": "Iterate on design",
      "sequence": ["generate", "generateVariations"]
    }
  ],

  "config": {
    "defaultProvider": "dalle3",
    "defaultSize": "1024x1024",
    "maxCostPerGeneration": 0.50,
    "saveGeneratedImages": true,
    "generatedPath": "/generated/images/"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| AICaller | Making API calls to providers |
| FileManager | Storing generated images |
| CostBudgetManager | Tracking generation costs |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Generating brand imagery |
| Content | Hero images, illustrations |
| SocialMediaPlanner | Social media visuals |

---

*Last updated: 2026-01-17*
