# SketchVox deployment guide

## Windows + Ollama + Qwen3

Recommended layout:

```text
C:\SketchVox\
  requirements.txt
  genai-pipeline\
    run_local_sketchvox.py
    requirements-kokoro.txt
    whiteboard_engine\
    tests\
```

Install the core pipeline:

```powershell
cd C:\SketchVox\genai-pipeline
python -m pip install -r ..\requirements.txt
```

Start Ollama and make sure the selected model exists, for example:

```powershell
ollama pull qwen3:8b
```

Set the model if needed:

```powershell
$env:SKETCHVOX_OLLAMA_MODEL="qwen3:8b"
```

## Voice / Kokoro

Use Python 3.12. Do not install the current Kokoro package into Python 3.13+.

```powershell
cd C:\SketchVox\genai-pipeline
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r ..\requirements.txt
python -m pip install -r requirements-kokoro.txt
```

Install **eSpeak-NG** on Windows and ensure it is available to Kokoro. The first Kokoro run may download/cache model and voice assets.

Default voice: `am_michael`.

If you only want to validate teaching/rendering on a machine without the voice environment, use `--no-voice`.

## Example runs

```powershell
python run_local_sketchvox.py "Explain photosynthesis to a class-10 student" --duration 45 --no-voice --out-dir output\photosynthesis-v54
python run_local_sketchvox.py "Explain BMR (Basal Metabolic Rate) to a beginner" --duration 45 --no-voice --out-dir output\bmr-v54
```

## Verification

```powershell
python -m pytest -q tests
python -m compileall -q whiteboard_engine
```

Recommended heterogeneous integration prompts: photosynthesis, DNA replication, Bayes' theorem, supply and demand, opportunity cost, gradient descent, transformer attention, CPU instruction execution, Kalman filter, inflation, P/E ratio, compound interest, moving average, and RAG.

## Troubleshooting

**`ModuleNotFoundError: whiteboard_engine...`** — run from `C:\SketchVox\genai-pipeline`; the package must contain `whiteboard_engine\__init__.py` and the current source files.

**Unsupported semantic object such as `tree` or `oxygen`** — 5.4 resolves domain vocabulary automatically. If it still appears as a hard validation error, the installation is not using the 5.4 source tree.

**Kokoro installation fails with Python-version messages** — use the dedicated Python 3.12 venv above; do not force an incompatible Kokoro package into Python 3.13/3.14.

**Ollama timeout** — set `$env:SKETCHVOX_OLLAMA_TIMEOUT="1200"` for a slow local model.

**Need a fast pipeline-only test** — use `--no-voice`; this bypasses TTS while retaining teaching, rendering, and visual QA.

## v5.5 Resilient Ollama Architecture

- Qwen3:8b remains the default teaching model. The local Ollama request now uses `keep_alive` (default `10m`) so sequential AnswerIR and Visual Director calls can reuse the loaded model.
- Ollama budgets are stage-specific: `SKETCHVOX_ANSWER_TIMEOUT` and `SKETCHVOX_VISUAL_TIMEOUT`; `SKETCHVOX_OLLAMA_TIMEOUT` remains a legacy fallback.
- The visual-director stage is no longer a single point of failure. If it times out after a valid locked AnswerIR + TeachingPlan, SketchVox compiles a topic-specific semantic storyboard deterministically from those contracts. It never falls back to generic filler for this path.
- `run_local_sketchvox.py` is graceful by default. Use `--strict` when a visual-stage failure should be fatal. Optional CLI overrides: `--ollama-answer-timeout` and `--ollama-visual-timeout`.
- Generation diagnostics record the visual stage, provider/model, elapsed time, fallback mode, and availability of the locked AnswerIR/TeachingPlan.
- This candidate has been tested offline/unit-level; an actual local Qwen3 render must still be run on the target Windows machine because Ollama/model performance is machine-specific.
