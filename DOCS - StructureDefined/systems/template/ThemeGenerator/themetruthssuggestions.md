These are high-level architectural decisions that will define the "intelligence" of your content engine. Below is an analysis of each point based on the goal of creating a consistent, scalable brand voice.1. Five Truths vs. Content Angles: Redundancy?Recommendation: Keep Both (Angle = The Hook; Truths = The Pillars)Rationale: An Angle is a marketing decision (e.g., "The Counter-Intuitive Approach to X"). Truths are factual/philosophical anchors (e.g., "Fact 1: X is caused by Y").The Relationship: The Angle dictates which Truths are prioritized. A "Beginner's Angle" might highlight Truths 1 and 2, while a "Masterclass Angle" highlights 4 and 5.Decision: Keep both. Allow the PromptBuilder to use the Angle to set the tone and the Truths to provide the substance.2. Why Exactly 5 Truths?Recommendation: Minimum 3, Target 5, Max 7Rationale: The "Rule of Three" is great for brevity, but "Seven" is the limit of human working memory.The "Padding" Risk: Forcing 5 truths on a simple topic (e.g., "How to drink water") leads to fluff. Restricting a complex topic (e.g., "Quantum Computing") to 5 leads to gaps.Decision: Implement a 3-7 range. This allows the AI to be "as brief as possible, but as deep as necessary."3. Where Do Truths Live?Recommendation: Hybrid (Theme-bound Master Truths + Outline-bound Subsets)Rationale: If you put truths only on the Outline, you lose the ability to maintain a consistent "Brand Knowledge Base." If you put them only on the Theme, every document from that theme starts to sound identical.Decision: 1.  Theme contains the "Master Truths" (the full knowledge pool).2.  Outline selects a subset (e.g., "This specific workbook will use Truths #1, #3, and #5").3.  Outline can add 1-2 "Contextual Truths" specific to that output (e.g., specific to a platform like LinkedIn).4. Truth Relationships/ProgressionRecommendation: Add sequence_type to the Theme metadata.Why it matters: The TemplateEngine needs to know if it's building a story or a list.Types to implement:Progressive: Section 2 requires the reader to understand the Truth in Section 1. (Best for Workbooks/Courses).Complementary: Truths can be read in any order. (Best for Social Media/Blog posts).Hierarchical: Foundation → Advanced.5. Cache TTL for ThemesRecommendation: 24 Hours (minimum) to Permanent.Rationale: Unlike "Trending Topics," a Brand Theme with its core Truths is an asset.Decision: Set default to 24 hours. However, add a is_archived or is_permanent flag in the SQLite database. If a user "Stars" or "Approves" a theme, it should essentially live forever (or until the Brand Profile is updated).6. PromptBuilder Truth InstructionRecommendation: Approved. Use "Truth Injection" blocks.Implementation: Your proposed instruction is perfect. To make it even stronger for the LLM, use a delimited block:Plaintext### MANDATORY KNOWLEDGE TO INCLUDE:
- TRUTH #1: [String]
- TRUTH #3: [String]

### INSTRUCTION:
Weave the above truths into the content naturally. 
Do not explicitly state "Truth #1 is..."; instead, ensure the 
underlying principle of Truth #1 informs the advice given.
Summary of System ImpactFeatureChangeImpactTruth Count5 → 3-7Higher quality, less AI "hallucination" padding.Truth StorageTheme → HybridBetter cross-document continuity.Truth TypeAdded sequence_typeEnables the AI to write better "Narrative Arcs."Cache1h → 24hSignificant reduction in API costs/latency.
This is exactly the right moment to pause and lock the **foundational choices**. You’re no longer debating features — you’re defining the philosophy of the system. I’ll give you clear, opinionated recommendations with rationale, trade-offs, and the downstream impact on your architecture.

I’ll be decisive where it matters.

---

## 1. Five Truths vs. Angles — Redundancy or Power Pair?

### Recommendation: **Keep both. Distinct roles. No redundancy.**

**Angle ≠ Truth.** They operate at different cognitive levels.

* **Truths** = enduring principles
  Stable, reusable, brand-defining. They survive formats, platforms, and time.
* **Angle** = situational expression
  The *lens* used to surface one or more truths for a specific context.

**Clean mental model**

