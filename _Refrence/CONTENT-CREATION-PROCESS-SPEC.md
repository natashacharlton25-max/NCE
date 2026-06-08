# Content Creation Process — Idea to Output

> **Purpose:** Define the full intention and flow of content creation: every stage from a raw idea to finished, multi-format output.
> **Scope:** Process-level spec (the journey). Module-level contracts live in their own specs.
> **Spine:** A single therapeutic message — the *five truths* — is established once and carried intact through every stage and every output format.

---

## The Intention (Why This Process Exists)

The goal is to take a raw idea and turn it into a coherent, therapeutically sound, on-brand resource — without losing the message, the voice, or the quality along the way, and without doing it by hand each time.

Three things must survive the whole journey:

1. **Message continuity** — what the piece is *really* saying (the five truths) must be the same in the workbook, the social post, and the SEO blurb.
2. **Voice authenticity** — the Natasha perspective shapes *how* concepts are approached, not just what is said.
3. **Quality + safety** — accessibility, plain language, accurate therapeutic framing, and appropriate safeguarding throughout.

Everything below is in service of those three surviving from idea to output.

---

## Engine Legend

Each stage runs on one of two engines. This matters for cost and speed.

- **[LOCAL]** — runs on your machine via Python/JS. No token cost. The bulk of the pipeline.
- **[API]** — calls a model. Only **three** stages need the API on the happy path: **Theme & Truths**, **Structured Outline**, and **Content Generation**. Everything else (QA, humanisation, formatting, assembly, archive) is local. The API is touched again only when a QA fail triggers a targeted regeneration.

This is the cost reality: ~3 API stages per piece, plus occasional redo-on-fail.

---

## The Flow at a Glance

```
  IDEA
   │
   ▼
[0] Trigger ─────────────── topic + brand + content type + target outcome
   │
   ▼
[1] Pre-Generation Intel ── [LOCAL] voice samples, gaps, questions (quiet-time, optional)
   │
   ▼
[2] Context Assembly ─────── [LOCAL] brand data + rules + cognitive level + perspective
   │
   ▼
[3] Theme & Truths ───────── [API]  WHAT it says  ← the five truths are born here
   │
   ▼
[4] Outline ──────────────── [API]  HOW it's shaped (high-level structure)
   │
   ▼
[5] Structured Outline ───── [API]  per-section components + word counts + cognitive levels
   │
   ▼
[6] Content Generation ───── [API]  the writing loop (context carried section→section)
   │
   ▼
[7] JSON Assembly ────────── [LOCAL] one master content object
   │
   ▼
[8] Quality Assurance ────── [LOCAL] grammar, readability, humanise, sentiment, safety
   │                                  └─ fail? → targeted [API] regen, then re-check
   ▼
[9] Fit & Overflow ───────── [LOCAL] format calculator: shrink, or send section back to AI
   │
   ▼
[10] Format Output ───────── [LOCAL] HTML · DOCX · PPTX · PDF renderers
   │
   ▼
[11] SEO & Images ────────── [LOCAL] keywords, image search/placement, attribution
   │
   ▼
[12] Repurposing ─────────── social, newsletter, etc. (derived from the same truths)
   │
   ▼
[13] Final Assembly ──────── [LOCAL] front matter + body + back matter
   │
   ▼
[14] Summary & Archive ───── [LOCAL] summary, audit JSON, version, cost log
   │
   ▼
[15] Human Review ────────── sign-off + analytics/feedback loop
   │
   ▼
  OUTPUT
```

---

## Stage Detail

### [0] Trigger — the idea
**Intention:** Fix the four things every piece needs before anything else can happen.
**Input:** A topic (or batch manifest entry).
**Steps:**
1. Capture the topic.
2. Bind a **brand** (MTB / BYLW / WWAS / Frequency).
3. Choose a **content type** (workbook / guide / worksheet / resource).
4. Name the **target outcome** — the transformation this piece is meant to facilitate (links to the Outcomes Library).
**Output:** A job seed `{ topic, brand_id, content_type, outcome_id }`.
**Handoff:** → Context Assembly.

---

### [1] Pre-Generation Intelligence — *[LOCAL], optional, quiet-time*
**Intention:** Gather raw material so generation is grounded, not generic. Run overnight/off-peak.
**Steps:** voice-sample scraping from own sites, existing-doc text extraction, content-gap analysis, question mining (what people actually ask).
**Output:** An intel pack cached against the topic.
**Note:** Optional. A piece can run without it; it just makes the output sharper.

---

### [2] Context Assembly — *[LOCAL]*
**Intention:** Build the single context bundle every API stage will read from. This is where the *intelligent context injection* happens — layered prompts, not a 100KB dump.
**Steps:**
1. Load **brand data** (voice, audience, visual tokens, therapeutic approach).
2. Load **global rules** (Australian English, accessibility/WCAG, plain language, active voice, disclaimers, prohibited terms, word limits).
3. Load **content rules** (framework applied, structure type, exercises/prompts per section, crisis resources flag).
4. Resolve **cognitive level** (Academic → Crisis-accessible) and temperature settings.
5. Inject the **Natasha perspective** for the target outcome (from the Outcomes Library — the canonical source).
6. Confirm **model routing** (which model handles which downstream stage — config-driven).
**Output:** A compact, layered context bundle (~4–6KB, not 100KB+).
**Handoff:** → Theme & Truths.

---

