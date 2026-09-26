# SketchVox Architectural Decisions

## ADR-001 — AnswerIR is authoritative
AnswerIR is the factual source of truth for a lesson. Visual generation may choose how to show a concept but may not silently replace or rename substantive concepts.

## ADR-002 — LLMs do not own pixel geometry
LLMs express semantic/visual intent. The deterministic layout compiler owns measured geometry, safe bounds, collision handling and final placement.

## ADR-003 — Teaching gates remain strict
A generic or merely named concept is not considered explained. We fix upstream contracts rather than weakening coverage QA.

## ADR-004 — Voiceover complements visuals
Narration carries complete explanation and nuance; screen text remains concise and visual elements demonstrate relationships. Future voice timing should share semantic IDs with the visual timeline.

## ADR-005 — Cross-model work is specification-driven
ChatGPT/project owner establishes architecture and acceptance criteria; DeepSeek implements and reports evidence. The Git repository and handoff documents are the shared source of truth.
