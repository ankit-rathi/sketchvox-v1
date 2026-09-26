# SketchVox — DeepSeek Master Context & Engineering Contract

**Document purpose:** This is the single compact context document to give DeepSeek when working on SketchVox.  
**Current project state:** v6.2.1 candidate  
**Audience:** DeepSeek, other coding agents, and fresh ChatGPT sessions  
**Source of truth:** The actual repository code + this document + the latest `CONTEXT_HANDOFF.md`/`DECISIONS.md`.

---

# 1. What SketchVox is

SketchVox is a general-purpose, teaching-first visual compiler that turns an explanatory prompt into a modern whiteboard/doodle teaching video.

It is intended to work across:

- finance and investing
- Data/AI
- mathematics
- science
- business
- algorithms
- probability/statistics
- engineering
- other explanatory subjects

The product is **not** merely a whiteboard renderer.

The core product objective is:

> Make the learner understand the concept through a coordinated combination of narration, concise screen language, semantic visuals, relationships, animation and recap.

A technically valid render is not automatically a successful SketchVox video.

---

# 2. Current canonical pipeline

The intended architecture is:

```text
User Prompt
    ↓
Answer Architect
    ↓
LOCKED AnswerIR
    ↓
Teaching Plan
    ↓
Visual Intent / Storyboard
    ↓
Semantic Scene Graph
    ↓
Asset Resolution
    ↓
Visual Choreography
    ↓
Constraint-Based Layout
    ↓
Geometry Proof
    ↓
Animation
    ↓
Deterministic Render
    ↓
Teaching QA
    ↓
Raster / Frame QA
    ↓
Accept / Repair / Rollback
```

Future voice architecture:

```text
AnswerIR
    ↓
Teaching Plan
    ├── Visual Plan
    └── Voiceover Plan
            ↓
      Shared semantic timeline
            ↓
      synchronized visual + narration
```

Voiceover is deliberately **not** a transcript-reading layer.

---

# 3. Non-negotiable product principles

## 3.1 Teaching before visual cleverness

A visual is valuable only when it performs a teaching job.

Every important visual should do one or more of:

- define
- distinguish
- demonstrate
- connect
- transform
- compare
- classify
- quantify
- show cause/effect
- show sequence
- show hierarchy
- show inputs/outputs
- show an exception
- create a memorable mental model

Avoid decorative complexity with no semantic purpose.

## 3.2 Narration and screen are complementary

The desired division is:

**Narration**
- complete explanations
- definitions
- nuance
- caveats
- reasoning
- examples
- transitions

**Screen**
- concise phrases
- labels
- semantic icons
- diagrams
- relationships
- transformations
- attention cues
- visual metaphors
- memorable summaries

Do NOT simply put the narration sentence on the board.

A viewer should ideally understand the topic better when both channels are present, rather than receiving the same information twice.

## 3.3 Visual relationships matter

When concepts have a relationship, show the relationship.

Examples:

```text
A → B
```

for sequence/causality,

```text
A ─┐
B ─┼→ C
C ─┘
```

for converging inputs,

and grouped:

```text
inputs → system/process → outputs
```

for examples and mental models where independent inputs feed a system.

Do not introduce arrows merely because there are multiple objects.

## 3.4 Screen text is concise

Typical display phrases should be short memory cues, usually around 2–6 words.

Long explanations belong in narration unless the teaching task explicitly requires readable text.

## 3.5 Summary must reconstruct the lesson

The final recap should be useful when paused.

It should contain:

- concept identity
- concise memory phrase
- semantic visual/icon where useful

It should not simply repeat full explanations.

---

# 4. AnswerIR is authoritative

AnswerIR is the factual source of truth.

The Answer Architect must produce:

- one-sentence answer
- 2–6 substantive concepts
- a non-empty explanation for every concept
- useful example where appropriate
- important distinction/misconception where appropriate
- final mental model

Each concept requires:

```json
{
  "name": "...",
  "explanation": "..."
}
```

The latest v6.2.1 contract specifically fixed a bug where the JSON schema allowed a concept with a name but no explanation.

### Never weaken the teaching gate to accommodate bad AnswerIR.

Correct sequence:

```text
bad AnswerIR
    ↓
reject / regenerate
```

NOT:

