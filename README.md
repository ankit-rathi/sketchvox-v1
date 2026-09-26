# SketchVox 6.2.1 — relationship-first visual teaching + constraint-based doodle engine

SketchVox turns a teaching prompt into a deterministic handwritten/doodle MP4. Local Ollama/Qwen3 supplies topic understanding; the visual compiler, layout engine, assets, renderer and QA are deterministic.

## What changed in 6.2.1

- **AnswerIR contract hardening:** every substantive concept now requires a non-empty name and explanation at the JSON-schema boundary and in runtime validation.
- **Stale-cache protection:** malformed AnswerIR cache entries are ignored and regenerated; the cache schema is bumped so older artifacts cannot silently survive the contract change.
- **Deterministic fallback reliability:** a visual-director timeout can only reach the semantic compiler after a valid locked AnswerIR + TeachingPlan exists.
- **Cross-model collaboration:** `AI_CONTRIBUTING.md` defines the human/ChatGPT/DeepSeek roles and `DECISIONS.md` records architecture invariants.

## What changed in 6.2

- **Clear-content contract:** AnswerIR is concise, topic-specific and viewer-oriented; jargon is defined before it is relied on; narration carries nuance while on-screen copy stays short.
- **Constraint-based layout is mandatory:** every drawable object is measured with renderer-matched font metrics and proven against a safe canvas before rendering.
- **Adaptive text fitting:** text wraps, reflows, resizes and moves without deleting teaching content.
- **Compound geometry fitting:** connectors are clipped safely; charts, pipelines, tables and oversized icons are resized rather than left to fail at QA.
- **Parent/child layout:** labels inside cards/containers stay inside their parent instead of being pushed away by collision avoidance.
- **Bundled doodle typography:** Comic Neue is the primary lesson typeface, with bundled Noto Sans for compact editorial/UI surfaces.
- **Modern presentation layer:** scene chrome, hierarchy, restrained accents, semantic icons and a responsive final recap follow a consistent explainer UX.
- **Open semantic icon vocabulary:** domain concepts can resolve to reviewed/native doodles or deterministic semantic fallbacks without requiring a closed object enum.
- **Transactional rendered QA:** raster QA remains the final safety net; deterministic geometry is solved before QA rather than repaired after the fact.
- **Visual-first presentation contract:** narration is the explanation channel; the board uses short phrases, labels, concrete semantic doodles and relationship arrows instead of repeating commentary.
- **Relationship-first UX:** every multi-object teaching beat has an explicit visual relationship; sequential flows use arrows, additive mechanisms use converging arrows, and examples/mental models use grouped input → process → output grammar.
- **Stable icon/caption alignment:** captions are renderer-metric centered and anchored to their doodle so layout collision repair cannot separate the label from the visual it describes.
- **Reveal choreography:** doodle → connector → caption reduces transient text/graphic collisions and makes the drawing order reinforce the teaching order.
- **Font-safe chemistry labels:** the presentation layer uses renderer-safe ASCII `CO2`/`O2` glyphs instead of unreliable Unicode subscripts.
- **Less redundant copy:** visual phrases are now typically 2–4 words; the final mental model is carried by the diagram itself rather than an extra explanatory sentence.
- **Concept-specific visual composition:** definition, mechanism, example, misconception and mental-model beats use different visual grammars. Causal examples use grouped inputs → system → outputs rather than misleading serial chains.
- **Canonical wrapped text renderer:** the world-class renderer now honours the measured `max_width`/line layout used by the constraint engine, eliminating the previous "measured safe, rendered off-screen" failure.
- **Modern recap UX:** summary cards show a title + one memorable phrase + semantic icon; explanatory paragraphs remain in narration.
- **Semantic doodle improvements:** science/data/AI concepts can use bundled vector doodles (leaf, sun, water, gases, glucose, energy, cell, etc.) instead of degrading to generic sparkles.

## Architecture

```text
Prompt
  ↓
Answer Architect → locked AnswerIR
  ↓
Teaching Planner → TeachingPlan
  ↓
Visual Teaching Director / semantic compiler
  ↓
Visual-first presentation compiler
  ├─ concise display phrases
  ├─ semantic doodle selection
  ├─ archetype-specific composition
  └─ narration/display separation
  ↓
Semantic asset resolution
  ↓
Constraint Layout
  ├─ renderer-matched measurement
  ├─ text wrap/reflow
  ├─ safe-zone placement
  ├─ collision handling
  └─ geometry proof
  ↓
Teaching integrity + preflight QA
  ↓
Deterministic doodle renderer
  ↓
Rendered-frame visual QA + transactional repair
  ↓
Optional Kokoro / authorized voice retiming
  ↓
MP4
```

**Invariant:** no normal drawable object reaches rendering unless its measured geometry fits the target safe canvas. A full-canvas `summary_board` is the intentional exception.

## Quick start — no voice

```powershell
cd C:\SketchVox\genai-pipeline
python run_local_sketchvox.py "Explain photosynthesis to a class-10 student" --duration 45 --no-voice --out-dir output\photosynthesis
```

For a local Ollama installation, use the supplied `.env.example` and keep Qwen3 warm with `SKETCHVOX_OLLAMA_KEEP_ALIVE=10m`.

## Voice

Use Python 3.12 for the Kokoro environment:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r ..\requirements.txt
python -m pip install -r requirements-kokoro.txt
```

## Verification

The current repository passes:

```powershell
python -m pytest -q tests
python -m compileall -q whiteboard_engine
```

The local development verification performed for this candidate includes **94 automated tests**, adversarial layout/presentation tests, and an actual rendered photosynthesis benchmark with rendered-frame QA. The photosynthesis benchmark is intentionally inspected for both text clipping and visual usefulness.

A real Ollama/Qwen3 run is intentionally **not** claimed as passed here because this build environment has no Ollama server. Run the target-machine integration check before treating the archive as production-validated.

## Documentation

- `README.md` — product, architecture and quick start
- `ROADMAP.md` — current architecture and release gate
- `CONTEXT_HANDOFF.md` — durable engineering context
- `DEPLOYMENT_GUIDE.md` — Windows/local deployment
- `AI_CONTRIBUTING.md` — ChatGPT/DeepSeek contribution workflow
- `DECISIONS.md` — durable architectural decisions
