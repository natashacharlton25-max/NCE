# EmbeddingService Module

> **Purpose:** Generate vector embeddings from text
> **Type:** AI Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

EmbeddingService is the specialized handler for converting text into vector representations. Unlike AICaller which handles completion/chat generation, this module focuses strictly on the text-to-vector transformation required for semantic search and validation.

---

## Notes

### Role
- Generate vector embeddings from text
- Distinct from AICaller (optimized for embedding generation)
- Populate VectorStore for semantic search

### Owns
- Embedding generation
- Model selection for embeddings
- Embedding caching

### Provider Support

| Provider | Model | Dimensions | Use Case |
|----------|-------|------------|----------|
| OpenAI | text-embedding-3-small | 1536 | Primary. Fast, cheap, high accuracy. |
| OpenAI | text-embedding-3-large | 3072 | Complex semantic nuance (optional). |
| Local | Xenova/all-MiniLM-L6-v2 | 384 | Offline/Privacy-focused fallback. |

### Key Responsibilities
- **Generate Embeddings:** Accepts string or array of strings, returns vector arrays.
- **Chunking:** Automatically chunks long text inputs to fit model window limits (e.g., 8191 tokens for OpenAI).
- **Cost Optimization:** Checks CacheHandler before making API calls to avoid re-embedding identical text.
- **Batching:** Aggregates small requests into efficient batch API calls where possible.

### Data Flow

```
Caller (e.g., ContentAlignment)
     ↓
EmbeddingService.getEmbeddings(text[])
     ↓
1. Hash text → Check CacheHandler (Hit? Return cached vector)
     ↓
2. Tokenize & Chunk (via TokenManager)
     ↓
3. Call Provider API (via AICaller or direct axios)
     ↓
4. Return Vector[] + Usage Stats
```

### Dependencies (Systems)
- **APIKeyManager:** Retrieves credentials for embedding providers.
- **CacheHandler:** Stores generated vectors to prevent redundant costs. Key = hash(text + model).
- **TokenManager:** Used for pre-flight chunking calculations.

### Feeds Into
- **VectorStore:** The primary consumer of these vectors.
- **ContentAlignment:** For on-the-fly semantic similarity checks.

### Pre-Build Concerns
- **Dimension Mismatch:** Ensure VectorStore schema handles different dimension sizes (1536 vs 384) if switching models.
- **Rate Limits:** Embedding APIs have different rate limits (RPM) than Chat APIs. RateLimiter needs a specific "embedding" bucket.

---

*Spec version: 0.0.0*
*Created: 2026-01-21*
*Status: Placeholder*