```text
bad AnswerIR
    ↓
weaken teaching QA
    ↓
pretend lesson is valid
```

---

# 5. LLM responsibilities vs deterministic responsibilities

## LLMs may decide

- what the topic means
- what concepts matter
- how concepts relate semantically
- which visual metaphor is appropriate
- which archetype fits the teaching relationship
- concise display phrases
- narration content
- visual intent

## LLMs must NOT own

- pixel-perfect coordinates
- final bounding boxes
- safe canvas calculations
- collision resolution
- renderer-specific geometry
- final connector endpoints
- deterministic text fitting
- geometry proofs

The deterministic compiler owns geometry.

This distinction is fundamental.

---

# 6. Geometry contract

Every rendered object must have valid measured geometry.

The invariant is approximately:

```text
safe_bbox(object) ⊆ safe_canvas
```

The layout compiler must use the same font/measurement assumptions as the renderer.

Do not reintroduce approximate character-count geometry where actual renderer metrics are available.

Layout should solve:

1. wrapping
2. font fitting
3. safe-zone placement
4. parent/child fitting
5. collision handling
6. connector endpoint clipping
7. adaptive sizing
8. final geometry proof

Preferred adaptation order:

```text
wrap
→ reposition
→ reflow
→ reduce secondary copy
→ slightly reduce font
→ split reveal
→ move detail to narration
```

Do not solve geometry by blindly shrinking everything.

---

# 7. Visual grammar / archetypes

SketchVox should select visual structures based on the teaching relationship.

Useful archetypes include:

- Definition
- Process
- Cause/effect
- Comparison
- Hierarchy
- Classification
- Sequence
- Formula
- Quantitative change
- Before/after
- Spatial
- Cycle
- Decision
- Trade-off
- Probability
- Algorithm
- Architecture
- Feedback
- Analogy
- Exception

The system should remain domain-independent.

Unknown topics should degrade gracefully to semantic relationships and generic safe archetypes rather than becoming generic filler.

---

# 8. Relationship grammar

The current UX direction explicitly distinguishes:

- sequential
- converging
- diverging
- contrast
- grouped input/process/output relationships

Examples:

### Sequential

```text
A → B → C
```

### Converging

```text
A ─┐
B ─┼→ Result
C ─┘
```

### Diverging

```text
        → B
A ─────→ C
        → D
```

### Contrast

```text
A   VS   B
```

Use the grammar that reflects the actual teaching relationship.

---

# 9. Choreography

The order in which objects appear matters.

Current important rule:

```text
doodle
   ↓
connector
   ↓
caption
```

Labels should not appear before the visual relationship they describe when that creates ambiguity.

Animation should make the teaching sequence understandable.

Future direction:

```text
semantic event
    ↓
visual event
    ↓
voice event
```

using shared semantic IDs.

---

# 10. Voiceover roadmap

Voiceover is planned as a first-class complementary teaching channel.

Desired architecture:

```text
AnswerIR
    ↓
Teaching Plan
    ├── visual beats
    └── narration beats
            ↓
       shared semantic IDs
            ↓
       master timeline
```

A future narration segment should be able to reference the visual concepts/objects it explains.

Do not implement this by simply copying screen text into speech.

The goal is:

> narration explains what the board demonstrates.

---

# 11. QA hierarchy

Use layered QA:

```text
L0 schema/type validation
L1 deterministic geometry proof
L2 composition / design checks
L3 teaching integrity / concept coverage
L4 rendered/raster/frame QA
```

A design advisory is not automatically a hard failure.

Hard failures include things such as:

- malformed schema
- missing required concept
- unexplained concept
- object outside safe bounds
- invalid geometry
- broken required relationship
- teaching coverage failure
- corrupt render

Advisories can include:

- optional visual polish
- non-critical density
- aesthetic preference
- a repair that is unnecessary because deterministic layout already solved the issue

Do not create brittle tests that fail good output merely because an optional repair was not used.

---

# 12. Transactional repair rule

Automatic repair must never silently worsen a known-good artifact.

Preferred model:

```text
candidate
   ↓
render
   ↓
QA
   ↓
compare against known-good
   ├── better → commit
   └── worse/equal-invalid → rollback
```

Do not mutate the only known-good artifact in place during experimental repair.

---

# 13. Cache correctness

