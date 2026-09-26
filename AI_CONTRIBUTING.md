# SketchVox AI Contribution Guide

## Purpose
SketchVox is a teaching-first visual compiler, not merely a whiteboard renderer. AI agents are expected to preserve the teaching contract while improving implementation, visual design, animation and QA.

## Roles
- **Project owner (human):** product intent, taste, acceptance of UX and visual quality, final merge decisions.
- **ChatGPT:** architecture, teaching/UX design, visual language, roadmap, cross-domain reasoning, design review and implementation specifications.
- **DeepSeek:** implementation workhorse: repository exploration, coding, refactoring, tests, debugging, benchmark generation and mechanical documentation updates.
- **Local Qwen3/Ollama:** runtime semantic generation through the Answer Architect and Visual Director contracts. It does not own geometry or the factual source of truth.

## Non-negotiable architecture
`Prompt → AnswerIR → TeachingPlan → Visual Intent/Storyboard → Choreography → Constraint Layout → Geometry Proof → Render → Teaching QA → Raster QA → Accept/Rollback`

AnswerIR is authoritative for factual teaching content. The deterministic compiler owns geometry, layout safety, asset resolution and rendering. LLMs must not be asked to solve pixel geometry.

## Voiceover direction
Voiceover is planned as complementary to the screen, not a transcript of the screen. Narration carries complete explanation and nuance; the board uses concise labels, visual relationships, transformations and attention cues. Future synchronization should use a shared semantic timeline.

## DeepSeek operating rules
1. Read `CONTEXT_HANDOFF.md`, `AI_CONTRIBUTING.md`, `ARCHITECTURE.md` (or the closest current architecture document) and `DECISIONS.md` before major changes.
2. Treat the latest handoff as the current state, not as permission to redesign architecture.
3. Implement approved specifications; do not silently replace contracts or weaken QA gates to make tests pass.
4. Run targeted tests first, then the full suite when practical.
5. Report files changed, tests run, benchmark/render evidence, known limitations and open architectural questions.
6. Prefer small, reversible commits.
7. Never commit `.env`, secrets, credentials, generated caches or large runtime outputs unless explicitly intended.

## Handoff format
Every substantial task should end with:
- Objective
- Approved specification
- Files changed
- Tests run and results
- Render/benchmark evidence
- Known limitations
- Architectural decisions made
- Questions requiring ChatGPT/project-owner review
- Git commit hash

## Definition of done
A change is not complete merely because unit tests pass. For visual features, validate the generated artifact and the teaching experience. Preserve known-good output and use transactional repair/rollback.
