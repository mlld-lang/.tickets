---
id: m-cddf
status: open
deps: [m-abb7]
created: 2026-02-09T07:02:43Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-845d
tags: [refactor, pipeline-command-execution, phase-3]
updated: 2026-02-09T07:02:44Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/command-execution.ts - Phase 3: Extract executable normalization and parameter binding



Objective:
Separate executable normalization from pipeline parameter binding with explicit first-parameter rules.

Instructions for the implementing agent:
- In scope: extract executable-def normalization and argument-binding mechanics only.
- Out of scope: guard/policy preflight, branch execution handlers, retry orchestration, or output finalization.
- Move normalization logic (partial/base binding merge, language/op-type derivation) into `.../normalize-executable.ts`.
- Move parameter binding logic into `.../bind-pipeline-params.ts`, including first-parameter pipeline input injection and typed pipeline variable construction.
- Add tests for bound-argument precedence, missing/extra parameter handling, and format-aware pipeline input wrapping.

Module boundaries:
- Normalization module produces canonical executable descriptors only.
- Binding module consumes canonical descriptors and runtime pipeline input to produce invocation args/context.
- Neither module performs command execution.

Preserve behavior checks:
- First-parameter `@input` binding semantics remain unchanged across text and structured inputs.
- Existing bound arguments keep precedence and ordering semantics.
- Typed pipeline variable creation preserves wrapper types and metadata.
- Parameter error behavior and messaging remain baseline-compatible.

## Acceptance Criteria

1. Normalization and binding logic are extracted into dedicated modules with explicit interfaces.
2. First-parameter `@input` semantics and parameter contracts match baseline behavior.
3. Preserve-behavior checks are asserted with targeted tests.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
