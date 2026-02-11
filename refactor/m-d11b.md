---
id: m-d11b
status: closed
deps: [m-ef4e]
links: []
created: 2026-02-09T06:24:51Z
type: epic
priority: 1
assignee: Adam Avenir
tags: [refactor, var]
updated: 2026-02-11T05:10:47Z
---
# Refactor Program: Modularize interpreter/eval/var.ts

Largest unplanned target file: `interpreter/eval/var.ts` (~2,418 LOC, ~94 KB).

This epic coordinates a phased refactor that decomposes `/var` assignment evaluation into focused modules while preserving runtime semantics, security descriptor propagation, tool scope enforcement, and pipeline behavior.

Execution order:
1. Phase 0 through Phase 9 proceed strictly in order.
2. Each phase closes only after its exit criteria are met.
3. No phase starts until its dependency ticket is closed.

Cross-phase constraints:
- Keep `prepareVarAssignment` and `evaluateVar` signatures stable.
- Preserve lazy vs eager behavior for arrays, objects, template wrappers, file/content loaders, command execution, and variable references.
- Preserve security descriptor propagation, capability context generation, and variable metadata attachment.
- Preserve tool collection normalization and tool-subset enforcement semantics for `new ... with { tools: ... }`.
- Keep diffs surgical and backed by characterization tests before structural moves.

## Wave 2 Architecture Contract

Target end state for `interpreter/eval/var.ts`:
- `prepareVarAssignment` and `evaluateVar` remain stable entrypoints and mostly orchestration.
- Assignment context, RHS dispatch, collection recursion, content-source branches, variable construction, tool-scope normalization, and post-pipeline rewriting live in focused modules under `interpreter/eval/var/`.
- Lazy/eager behavior and descriptor propagation remain unchanged across nested object/array/template/load-content paths.
- Shared RHS/result contracts are defined once and imported by all var modules.

Wave 2 guardrails for every phase in this epic:
- Do not create runtime modules under 60 lines unless the file is a pure type-definition file or re-export barrel.
- Do not duplicate RHS branch logic across dispatcher handlers; common helpers are extracted once.
- Do not introduce callback-lambda service composition for builder/dispatcher wiring.
- Verify no circular dependencies inside `interpreter/eval/var*` after each phase.

## Minimum Targeted Test Evidence

Every phase note includes targeted coverage from suites that exercise this area, using files such as:
- `tests/interpreter/security-metadata.test.ts`
- `interpreter/eval/run.structured.test.ts`
- `interpreter/eval/show.structured.test.ts`
- representative fixtures under `tests/cases/feat/var/*`

## Acceptance Criteria

1. Phase tickets (0-9) exist under this epic and are dependency-linked in sequence.
2. Each phase ticket includes explicit implementation instructions and explicit exit criteria.
3. Final structure splits major `var.ts` responsibilities into cohesive modules under `interpreter/eval/var/` (or an equivalent focused subdirectory), and reduces `var.ts` to orchestration/composition responsibilities.
4. Final validation command passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-11 05:10 UTC:** Epic architecture checkpoint: m-d11b (/var modularization)

Module set and ownership:
- interpreter/eval/var.ts (328 LOC): entrypoint orchestration for prepareVarAssignment/evaluateVar, descriptor/capability finalization, and module composition.
- interpreter/eval/var/assignment-context.ts (73 LOC): assignment context/identifier/source metadata extraction.
- interpreter/eval/var/security-descriptor.ts (291 LOC): descriptor state, merge helpers, interpolation-aware descriptor extraction.
- interpreter/eval/var/rhs-content.ts (161 LOC): non-execution RHS content evaluation.
- interpreter/eval/var/reference-evaluator.ts (174 LOC): variable-reference and reference-tail evaluation behavior.
- interpreter/eval/var/execution-evaluator.ts (247 LOC): execution-capable RHS branches (command/code/exec/new/for/when/env/loop/foreach).
- interpreter/eval/var/rhs-dispatcher.ts (450 LOC): typed RHS dispatch and branch routing.
- interpreter/eval/var/collection-evaluator.ts (388 LOC): collection/object/array recursion and lazy/eager handling.
- interpreter/eval/var/variable-builder.ts (620 LOC): strategy-based variable construction, source/internal/security factory option wiring.
- interpreter/eval/var/tool-scope.ts (220 LOC): tool scope normalization, bind/expose validation, executable reference and subset enforcement.
- interpreter/eval/var/pipeline-finalizer.ts (130 LOC): pipeline skip/execute matrix and post-pipeline output rewriting.

Callback/cycle-break seams:
- No callback-lambda service injection introduced.
- No recursion-only callback seams added.
- Composition uses direct interface-typed module dependencies with acyclic imports.

Module count and line distribution:
- Runtime modules in /var extraction area: 10 extracted modules under interpreter/eval/var/*.ts plus var.ts orchestrator.
- Runtime LOC distribution spans 73-620 per extracted module; orchestrator is 328 LOC.

Guardrail confirmations:
- No extracted runtime module is under 60 lines.
- No extracted runtime module is a thin facade/forwarder; each contains substantive branch logic.
- Shared helpers/types are centralized by responsibility area; no duplicated sibling branch logic introduced across the extraction area.

Epic validation:
- Final full gate for epic boundary passed:
  npm run build && npm test && npm run test:tokens && npm run test:examples
