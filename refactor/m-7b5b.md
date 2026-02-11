---
id: m-7b5b
status: closed
deps: [m-b140]
links: []
created: 2026-02-09T06:24:51Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-0]
updated: 2026-02-11T03:59:22Z
---
# Refactor Program: Modularize interpreter/eval/var.ts - Phase 0: Baseline and characterization

Objective:
Establish a behavior baseline and safety net for `prepareVarAssignment` and `evaluateVar`.

Instructions for the implementing agent:
- Build a responsibility map for `interpreter/eval/var.ts` with function clusters and current call flows.
- Add/expand characterization coverage for high-risk behavior clusters:
  - identifier extraction and assignment metadata setup
  - lazy vs eager handling for object/array/template values
  - command/code/exec invocation/new expression and env-expression RHS paths
  - variable reference field access, with-tail handling, and condensed pipelines
  - security descriptor collection/merge and capability metadata propagation
  - tool collection validation and tool-subset enforcement in `new ... with { tools: ... }`
  - pipeline skip conditions and post-pipeline variable rewriting
- Capture explicit extraction boundaries for upcoming phases in ticket notes.
- Do not perform structural refactors in this phase.

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

1. Characterization coverage exists for each high-risk cluster listed in the phase instructions.
2. Baseline notes include extraction seams and explicit regression risk callouts.
3. No intentional runtime semantic change is introduced.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples


**2026-02-11 03:57 UTC:** Phase 0 characterization complete.

Responsibility map for `interpreter/eval/var.ts`:
- Assignment context and security bootstrap (`prepareVarAssignment` prologue): identifier extraction, source metadata, capability context setup, descriptor merge helpers.
- RHS dispatch core (`prepareVarAssignment` main body): type-based branches for FileReference/load-content/path/section/code/command/runExec/ExecInvocation/new-expression/env-expression/template/object/array/literal/variable-reference.
- Collection evaluation and complexity guards: `hasComplexValues`, `hasComplexArrayItems`, `evaluateArrayItem`.
- Tool-scope normalization and subset enforcement for `new ... with { tools: ... }`: `evaluateToolCollectionObject`, `resolveWithClauseToolsValue`, `normalizeToolScopeValue`, `enforceToolSubset`, `normalizeToolCollection`.
- Pipeline post-processing and result rewriting: post-assignment pipeline execution, string/structured rewrite path.
- Directive write path: `evaluateVar` environment set + autosign side effect.

Extraction seams for next phases:
1) Assignment context + descriptor/capability service layer.
2) Collection recursion + complexity evaluators.
3) Content-source branches (path/section/load-content/FileReference).
4) Reference + executable RHS evaluators (command/code/runExec/ExecInvocation/new-expression/env-expression).
5) Typed RHS dispatcher orchestration.
6) Variable construction strategy boundary.
7) Tool scope + collection normalization boundary.
8) Pipeline post-processing + rewrite boundary.
9) Final orchestrator composition cleanup.

Characterization coverage added:
- `interpreter/eval/var.characterization.test.ts` (150 lines): identifier/source metadata, object/template lazy-vs-eager behavior, reference tail pipelines (`|` and `with`), tool subset enforcement, structured/string pipeline rewriting.

Checklist evidence:
- Touched runtime modules: none.
- Touched test modules:
  - `interpreter/eval/var.characterization.test.ts` (150 lines): phase safety-net characterization suite.
- No new runtime module under 60 lines introduced.
- No callback-lambda service injection introduced.

Checkpoint commands run:
- `npm run build` ✅
- `npm test -- interpreter/eval/var.characterization.test.ts tests/interpreter/security-metadata.test.ts interpreter/eval/run.structured.test.ts interpreter/eval/show.structured.test.ts interpreter/eval/tools-collection.test.ts` ✅

**2026-02-11 03:59 UTC:** Phase 0 full gate result: ✅
- `npm run build` passed
- `npm test` passed (314 files passed, 7 skipped)
- `npm run test:tokens` passed (6 files passed)
- `npm run test:examples` passed (fixture suite passed)

Ready to close phase 0.
