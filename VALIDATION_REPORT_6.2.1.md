# SketchVox 6.2.1 Candidate — Validation Report

## Change under test
The real Windows photosynthesis run exposed an incomplete AnswerIR reaching the deterministic fallback after a 300-second visual-director timeout. The AnswerIR schema did not require concept explanations.

### Fix
- Every AnswerIR concept now requires `name` + non-empty `explanation` in the JSON schema.
- Runtime AnswerIR validation independently enforces 2–6 substantive concepts and non-empty concept explanations.
- Invalid cached AnswerIR entries are ignored and regenerated.
- Storyboard cache schema bumped from `4.2.0` to `6.2.1`.
- Added regression tests for malformed AnswerIR and stale-cache recovery.
- Added `AI_CONTRIBUTING.md` and `DECISIONS.md` for GitHub/DeepSeek/ChatGPT collaboration.

## Automated evidence
- `python -m pytest -q tests` → **94 passed**
- `python -m compileall -q whiteboard_engine` → **pass**
- Targeted semantic/layout/visual-first/contract tests → **17 passed**
- Malformed-cache recovery regression → **pass**
- Deterministic semantic photosynthesis compiler tests → **pass**

## Validation-environment limitation
The three legacy modules under `test_scripts/` import `google.genai` during collection. The current validation environment does not have that optional dependency installed, so a raw `pytest` invocation reports collection errors there. The repository's `requirements.txt` declares `google-genai`; this is not part of the 94-test core SketchVox suite.

## Target-machine acceptance still required
Run the real Windows/Ollama command after installing the candidate. The release is not being represented as real-Qwen/real-Kokoro validated by this report.