Caches are part of the correctness boundary.

Whenever an output contract changes:

- bump the cache schema/version
- validate cached artifacts against the current contract
- treat invalid old artifacts as cache misses
- regenerate rather than weakening validation

Never assume a cached JSON artifact is valid merely because it parses.

---

# 14. Current v6.2.1 reliability lesson

The latest target-machine failure was:

```text
Ollama visual_director timed out after 300s
    ↓
fallback to deterministic semantic compiler
    ↓
semantic teaching gate failed:
"one or more required concepts are not actually explained"
```

Root cause:

The AnswerIR schema did not require `concept.explanation`.

Fix:

- schema requires `name` + `explanation`
- runtime validation enforces explanations
- stale AnswerIR cache is invalidated
- cache schema bumped to 6.2.1
- regression tests cover malformed AnswerIR/cache

Do not undo this by weakening the teaching gate.

---

# 15. DeepSeek's role

DeepSeek is the implementation workhorse.

Primary responsibilities:

- inspect repository
- trace bugs
- implement approved changes
- refactor safely
- write tests
- run tests
- generate benchmarks
- render test artifacts
- inspect logs
- update mechanical documentation
- prepare small reversible commits

DeepSeek should NOT independently redesign the product architecture unless explicitly asked.

Architecture/product decisions should be escalated for human + ChatGPT review.

---

# 16. Required DeepSeek workflow

When given a bug:

### Step 1 — Reproduce

Do not immediately patch.

Record:

- command
- environment
- exact traceback
- relevant output
- whether failure is deterministic

### Step 2 — Trace

Find:

```text
entry point
→ caller
→ contract
→ data structure
→ failing invariant
```

Inspect the relevant source files before proposing a fix.

### Step 3 — Identify layer

Classify the problem:

- AnswerIR/content
- teaching plan
- visual intent
- semantic storyboard
- asset resolution
- choreography
- layout
- renderer
- raster QA
- cache
- environment/dependency
- packaging

Fix the lowest layer that is actually responsible.

### Step 4 — State root cause

Use this format:

```text
ROOT CAUSE:
...

WHY IT HAPPENS:
...

CORRECT LAYER TO FIX:
...

WHY NOT A DOWNSTREAM WORKAROUND:
...
```

### Step 5 — Implement smallest safe fix

Preserve existing public contracts unless the specification explicitly changes them.

### Step 6 — Add regression test

The new test should fail before the fix and pass after the fix.

### Step 7 — Run targeted tests

Then run the full core suite.

### Step 8 — Render when visual behavior changed

A visual feature is not complete based only on unit tests.

### Step 9 — Report

Always report:

```text
Objective
Root cause
Files changed
Tests run
Test results
Render/benchmark result
Known limitations
Architectural implications
Questions requiring review
Commit hash
```

---

# 17. What DeepSeek must NOT do

Do not:

- weaken teaching gates to make tests pass
- delete tests because they fail
- make QA less strict without evidence
- move pixel geometry into LLM prompts
- replace AnswerIR concepts silently
- rewrite architecture during a small bug fix
- introduce arbitrary magic coordinates
- use character-count estimates when renderer metrics are available
- hide failures behind broad `except Exception`
- silently fall back from a failed invariant to invalid output
- commit `.env`
- commit API keys
- commit credentials
- commit large generated videos unless explicitly requested
- claim a test passed without actually running it
- claim a rendered artifact passed without rendering/inspecting it
- claim Ollama integration works without testing against Ollama

---

# 18. How to handle an unknown bug

If the user says:

> "SketchVox is broken."

DeepSeek should not guess.

Use:

```text
1. inspect repository structure
2. read this document
3. read latest CONTEXT_HANDOFF.md
4. inspect relevant architecture/code
5. reproduce
6. isolate failing layer
7. propose root cause
8. implement minimal fix
9. add regression test
10. run validation
```

If the actual source code is unavailable, explicitly say which files are needed.

This document is a specification/context document, NOT a substitute for source code.

---

# 19. Repository navigation strategy

Do not read every file blindly.

Start with:

```text
README.md
CONTEXT_HANDOFF.md
DECISIONS.md
AI_CONTRIBUTING.md
ROADMAP.md
```

Then inspect the relevant subsystem.

Likely core areas:

