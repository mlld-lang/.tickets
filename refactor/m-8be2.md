---
id: m-8be2
status: closed
deps: [m-796b]
links: []
created: 2026-02-09T06:24:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-3, content-loader]
updated: 2026-02-11T04:26:20Z
---
# Refactor Program: Modularize interpreter/eval/var.ts - Phase 3: Extract content-source evaluation paths

Objective:
Extract content-source evaluation paths (file reference/path/section/load-content).

Instructions for the implementing agent:
- Move content-loading and section/path evaluation branches into focused module(s) (example target: `interpreter/eval/var/rhs-content.ts`).
- Preserve `readFileWithPolicy` usage and source-location propagation for all file reads.
- Preserve section extraction behavior, including llmxml path and fallback extraction behavior.
- Preserve `withClause.asSection` transform behavior for both glob and single-file load-content sources.
- Add focused tests for path, section, load-content, and asSection transform behavior across single-file and glob inputs.

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

1. Content-source RHS branches are extracted into dedicated module(s) with clear inputs/outputs.
2. Policy, section extraction, and transform semantics remain unchanged.
3. Added tests cover path/section/load-content edge cases, including glob transforms.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-11 04:26 UTC:** Phase close evidence:

Implemented extraction:
- interpreter/eval/var/rhs-content.ts (161 LOC): owns content-source RHS evaluation for FileReference, path, section, and load-content branches, including policy reads with source-location propagation, llmxml section path + fallback extraction, and asSection handling for single-file vs glob load-content nodes.
- interpreter/eval/var.ts (1471 LOC): content-source branches delegate to rhs-content evaluator; orchestration flow and call order remain stable.

Tests added/updated:
- interpreter/eval/var/rhs-content.test.ts: focused coverage for path evaluation, section evaluation (llmxml + fallback), load-content asSection transforms for single-file and glob sources, and FileReference field-access evaluation path.

Checklist status:
- [x] Extracted content-loading and section/path branches into dedicated rhs-content module.
- [x] Preserved readFileWithPolicy usage and source-location forwarding for file reads.
- [x] Preserved llmxml section path with fallback extraction behavior.
- [x] Preserved asSection transform behavior for both single-file and glob load-content sources.
- [x] Added focused tests covering path/section/load-content/asSection behavior.

Targeted tests:
- npm run build -> PASS
- npx vitest run interpreter/eval/var/rhs-content.test.ts interpreter/eval/var/collection-evaluator.test.ts interpreter/eval/var.characterization.test.ts interpreter/eval/content-loader.test.ts -> PASS
- npx vitest run interpreter/interpreter.fixture.test.ts -t "var-data-object|var-data-array|text-assignment-path|section|collection-element-access-array|collection-element-access-object|pipeline-taint|audit-log-taint" -> PASS

Full gate:
- npm run build && npm test && npm run test:tokens && npm run test:examples -> PASS

Guardrail evidence:
- No new sub-60 runtime modules introduced (new runtime module rhs-content.ts is 161 LOC; no type/barrel exception needed).
- No callback-lambda service injection introduced; composition uses typed dependency objects and direct service functions.
- Touched var extraction dependencies remain acyclic (var.ts imports rhs-content; rhs-content does not import var.ts).
