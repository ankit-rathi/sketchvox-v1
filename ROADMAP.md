# SketchVox roadmap — 6.2.1

## Current — 6.2.1 relationship-first visual constraint-based teaching compiler

### 6.2.1 reliability focus
- AnswerIR concept explanations are mandatory at schema and runtime boundaries.
- Stale malformed answer caches are rejected/regenerated.
- The deterministic semantic fallback is never allowed to start from an incomplete lesson contract.
- Cross-model handoff is documented for GitHub + DeepSeek + ChatGPT workflows.

### 6.2 UX focus
- Relationship grammar is explicit: sequential, converging, diverging and contrast layouts are selected from semantic intent.
- Captions are tied to their doodles and are not independently collision-shifted.
- Reveal timing follows doodle → connector → caption.
- Screen copy is a visual cue, not a narration transcript.


1. **Content clarity**
   - Locked AnswerIR answers the actual question before visual design.
   - Concepts are topic-specific and explained in plain language.
   - Short labels and screen copy carry the idea; narration carries nuance.
   - Summary cards are derived from the locked concepts.

2. **Robust design**
   - Constraint layout is mandatory during normalization and renderer entry.
   - Renderer and QA share the same text/font measurement model.
   - Text uses wrap → reflow → size reduction → repositioning rather than clipping.
   - Oversized compound primitives are resized deterministically.
   - Parent/child relationships are preserved during collision solving.
   - Safe-canvas geometry is a pre-render invariant.

3. **Modern UX / visual-first presentation**
   - Comic Neue is the primary doodle/handwriting face for lesson marks and headlines.
   - Noto Sans is used for compact labels and editorial/UI surfaces.
   - Screen copy is phrase-first; narration carries full explanations.
   - Definition, mechanism, example, contrast and mental-model scenes use distinct visual grammars.
   - Semantic doodles are selected from topic meaning and arranged with relationship-aware geometry.
   - The recap shows concise memory phrases rather than repeating the narration.

## Next

1. **Reviewed doodle catalog 2.0** — the current source archive has an empty external-doodle manifest, so 6.1 uses bundled deterministic vector doodles. Add provenance-verified SVG/PNG assets when the reviewed icon catalog is supplied.
2. **Evidence-aware teaching QA** — verify that each concept's required visual relationship is actually demonstrated, not merely labelled.
3. **Archetype library** — definition, process, cause/effect, comparison, hierarchy, sequence, formula, quantitative change, cycle, trade-off, algorithm, architecture and feedback patterns.
4. **Benchmark harness** — run a fixed corpus through offline, real Ollama/Qwen3 and novel-domain tests, preserving storyboard/MP4/QA artifacts.
5. **Provider parity** — keep AnswerIR/TeachingPlan contracts identical across Ollama, Gemini and future providers.
6. **Voice adapters** — keep Kokoro and authorized voice-reference workflows isolated from teaching/rendering logic.

## Release gate

A release candidate is not production-validated until:

- clean extraction imports and starts correctly;
- the complete automated suite passes;
- adversarial layout/property tests pass;
- semantic-object rendering smoke tests pass;
- heterogeneous offline end-to-end renders pass rendered-frame QA;
- actual target-machine Ollama/Qwen3 runs pass, including **BMR** and a novel-domain topic such as photosynthesis;
- final MP4s are inspected for content, layout, typography, animation and recap UX;
- voice is separately verified when voice is part of the release target.

## Engineering rule

Deterministic layout owns geometry. Raster QA owns unexpected presentation defects. LLMs own teaching understanding and semantic intent, not pixel coordinates.
