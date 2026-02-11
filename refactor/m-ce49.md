---
id: m-ce49
status: closed
deps: [m-3469]
created: 2026-02-09T06:59:36Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-943f
tags: [refactor, import-directive-evaluator, phase-1]
updated: 2026-02-10T13:04:37Z
---
# Refactor Program: Modularize interpreter/eval/import/ImportDirectiveEvaluator.ts - Phase 1: Extract import type inference and validation services



Objective:
Isolate import type resolution/validation from transport-specific handlers.

Instructions for the implementing agent:
- In scope: type inference/validation and duration conversion helpers only.
- Out of scope: transport handler extraction (MCP/resolver/module/node/file/url/dir).
- Extract `resolveImportType`, `inferImportType`, `inferResolverImportType`, `validateDeclaredImportType`, and duration conversion helpers into a dedicated type policy module.
- Keep resolver-specific compatibility rules unchanged (`local`, `templates`, `static`, `cached`, `live`, `module`).
- Add focused tests for declared type mismatch and cache duration conversion behavior.

Module boundaries:
- Type policy module owns import-type inference, compatibility checks, and declared-type validation.
- Transport handlers consume this module through explicit APIs.

Preserve behavior checks:
- Import-type inference semantics remain unchanged.
- Declared-type mismatch errors and messages remain unchanged.
- Resolver compatibility rule matrix remains unchanged.
- Cache duration conversion semantics remain unchanged.

## Acceptance Criteria

1. Type inference/validation logic is extracted into a dedicated module.
2. Error semantics and compatibility behavior remain baseline-compatible.
3. Tests cover mismatch, compatibility, and duration conversion paths.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 13:03 UTC:** Implemented phase scope: extracted import-type inference/validation policy into interpreter/eval/import/ImportTypePolicy.ts (resolveImportType, inferImportType, inferResolverImportType, validateDeclaredImportType, durationToMilliseconds). ImportDirectiveEvaluator now consumes the extracted policy API and no longer owns these helpers. Checklist completed: extraction complete; resolver compatibility rules preserved (local/templates/static/cached/live/module); declared type mismatch semantics preserved; cache duration conversion semantics preserved; focused tests added in interpreter/eval/import/import-type-policy.test.ts and characterization/import-type tests updated. Targeted tests (pass): npx vitest run interpreter/eval/import/import-type-policy.test.ts interpreter/eval/import/import-types.test.ts interpreter/eval/import/import-directive-evaluator.characterization.test.ts interpreter/eval/import/import-runtime-validation.test.ts. Full gate (pass): npm run build && npm test && npm run test:tokens && npm run test:examples.

**2026-02-10 13:04 UTC:** Post-commit validation complete. Checklist status: all phase-1 checkpoints complete. Commands run and pass: npm run build && npm test && npm run test:tokens && npm run test:examples. Commit: 7592b439c.
