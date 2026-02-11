---
id: m-a5e7
status: closed
deps: [m-9dd5]
created: 2026-02-09T07:03:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-ff50
tags: [refactor, show-evaluator, phase-0]
updated: 2026-02-11T17:45:08Z
---
# Refactor Program: Modularize interpreter/eval/show.ts - Phase 0: Baseline and characterization



Objective:
Establish behavior baseline across show/add subtype handlers.

Instructions for the implementing agent:
- Map subtype branches and shared concerns: descriptor collection, materialization, policy checks, and effect emission.
- Add characterization tests covering showVariable, showPath/showPathSection, invocation variants, foreach variants, load-content, showCommand/showCode, and tail pipelines.
- Capture extraction seams and regression risks; do not move code across files in this phase.

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

**2026-02-11 17:45 UTC:** Completed Phase 0 baseline characterization for `interpreter/eval/show.ts`.

Branch map captured in characterization coverage:
- showVariable (field access + variable-tail pipeline)
- Legacy showPath / showPathSection
- Invocation variants: showInvocation, addInvocation, showExecInvocation, addExecInvocation, addTemplateInvocation
- Foreach variants: showForeach, addForeach
- showLoadContent (section extraction path)
- showCommand and showCode execution paths
- Tail pipeline application path via `meta.applyTailPipeline`

Extraction seams and regression-risk baseline:
- Shared concerns identified in current monolith: descriptor collection/merge, display materialization, policy enforcement, effect emission, and wrapper normalization.
- Subtype families identified for phased extraction: variable resolution pipeline, path/template/load-content sourcing, invocation handling, foreach handling, and runtime execution handlers.
- Legacy compatibility paths (add* subtypes and template invocation) are now explicitly covered before file extraction begins.

Files touched:
- interpreter/eval/show.characterization.test.ts (286 lines): phase baseline and branch parity tests.

Targeted validation (pass):
- npm run build && npx vitest run interpreter/eval/show.characterization.test.ts interpreter/eval/show.structured.test.ts interpreter/eval/alligator.structured.test.ts

Phase gate validation (pass):
- npm run build
- npm test
- npm run test:tokens
- npm run test:examples

Guardrail attestation:
- No runtime modules were created or split in this phase.
- No new sub-60 runtime module introduced (not applicable).
- No callback-lambda service injection introduced.
- No dependency graph changes were introduced in `interpreter/eval/show*`.
