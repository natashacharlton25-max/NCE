# AIImageCreation Module

> **Purpose:** Generate images using AI image models
> **Type:** Visual Design Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

AIImageCreation generates custom images using AI image generation models based on prompts and style guidance.

---

## Notes

### Provider Support

| Provider | Status | Use Case | Config |
|----------|--------|----------|--------|
| DALL-E 3 (OpenAI) | Primary | High-quality generation | openai SDK |
| Midjourney | Future | Artistic styles | API (when available) |
| Stable Diffusion | Supported | Local/self-hosted, variations | REST API |
| Leonardo.ai | Supported | Fast generation, fine-tuned models | REST API |
| Ideogram | Future | Text-in-image | REST API |

### Key Responsibilities
- **Generate images**: Execute image generation from text prompts
- **Apply brand styling**: Ensure generated images match brand visual identity
- **Upscale images**: Increase resolution for print/high-res needs
- **Create variations**: Generate alternative versions of existing images
- **Provider routing**: Route calls based on style requirements, cost, quality needs

### Does NOT Do
- Search stock images (ImageBrowser does that)
- Store/organize images (ImageCollections does that)
- Build image prompts (PromptBuilder does that)
- Composite/layer images (ImageRenderer does that)
- Create templates (ImageTemplate does that)
- Blur/redact sensitive content (separate module)
- Verify brand guidelines compliance (VisualCheck does that)

### Data Flow
```
PromptBuilder → image prompt
     ↓
AIImageCreation.generate(prompt, options)
     ↓
Provider API (DALL-E, etc.)
     ↓
Generated image (URL or binary)
     ↓
ImageCollections (optional: store to brand library)
```

### Depends On
- APIKeyManager - provider credentials (OpenAI, Leonardo, etc.)
- RateLimiter - enforce rate limits per provider
- FailureHandler - error recovery (generation failures, content policy)
- PromptBuilder - builds image prompts from context
- BrandManager - brand style data for applyBrandStyle()
- Logger - expensive/risky operations need logging

### Feeds Into (Called By)
- TemplateEngine - AI-generated image fields in sections
- ImageTemplate - dynamic image generation for templates
- LogoMaker - AI-assisted logo generation
- SocialMediaManager - on-demand social images

### Functions (from ROLE-MATRIX)

| Function | Input | Output |
|----------|-------|--------|
| `generate(prompt, options)` | prompt, options | generatedImage |
| `applyBrandStyle(image, brandId)` | image, brandId | styledImage |
| `upscale(image, scale)` | image, scale | upscaledImage |
| `variations(image, count)` | image, count | imageVariations |

### Options Type (Draft)

```javascript
{
  provider?: string,           // "dalle3" | "leonardo" | "stable-diffusion"
  model?: string,              // provider-specific model ID
  size?: string,               // "1024x1024" | "1792x1024" | "1024x1792"
  quality?: string,            // "standard" | "hd"
  style?: string,              // "vivid" | "natural" (DALL-E specific)
  brandId?: string,            // for brand style application
  negativePrompt?: string,     // what to avoid (Stable Diffusion)
  seed?: number,               // reproducibility
  n?: number                   // number of images (default: 1)
}
```

### Error Codes (to add to ERROR-CODES.md)
- `IMAGE_GENERATION_FAILED` - provider returned error
- `IMAGE_CONTENT_POLICY` - prompt violated content policy
- `IMAGE_RATE_LIMIT` - provider rate limit exceeded
- `IMAGE_QUOTA_EXCEEDED` - account quota exceeded
- `IMAGE_INVALID_SIZE` - requested size not supported
- `IMAGE_PROVIDER_TIMEOUT` - generation timed out
- `IMAGE_PARTIAL_FAILURE` - some images in batch failed

### Architecture Considerations
- **Async generation**: Some providers take 10-60 seconds; need polling/webhooks
- **Cost tracking**: Image generation is expensive; track per-brand costs
- **Content moderation**: Pre-filter prompts to avoid policy violations
- **Caching**: Cache identical prompt results to reduce costs
- **Brand consistency**: Store brand style presets for consistent generation

### Pre-Build Concerns

#### 1. Prompt Building - Interface Design
- Does AIImageCreation receive a full natural-language prompt from PromptBuilder?
- Or does it receive structured data (brand style + visual keywords + mood)?
- **Decision needed**: This affects the interface contract

#### 2. Brand Style Application - `applyBrandStyle()` Clarity
- Does it modify the prompt BEFORE sending to provider?
- Or does it post-process the generated image?
- Does it leverage ColourMix, FontManager, VisualBrand modules?
- Or is it self-contained?
- Where does brand style data come from—BrandManager directly, or through VisualBrand?

#### 3. Image Storage & Metadata Ownership
- Who owns the decision to store generated images? (ImageCollections? Writer?)
- What metadata gets persisted?
  - Generation params, timestamp, prompt, provider, cost
- Does AIImageCreation write to a database (image_generation.db)?
- Or pass raw image + metadata to ImageCollections?

#### 4. Cost Tracking
- Image generation costs are significant and variable by provider/size/quality
- CostTracker should own the formula
- AIImageCreation needs to emit structured cost data:
  - provider, model, tokens/credits consumed
- **Decision**: Separate cost event or bundled with response?

#### 5. Error Handling Nuance
- `IMAGE_CONTENT_POLICY`: Filter prompts before sending, or let provider reject and catch?
- Provider-side timeout vs client-side timeout—how to distinguish?
- Partial failures (1 of 5 images succeeded)—how does that flow to FailureHandler?

#### 6. Async Considerations
- If generation takes 10-60s, is this a synchronous blocking call?
- Or should it return a job ID + polling?
- How does this integrate with Orchestrator job queue/task management?

#### 7. Schema Validation
- Input validation schema for `generate()` options (Zod)
- Validate before sending to expensive API calls

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Updated: 2026-01-20*
*Status: Placeholder*