### [3] Theme & Truths — *[API] · WHAT*
**Intention:** Decide what the piece is *really* about and establish the **five truths** — the message spine that every later stage and every output format must stay loyal to.
**Steps:**
1. Generate the theme: title, subtitle, core metaphor, visual motif.
2. Define the emotional journey: start state → end state → transformation arc.
3. Generate the five truths: each with statement, therapeutic basis, and the reframe (from → to).
4. Identify the central truth and truth hierarchy.
5. Validate against the framework; checkpoint save.
**Output:** `theme` + `truths` JSON.
**Why it's first on the API:** everything downstream references these. Get them wrong and the whole piece drifts.

---

### [4] Outline — *[API] · HOW*
**Intention:** Shape the message into a structure — the skeleton.
**Steps:** generate title, introduction (hook/context/what-to-expect/how-to-use), the ordered sections (each tagged with the truths it carries and an estimated length), and the conclusion approach. Checkpoint save.
**Output:** `outline` JSON.
**Handoff:** → Structured Outline.

---

### [5] Structured Section Outline — *[API]*
**Intention:** Turn each section heading into a buildable brief so the writing stage has no guesswork.
**Steps:** for each section, define components (intro paragraph, theory explanation, real-world example, exercises, reflection prompts, key-points summary, transition), assign word counts, and map the cognitive level per component.
**Output:** `sections_detailed` JSON.
**Handoff:** → Content Generation.

---

### [6] Content Generation — *[API] · the main loop*
**Intention:** Write the actual content, section by section, keeping the thread.
**Steps:**
1. For each section, build a generation request carrying: brand context, global + content rules, the relevant truths, the section structure, **a summary of the previous section**, and **a preview of the next**.
2. Generate the section.
3. Checkpoint after each section (so a fail resumes mid-piece, not from scratch).
**Output:** `section_content` for every section.
**This is the heaviest stage** — the bulk of tokens and time. Everything else is cheap by comparison.

---

### [7] JSON Assembly — *[LOCAL]*
**Intention:** Collapse all the pieces into one master content object that renderers and QA can read.
**Output:** the master document JSON (theme + truths + outline + all section content + metadata).

---

### [8] Quality Assurance — *[LOCAL], with conditional [API] redo*
**Intention:** Catch and fix everything mechanical before formatting. No API needed unless a check fails badly enough to need a rewrite.
**Checks (all local Python):**
- Spelling & grammar (Australian English).
- Readability (Flesch-Kincaid / grade level / reading time).
- Humanisation (remove AI tells, vary sentence rhythm, kill repetition).
- Sentiment / emotional-arc consistency.
- **Safety scan** (trigger words, required disclaimers present, crisis resources present).
**Fail path:** if a section can't be fixed locally → targeted **[API]** regeneration of *just that section* (with full context), then re-run the checks.
**Output:** QA-passed content + a QA report.

---

### [9] Fit & Overflow Calculator — *[LOCAL]*
**Intention:** Make the content physically fit the chosen format before rendering.
**Steps:**
1. Apply max char/space rules per format slot.
2. If content overflows: first try mechanical fixes (font/spacing adjustment within brand limits).
3. If still over: send that section back to AI with section context to tighten the copy.
**Output:** fit-checked content ready to render.

---

### [10] Format Output Generation — *[LOCAL]*
**Intention:** Render the same content into every required format, all reading from the one master object so they can't diverge.
**Renderers:** HTML · DOCX · PPTX · PDF (brand tokens + templates applied).
**Output:** rendered files per format.

---

### [11] SEO & Image Integration — *[LOCAL]*
**Intention:** Make it findable and visual.
**Steps:** generate keywords + metadata; derive image search keywords; search/select images; place them; record attribution.
**Output:** SEO metadata + placed, attributed images.

---

### [12] Repurposing — *derived from the same truths*
**Intention:** Spin off variants (social posts, newsletter, etc.) **from the five truths**, so every channel says the same thing in its own format.
**Output:** channel-specific variants.

---

### [13] Final Assembly — *[LOCAL]*
**Intention:** Combine front matter, body, and back matter (disclaimers, resources, about) into the finished deliverables.
**Output:** complete, distributable files.

---

### [14] Summary & Archive — *[LOCAL]*
**Intention:** Leave a complete trail.
**Steps:** generate an executive summary, write the full audit JSON (every stage output preserved), apply the naming convention, version it, and log per-stage cost (model, tokens, calls, total).
**Naming:** `{brand}_{content_type}_{title_slug}_{version}_{date}.{ext}`
**Output:** archived job + cost record.

---

### [15] Human Review & Feedback
**Intention:** Keep a human in the loop where it matters, and learn from performance.
**Steps:** review flagged items, therapeutic-accuracy and brand sign-off, final approval, then wire up analytics so performance feeds back into future generation.
**Output:** approved, live content + a feedback signal.

---

## Cross-Cutting Concerns (Every Stage)

- **Checkpoints:** each stage saves its JSON; a fail resumes from the last good checkpoint.
- **Retry:** transient fails retry with backoff; permanent fails go to the human queue.
- **Cost tracking:** every API stage emits token/cost events to the cost log.
- **Versioning:** every stage output is stored; any stage is a rollback point.

---

## The Three Stages, Plainly

If you strip it to its essence, the whole thing is three creative moves plus plumbing:

1. **WHAT** — Theme & Truths *(what we're saying)*
2. **HOW** — Outline + Structured Outline *(how it's shaped)*
3. **FULL** — Content Generation *(the actual writing)*

Everything else — assembly, QA, fitting, rendering, SEO, repurposing, archive — is local plumbing that takes those three creative outputs and turns them into finished, multi-format deliverables without ever letting the five truths drift.

---

*Created: 2026-06-02 · Status: Process spec (consolidated)*