| Layer   | Purpose                               | Volatility |
| ------- | ------------------------------------- | ---------- |
| Truths  | What the brand fundamentally believes | Very low   |
| Angle   | How that belief is framed *this time* | Medium     |
| Outline | How it unfolds structurally           | High       |

**Design rule I’d lock in:**

* Angles **must reference 1–2 Truth IDs**
* Angles are *derived from* truths, not independent concepts

This avoids drift while keeping creative flexibility.

✅ You retain clarity
✅ You avoid duplication
✅ You gain traceability (“this post expresses Truth 3”)

**Do not remove angles.** That would force truths to do too much work and reduce expressive range.

---

## 2. Why Exactly Five Truths?

### Recommendation: **Minimum 3 · Target 5 · Maximum 7**

This is the most mature choice — and the one used in high-quality knowledge systems.

**Why this wins:**

* Cognitive science supports 5±2
* Prevents filler content
* Allows depth when the topic demands it
* Keeps validation rules sane

**System rule (important):**

* UI, documentation, and defaults always *present 5*
* Validation allows 3–7
* Warnings (not errors) outside 5

This preserves the *feel* of Five Truths while staying honest.

---

## 3. Where Truths Live (This Is a Big One)

### Recommendation: **Hybrid model**

This gives you continuity *and* adaptability without fragmentation.

### Structure:

**Theme**

* Owns **Master Truths** (canonical, stable)

**Outline**

* Selects:

  * subset of master truths **(required)**
  * optionally adds **local truths** (max 1–2)

```
Theme
 └── masterTruths[3–7]

Outline
 ├── truthRefs: [truth_1, truth_3, truth_5]
 └── localTruths?: [optional]
```

**Rules to enforce:**

* Outline must reference ≥1 master truth
* Local truths must declare:

  * `extendsTruthId?` OR
  * `isContextSpecific: true`

This prevents truth sprawl and preserves brand continuity.

---

## 4. Truth Relationships & Progression

### Recommendation: **Yes — but lightweight**

Add **`relationshipType` at the outline level**, not globally on the truth.

Why? Because the same truths can behave differently depending on context.

**Allowed values:**

* `progressive`
* `complementary`
* `hierarchical`
* `standalone` (default)

This keeps the truths themselves pure, while letting the outline define narrative flow.

Elegant. Flexible. Safe.

---

## 5. Cache TTL for Themes

### Recommendation: **24–72 hours minimum**

Truths are **brand assets**, not volatile data.

**Suggested TTLs:**

* Theme (with truths): **48h**
* Outline templates: **6–12h**
* Usage stats: **no cache / short-lived**

You’ll reduce AI calls, improve performance, and avoid subtle inconsistency.

---

## 6. PromptBuilder — Truth Instruction (Yes, Absolutely)

This is a **must-have**, not a nice-to-have.

Your proposed instruction is strong. I’d formalise it slightly:

> “This content must clearly express Truth #X and Truth #Y.
> Other truths may only be referenced if required for coherence.
> Do not contradict or dilute any defined truth.”

Why this matters:

* Prevents truth leakage
* Enables partial truth expression
* Supports multi-part series and campaigns

This turns Truths into **hard constraints**, not inspiration fluff.

---

## Final Executive Summary (Decisions to Lock)

| Decision            | Recommendation                              |
| ------------------- | ------------------------------------------- |
| Truths vs Angles    | **Keep both; angles derive from truths**    |
| Truth count         | **Min 3 · Target 5 · Max 7**                |
| Truth ownership     | **Hybrid: theme-bound + outline selection** |
| Truth relationships | **Outline-level sequencing only**           |
| Theme cache TTL     | **48h+**                                    |
| Prompt enforcement  | **Explicit truth inclusion rules**          |

---
Excellent breakdown. Let me work through these decisions systematically with recommendations:

## 1. Five Truths vs. Content Angles - KEEP BOTH (with clear distinction)

**Recommendation**: Keep both with this relationship:

```
Angle = The LENS through which you view the theme
Truths = The MESSAGES you communicate through that lens
```

**Example**:
- **Theme**: "Sustainable Self-Care for Busy Parents"
- **Angle**: "Self-care as radical resistance to hustle culture"
- **Truths**: 
  1. Rest is productive
  2. Small moments count
  3. Imperfect consistency beats perfect intentions
  4. You can't pour from an empty cup
  5. Self-care modeling teaches children