```text
whiteboard_engine/
    answer_ir.py
    teaching_providers.py
    storyboard.py
    semantic_storyboard.py
    scene_graph.py
    layout.py
    visual_qa.py
    renderer/
    assets/
```

Use tests as executable documentation.

When debugging a behavior, search for:

- the error string
- the public function
- the data structure
- the invariant
- the relevant test
- the renderer/QA implementation

---

# 20. Source-of-truth hierarchy

When documents disagree, use this hierarchy:

```text
1. actual executable code + passing regression tests
2. latest explicit product-owner instruction
3. latest CONTEXT_HANDOFF.md
4. DECISIONS.md
5. AI_CONTRIBUTING.md
6. older documentation
```

If a conflict affects architecture or product intent, stop and ask for review rather than silently choosing.

---

# 21. Cross-model collaboration

The intended division is:

```text
Human
  ↓
Product intent / taste / acceptance
  ↓
ChatGPT
  ↓
Architecture / UX / visual design / specifications
  ↓
DeepSeek
  ↓
Implementation / tests / debugging
  ↓
Local machine
  ↓
Real render + evidence
  ↓
Human + ChatGPT
  ↓
Design review
```

DeepSeek should produce concise handoffs rather than requiring the entire historical conversation.

---

# 22. Handoff template

For every substantial change, provide:

```markdown
# DeepSeek Handoff

## Objective
...

## Root cause
...

## Approved behavior
...

## Files changed
- ...

## Tests
- command:
- result:

## Render evidence
- command:
- output:
- QA result:

## Known limitations
- ...

## Architectural decisions
- ...

## Open questions for ChatGPT/project owner
- ...

## Commit
...
```

---

# 23. Definition of done

A code change is DONE only when applicable items below are satisfied:

- [ ] Root cause identified
- [ ] Correct architectural layer fixed
- [ ] No teaching invariant weakened
- [ ] Regression test added
- [ ] Targeted tests pass
- [ ] Core suite passes
- [ ] Relevant render generated
- [ ] Visual behavior inspected for visual changes
- [ ] No known-good behavior degraded
- [ ] Documentation/handoff updated
- [ ] Git diff reviewed
- [ ] No secrets committed
- [ ] Known limitations reported

For a purely mechanical change, render validation may not be necessary.

For a visual/animation/teaching change, render validation is required.

---

# 24. Current known roadmap direction

Do not treat these as permission to implement them automatically.

They are product direction for future design discussions:

1. stabilize heterogeneous-topic visual UX
2. make narration and visual choreography complementary
3. create a first-class semantic timeline
4. synchronize voice events with visual events
5. make animation substantially more advanced and modern
6. create stronger visual metaphors and transformations
7. expand reusable semantic assets
8. improve camera choreography without sacrificing readability
9. maintain deterministic geometry and QA
10. benchmark across heterogeneous domains
11. preserve a WOW factor without sacrificing teaching clarity

The target is not "more animation."

The target is:

> **animation that makes the concept easier to understand and feels exceptionally polished.**

---

# 25. Current project status

Current candidate:

```text
v6.2.1
```

Core validation status at the time this document was created:

```text
94/94 core tests passing
compileall passing
AnswerIR/cache regression tests passing
```

Actual Windows/Ollama validation remains important because the development environment does not reproduce the user's local Qwen3/Ollama runtime.

The real target-machine command currently being used is:

```powershell
python run_local_sketchvox.py `
  "Explain photosynthesis to a class-10 student" `
  --duration 45 `
  --no-voice `
  --out-dir output\photosynthesis-v621
```

Do not report this as passed unless it has actually been run on the target machine.

---

# 26. Final instruction to DeepSeek

You are the implementation engineer, not the product owner.

Your job is to:

> understand the existing architecture, identify the actual invariant that is being violated, make the smallest robust implementation change that restores the invariant, prove it with tests, and provide evidence.

When a design problem is discovered, do not automatically solve it with more code.

First determine whether it is:

- a teaching-model problem,
- a visual-language problem,
- a choreography problem,
- a layout problem,
- a renderer problem,
- a QA problem,
- or an implementation bug.

For ambiguous product/UX decisions, preserve the current architecture and ask for human/ChatGPT review.

**Never trade teaching correctness or architectural integrity for a green test suite.**
