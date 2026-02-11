---
id: m-ef95
status: closed
deps: [m-9ab1]
created: 2026-02-09T07:06:07Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-722c
tags: [refactor, for-evaluator, phase-6]
updated: 2026-02-11T20:35:49Z
---
# Refactor Program: Modularize interpreter/eval/for.ts - Phase 6: Extract error aggregation and context reporting helpers



Objective:
Centralize for-loop error/context reporting utilities.

Instructions for the implementing agent:
- Extract error formatting, context reset, and for-context snapshot helpers into dedicated modules.
- Preserve logging and stderr effect behavior for parallel iteration failures.
- Add tests for forErrors context publication and formatted error output behavior.

## Wave 2 Phase Guardrails

Required design constraints for this phase:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate shared logic across sibling modules in this area; extract a shared helper/service when behavior overlaps.
- Do not introduce constructor callback-lambda injection as a service-composition strategy; use interface-typed service objects.
- Define shared result/context/types/type-guards once per extraction area and import them instead of re-declaring.
- Keep module dependency direction acyclic in the touched area before closing the phase.

Evidence required in the phase note before close:
- Touched runtime modules with line counts and a short ownership note per module.
- Targeted test commands run for this phase and pass/fail status.
- Explicit statement that no new sub-60 runtime module was introduced (or list allowed type/barrel exceptions).
- Explicit statement that no callback-lambda service injection was introduced (or justify a recursion-only exception).

## Acceptance Criteria

1. Phase objectives for this slice are fully implemented with cohesive module boundaries.
2. Runtime behavior, error surface, and metadata contracts remain aligned with baseline expectations.
3. Phase-specific coverage is added or updated for the extracted responsibilities.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 20:35 UTC:** Implemented phase-6 extraction for for-loop error aggregation/context reporting.

Changes:
- Added interpreter/eval/for/error-reporting.ts (82 LOC): owns error message formatting, @mx for-error context publication, standardized ForIterationError marker creation, and parallel expression stderr/error aggregation recording.
- Updated interpreter/eval/for.ts (429 LOC): removed local error helper duplication and rewired directive/expression parallel paths to shared error-reporting helpers.
- Updated interpreter/eval/for/batch-pipeline.ts (89 LOC): uses shared createForIterationError for batch pipeline fallback markers.
- Added interpreter/eval/for/error-reporting.test.ts: characterization for wrapper-text stripping, @mx.errors publication, and parallel stderr emission content.

Guardrail checks:
- No new runtime module under 60 LOC introduced (new runtime module is 82 LOC).
- No callback-lambda service injection introduced; composition remains direct service/helper imports.
- Shared error/context helper logic is defined once in error-reporting.ts and reused from for.ts + batch-pipeline.ts.
- Dependency direction remains acyclic in interpreter/eval/for*.

Targeted tests (pass):
- npx vitest run interpreter/eval/for/error-reporting.test.ts interpreter/eval/for.characterization.test.ts interpreter/eval/for-expression-mx.test.ts interpreter/eval/for-expression-when.test.ts interpreter/eval/for-nested-empty.test.ts tests/for/key-binding.test.ts tests/for/parallel-runtime.test.ts tests/for/rate-limit-retry.test.ts tests/integration/for-expr-error-propagation.test.ts tests/parallel/shell-commands-parallel.test.ts

Full gate (pass):
- npm run build && npm test && npm run test:tokens && npm run test:examples