**Why This Works**:
- Angle gives content its unique POV/personality
- Truths ensure message consistency across multiple pieces using that angle
- One angle can express multiple truths
- Different angles on same theme can share the same truths

**Implementation**:
```javascript
content_angles: [
  {
    angle_id: "MTB_SC_001_A1",
    statement: "Self-care as radical resistance to hustle culture",
    truth_connections: ["T1", "T2", "T3"], // which truths this angle naturally expresses
    tone: "empowering, slightly rebellious"
  }
]
```

## 2. Truth Count - MINIMUM 3, TARGET 5, MAX 7

**Recommendation**: Flexible range with quality gates

**Validation Logic**:
```javascript
if (truths.length < 3) {
  error: "TRUTHS_INSUFFICIENT_COVERAGE"
}
if (truths.length > 7) {
  warning: "TRUTHS_EXCESSIVE_COMPLEXITY" 
  // Allow but flag for review
}
if (truths.length === 5) {
  // Ideal state, no warnings
}
```

**Quality Over Quantity Rule**:
- 3 strong, distinct truths > 5 truths with padding
- Complex themes can justify 6-7
- Simple themes might only need 3-4

**Generator Instruction**:
"Generate 3-7 core truths. Target 5 if theme complexity supports it. Prioritize quality and distinctness over meeting quota."

## 3. Where Truths Live - HYBRID APPROACH

**Recommendation**: Theme has "master truths"; Outline selects subset + optional custom

**Structure**:
```javascript
theme: {
  master_truths: [
    // 5-7 truths that comprehensively cover theme
  ]
}

outline: {
  selected_truths: ["T1", "T3", "T5"], // subset from master
  custom_truths: [
    // Optional 1-2 outline-specific truths
    // Use case: specific audience segment needs unique angle
  ],
  primary_truth: "T1", // main focus for this outline
  secondary_truths: ["T3", "T5"] // supporting messages
}
```

**Why Hybrid**:
- **Continuity**: Master truths ensure campaign coherence
- **Flexibility**: Outlines can emphasize different truths for different audiences/purposes
- **Specificity**: Custom truths allow drilling deeper on one aspect
- **Efficiency**: Don't regenerate truths every time

**Use Cases**:
- **Email series**: Selects T1, T2, T4 (beginner-friendly truths)
- **Advanced workshop**: Selects T4, T5, T6 + custom truth for deep dive
- **Social campaign**: All master truths distributed across 15 posts

## 4. Truth Relationships - YES, ADD `sequence_type`

**Recommendation**: Add relationship metadata with validation

**Schema**:
```javascript
theme: {
  master_truths: [...],
  truth_relationships: {
    sequence_type: "progressive" | "complementary" | "hierarchical" | "cyclical",
    relationship_map: {
      "T1": { builds_on: null, supports: ["T2"], conflicts_with: [] },
      "T2": { builds_on: ["T1"], supports: ["T3", "T4"], conflicts_with: [] },
      "T3": { builds_on: ["T1", "T2"], supports: ["T5"], conflicts_with: [] },
      // etc.
    },
    recommended_flow: ["T1", "T2", "T4", "T3", "T5"] // optimal teaching sequence
  }
}
```

**Sequence Types Defined**:
- **Progressive**: Each truth builds on previous (1→2→3→4→5 linear flow)
  - Use: Educational content, skill-building
- **Complementary**: Truths are different facets, no required order
  - Use: Awareness content, perspective-shifting
- **Hierarchical**: Foundation → Advanced
  - Use: Framework-teaching, methodology
- **Cyclical**: Truths reinforce each other in any combination
  - Use: Mindset content, principles-based messaging

**Validation**:
- `progressive` and `hierarchical` must have valid dependency chains
- `complementary` truths should have minimal overlap
- `relationship_map` must be internally consistent

## 5. Cache TTL - INCREASE TO 7 DAYS

**Recommendation**: 7 days (168 hours) with conditions

**Rationale**:
- Themes are strategic assets, not tactical responses
- Truths represent brand positioning, which is stable
- 7 days allows weekly theme rotation while avoiding stale content

**Cache Strategy**:
```javascript
cache: {
  ttl: 604800, // 7 days in seconds
  invalidate_on: [
    "brand_voice_update",
    "messaging_pillar_change", 
    "manual_theme_refresh"
  ],
  refresh_ahead: true, // Regenerate at 6 days to avoid cache miss
  stale_while_revalidate: 86400 // Serve stale for 1 day while regenerating
}
```

