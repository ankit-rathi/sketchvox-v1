# SketchVox context handoff — 6.2.1 answer-contract reliability candidate

## Product intent

SketchVox is a general-purpose teaching-first whiteboard/doodle video compiler for finance, investing, Data/AI, mathematics, science, business and other explanatory subjects.

The reusable contract is:

`Prompt → AnswerIR → TeachingPlan → semantic scene graph → asset resolution → constraint layout → teaching QA → deterministic render → rendered QA → optional voice retiming → MP4`

Every visual must have a teaching job. Narration explains; screen text labels or emphasizes; visuals demonstrate relationships. The final recap must let a viewer reconstruct the lesson after pausing.


## 6.2.1 reliability fix — current change

The latest real Windows run exposed a contract-boundary bug after the visual-director timeout fallback: the AnswerIR schema allowed concept objects without an `explanation`. The deterministic semantic compiler correctly rejected such an incomplete lesson, but only after the 300-second visual-director timeout.

This candidate fixes the failure at the source:
- AnswerIR JSON schema now requires every substantive concept to contain both `name` and non-empty `explanation`.
- `validate_answer_ir()` independently enforces 2–6 concepts and non-empty explanations.
- stale cached AnswerIR entries that fail the locked contract are treated as cache misses and regenerated.
- storyboard cache schema is bumped to `6.2.1`, preventing older cached artifacts from surviving the contract change.
- regression tests cover the exact malformed-cache/fallback scenario.

The teaching gate itself remains strict. We do **not** weaken `one or more required concepts are not actually explained`; we move the failure earlier to the authoritative AnswerIR boundary.

## Current target-machine acceptance

After installing this candidate, rerun:

```powershell
python run_local_sketchvox.py `
  "Explain photosynthesis to a class-10 student" `
  --duration 45 `
  --no-voice `
  --out-dir output\photosynthesis-v621
```

Expected behavior if the visual director times out: the locked AnswerIR remains valid, the deterministic semantic compiler completes, geometry/teaching QA runs, and the command either renders a valid video or reports a concrete downstream defect. It should not fail because concept explanations are absent.

## Cross-model collaboration

The repository is intended to be GitHub-ready and usable by another ChatGPT account or DeepSeek without reconstructing old chats. See `AI_CONTRIBUTING.md` for roles, invariants, handoff format and DeepSeek's implementation boundaries. `CONTEXT_HANDOFF.md` is the compact project-state source for a new chat.

## 6.2 architecture — important invariants

**Constraint layout is now a compiler stage, not an optional polish pass.**

`whiteboard_engine/layout.py` provides the deterministic geometry contract:

- safe canvas margins;
- renderer-matched text measurement;
- word wrapping and line counts;
- headline/body typography fitting;
- safe-zone placement;
- parent/child container fitting;
- collision avoidance that respects semantic relationships;
- connector endpoint clipping;
- adaptive sizing for charts, pipelines, tables and large icons;
- a final geometry proof.

`scene_graph.object_bbox()` uses the same bundled font metrics used by rendering. This removes the old failure mode where QA used a character-count estimate different from the renderer.

Full-canvas `summary_board` objects are intentional and excluded from ordinary safe-margin validation.

## Visual-first presentation contract

This is a deliberate product rule, not a style preference:

- **Narration explains.** Full sentences, nuance, definitions and caveats belong in the voiceover.
- **The board demonstrates.** Use large concept labels, 1–3 semantic doodles, short phrases and relationship arrows.
- Avoid repeating a narration sentence on screen. A display phrase should normally be a fragment of roughly 2–6 words.
- Causal/process scenes must show the relationship, not just name it.
- Examples and mental models should use grouped `inputs → system → outputs` layouts where appropriate; do not imply a serial transformation between independent inputs.
- Definition scenes should not add arrows unless the arrows represent an actual relationship.
- Summary cards should not repeat explanations; they show a concept title plus a memorable phrase/icon.

## Content contract

AnswerIR is authoritative for factual teaching content. The Answer Architect should:

- answer the actual question in one sentence;
- extract 2–6 substantive concepts;
- explain them in plain language;
- define necessary jargon before relying on it;
- include a concrete example when useful;
- include important distinctions/misconceptions when useful;
- finish with a memorable mental model.

Screen copy should be concise; narration can carry the full explanation. Visual design cannot rewrite AnswerIR or silently replace concept identities.

## Visual/UX contract

- Comic Neue is the primary bundled doodle/handwriting font.
- Noto Sans is used for compact editorial/UI surfaces such as the recap.
- Semantic visual names are open-ended. The asset resolver maps known names to deterministic glyphs and unknown safe names to labelled fallbacks.
- The final recap uses responsive card geometry, one concise memory phrase per concept, semantic icons and protected text zones.
- The world-class renderer must honour `max_width` and measured line wrapping; layout proof and actual raster geometry must agree.
- Camera motion is deliberately restrained; recap readability takes precedence over motion.

## QA hierarchy

1. Schema/type validation
2. Constraint geometry proof
3. Teaching-integrity/coverage validation
4. Design advisory checks
5. Deterministic render
6. Raster/frame QA and OCR where available
7. Transactional repair only for residual presentation defects

A good layout should not be failed merely because a downstream repair is no longer necessary. Tests may accept either deterministic pre-layout resolution or a rendered-QA repair when both preserve the invariant.

## Verification status of this candidate

The development environment passes **88 automated tests**. The new visual-first photosynthesis benchmark was rendered to MP4 and passed rendered-frame QA with no errors; remaining QA messages are non-blocking visual advisories. The generated benchmark was also manually inspected for clipping, semantic arrows, visual richness and recap overlap.

The development environment does **not** have an Ollama server, so actual Qwen3 integration was not claimed as verified. The target-machine gate remains mandatory for BMR and novel-domain tests such as photosynthesis.

## Operational docs

- `README.md`
- `ROADMAP.md`
- `CONTEXT_HANDOFF.md`
- `DEPLOYMENT_GUIDE.md`


## 6.2 UX review outcome

The photosynthesis benchmark was reviewed frame-by-frame as a visual UX artifact. The main issue was not lack of content but weak relationship grammar: some scenes placed related icons without showing the relationship, and some labels moved away from their doodles during constraint repair.

The 6.2 presentation compiler therefore enforces:
1. multi-object teaching beats must expose their relationship with a connector;
2. sequential relationships use linear arrows; additive/input relationships use converging arrows;
3. example and mental-model scenes use grouped inputs → plant/process → outputs;
4. captions are renderer-metric centered and treated as attached to their doodle during collision solving;
5. labels reveal after the doodle and connector;
6. visual phrases are short memory cues, not compressed narration;
7. renderer-safe ASCII `CO2`/`O2` is used where the bundled handwriting font cannot reliably render Unicode subscripts.

Validation after the UX refactor: **94/94 core `tests/` tests pass**, compileall passes, and the photosynthesis teaching sequence has deterministic semantic/rendering coverage for alignment, connector grammar, safe geometry and visual density. The 6.2.1 candidate additionally passes the new AnswerIR schema/cache regression suite. The validation environment does not have `google-genai`, so three legacy `test_scripts/` modules cannot be collected there; this is an environment dependency limitation, not a failure of the core SketchVox suite.
