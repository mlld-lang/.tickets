---
id: m-03cd
status: open
deps: [m-031c]
created: 2026-02-09T07:01:17Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-225c
tags: [refactor, content-loader, phase-7]
updated: 2026-02-09T07:01:17Z
---
# Refactor Program: Modularize interpreter/eval/content-loader.ts - Phase 7: Final composition cleanup and module boundary verification



Objective:
Reduce `content-loader.ts` to orchestration and verify stable contracts.

Instructions for the implementing agent:
- In scope: final orchestration cleanup, dependency-direction checks, and parity verification.
- Out of scope: introducing new loader semantics.
- Refactor `processContentLoader` into a thin router/orchestrator delegating to extracted modules.
- Remove duplicate utilities and ensure dependency directions remain acyclic.
- Attach architecture notes documenting module boundaries and data-flow through loading/transform/finalize stages.
- Run final parity checks across source reconstruction, AST variants, URL/HTML, file/glob, section matching, and finalization return-shape behavior.

Module boundaries:
- `content-loader.ts` acts as orchestration entrypoint only.
- Extracted modules have acyclic dependencies and explicit ownership by stage (source/read, AST, transports, section, transform, finalize).
- `processContentLoader` contract remains stable.

Preserve behavior checks:
- Source reconstruction and policy enforcement remain unchanged.
- AST extraction variant behavior remains unchanged.
- URL/HTML/file/glob branch parity remains unchanged.
- Section extraction/heading matching remains unchanged.
- Finalization return-shape contracts remain unchanged.

Micro-checklist (final content-loader parity gate):
- Add integrated cases spanning source reconstruction + AST extraction + transform/finalization in one path.
- Add integrated URL/HTML and file/glob parity cases with equivalent section requests and assert equivalent final contracts.
- Add section-matching fallback cases (llmxml + regex fallback) that flow through finalization and assert unchanged outputs.
- Add policy-denied and optional-loader variants that still assert stable final return contracts.

## Acceptance Criteria

1. `content-loader.ts` is orchestration-focused with clear, acyclic module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain baseline-compatible.
3. Final parity checks cover all preserve-behavior requirements listed in this ticket.
4. Every item in the micro-checklist is backed by explicit tests before closing this phase.
5. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