**Override Options**:
- Seasonal/timely themes: 24-48 hours
- Evergreen brand themes: 30 days
- Campaign-specific themes: Campaign duration

## 6. PromptBuilder Truth Instructions - YES, ADD EXPLICIT TEMPLATES

**Recommendation**: Add instruction templates with emphasis levels

**Template Structure**:
```javascript
truth_instructions: {
  primary_emphasis: `
    Your PRIMARY focus is Truth #{primary_truth_number}: "{truth_statement}"
    
    This truth should be:
    - The main message/takeaway
    - Reinforced 2-3 times throughout the content
    - Present in opening hook OR closing CTA
    
    Application guidance: {truth.application_notes}
  `,
  
  secondary_support: `
    SUPPORT your primary message with Truth #{truth_number}: "{truth_statement}"
    
    This truth should:
    - Appear naturally 1-2 times
    - Strengthen/complement the primary truth
    - Not overshadow the main message
  `,
  
  background_context: `
    You may reference Truth #{truth_number}: "{truth_statement}" if contextually relevant, 
    but it's not required for this piece.
  `,
  
  explicit_exclusion: `
    DO NOT reference Truths #{excluded_truth_numbers}. They are not relevant to this content piece.
  `
}
```

**Outline-Level Truth Assignment**:
```javascript
outline: {
  truth_strategy: {
    primary: "T2",
    secondary: ["T1", "T4"],
    background: ["T3"],
    excluded: ["T5"] // Explicitly reserved for different content
  }
}
```

**PromptBuilder Usage**:
```javascript
// PromptBuilder constructs:
const prompt = `
${truth_instructions.primary_emphasis} // Always include
${truth_instructions.secondary_support} // For each secondary
${truth_instructions.explicit_exclusion} // If truths excluded

Now write the content...
`;
```

---

These are *governance-level* questions. The answers define how much authority you give the system versus the human — and how safe, scalable, and “on-brand” this becomes long-term. I’ll be crisp, opinionated, and practical.

---

## 1. Angle Generation — AI or Human?

### Recommendation: **Hybrid, with human-locked defaults**

**Angles should be AI-generated by default, but human-lockable.**

Why this works:

* Angles are **contextual and expressive**, not doctrinal
* AI is excellent at reframing truths for:

  * platform
  * format
  * moment
* Humans should intervene only when:

  * a campaign demands precision
  * legal/reputation risk exists
  * a brand wants a signature hook

### Practical model:

* `angle.source: "ai" | "human"`
* `angle.locked: boolean`

**Rule of engagement:**

* If `locked = true`, AI may *adapt language* but not intent
* If `locked = false`, AI can regenerate per outline/platform

This gives you speed *and* control — no bottlenecks, no drift.

---

## 2. Truth Approval Workflow

### Recommendation: **Tiered trust model (brand-configurable)**

Not all brands need the same safety rails.

### Suggested levels:

| Mode        | Behaviour                                                     |
| ----------- | ------------------------------------------------------------- |
| **Strict**  | All truths require human approval before activation           |
| **Guided**  | AI-generated truths auto-approve but flagged for later review |
| **Trusted** | Auto-approve truths for whitelisted brands/themes             |

### Implementation hint:

* Truth-level fields:

  * `status: draft | active | flagged`
  * `reviewRequired: boolean`
* Brand config:

  * `truthApprovalMode`

This lets:

* Established brands move fast
* Sensitive brands stay protected
* You avoid one-size-fits-all friction

---

## 3. Cross-Brand Truth Reuse (This Is Subtle)

### Recommendation: **Yes, but only at a universal layer**

Truths *can* transcend brands — **but never by default**.

### Clean separation:

#### Universal Truths

* Human-authored
* Brand-agnostic
* Tagged as:

  * `scope.brand = universal`
* Example:

  > “Avoiding difficult conversations creates larger problems later.”

#### Brand Truths

* Adapted from universal OR created fresh
* Carry brand voice, values, stance
* Example:

  * Mind the Box → structured, reflective framing
  * Be You Love Wins → emotional, expressive framing

### Rule to enforce:

* Brand-specific truths may **reference** universal truths
* They may **not inherit blindly**

This protects brand identity while enabling reuse intelligently.

---

## 4. Truth Evolution & Performance Feedback

### Recommendation: **Automatic flagging, manual refinement**

Let the system *observe*, not *rewrite doctrine*.

### What should happen automatically:

* Monitor:

  * engagement
  * usage frequency
  * drop-off points
* Trigger:

  * `truth.status = flagged`
  * attach performance notes

### What should *not* happen:

* No automatic truth mutation
* No silent rewrites

### Why:

Truths are **beliefs**, not headlines.
They require judgment, not optimisation loops.

### Best-in-class behaviour:

* System says: *“This truth may no longer resonate.”*
* Human decides: refine, reframe, archive, or double down

That’s authority, not automation.

---

## Final Governance Recommendations (Lock These)

| Question          | Decision                           |
| ----------------- | ---------------------------------- |
| Angle generation  | **AI by default, human-lockable**  |
| Truth approval    | **Brand-configurable trust tiers** |
| Cross-brand reuse | **Universal → Brand-adapted only** |
| Truth evolution   | **Auto-flag, human-refine**        |

---

These four questions touch on the "Human-in-the-Loop" (HITL) strategy for your platform. Effectively balancing AI autonomy with human oversight is what separates a "spam generator" from a "brand authority engine."Here are my recommendations for each design decision:1. Angle Generation: AI-Generated or Human-Defined?Recommendation: AI-Generated (based on Human-Defined "Styles")If you force humans to define every angle, you create a bottleneck in the scaling process. However, if the AI is left totally unguided, it will default to generic perspectives.The Strategy: Use Angle Blueprints. In your repository, store a set of high-performing "Angle Templates" (e.g., The Myth-Buster, The Beginner’s Guide, The Expert Deep-Dive).The Execution: When ThemeGenerator runs, the user picks a "Style," and the AI generates a specific Angle based on that style + the Topic + the Brand Voice.Verdict: Let the human define the type of angle; let the AI write the specific implementation for that theme.2. Truth Approval Workflow: Auto-Approval vs. Manual ReviewRecommendation: Tiered Approval (Status-Based)Not all brands carry the same level of risk. A personal blog needs less oversight than a regulated financial or health brand.The Logic: Implement a Brand.trustLevel or Theme.status field.Draft (Auto): AI generates truths; content generation is blocked until a human clicks "Approve." (Default for new brands).Verified (Bypass): For established brands/themes, allow a "Fast-Track" flag where content can be generated immediately, but the document is watermarked as "AI-Draft: Review Truths."Verdict: Require Manual Review for the Theme (the truths), but then Auto-Approve the Outlines and content that follow. This ensures the "Knowledge" is solid before the "Execution" begins.3. Cross-Brand Truth ReuseRecommendation: Brand-Specific by Default, "Universal" by ExceptionWhile Mind the Box and Be You Love Wins might share philosophical DNA (like "Growth Mindset"), their expression of those truths will differ to maintain distinct brand identities.The Strategy: Use a Truth Library (Universal Repo).If a truth is universal, store it in a universal_truths.db.When generating a theme for Mind the Box, the ThemeGenerator can query the universal library and "Brand-ify" the truth (e.g., changing the tone or terminology).Verdict: Always store them as brand-specific entries in the SQLite DB to ensure usageCount and rating metrics stay clean per brand, even if the underlying fact is the same.4. Truth Evolution: Flagging vs. Manual ReviewRecommendation: Automatic Flagging for Manual RefinementThe system should identify the "weakest link" in your knowledge base using the data you're already collecting.The Logic: Create a "Truth Performance Monitor".If a section generated using Truth #4 consistently receives low user ratings or high "Regenerate" requests, the system calculates a qualityScore for that truth.Action: When a truth falls below a threshold (e.g., 2.5/5 stars), set its status to needs_refinement and notify the admin.Verdict: Do not let the AI "auto-fix" truths without oversight, as it might accidentally remove a core brand principle to chase "engagement." Flag automatically; fix manually.Summary of DecisionsDecision AreaProposed PolicyWhy?Angle GenerationAI-GuidedScales better than manual, but stays on track via templates.Truth ApprovalManual at Theme-LevelEnsures the foundation is correct before building the house.Cross-Brand ReuseInherited / Brand-ifiedMaintains unique brand voice while saving time.Truth EvolutionFlag for ReviewPrevents AI from "hallucinating" brand changes to optimize metrics.
