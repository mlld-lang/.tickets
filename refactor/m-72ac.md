---
id: m-72ac
status: closed
deps: []
created: 2026-02-12T03:22:29Z
type: task
priority: 1
assignee: Adam
updated: 2026-02-12T05:12:54Z
---
# Consolidation Program: Phase 0 baseline + safety harness

Establish a safe baseline for consolidation work, including protected behavior contracts and explicit module scope.

## Acceptance Criteria

- Define and document in ticket notes the in-scope modules and excluded risky areas.
- Confirm baseline behavior contracts for dispatch mapping, namespace display, guard pre/post behavior, and import rehydration.
- Before committing this phase, run `npm test` and `npm run build`; both must pass.


**2026-02-12 05:11 UTC:** --dir=refactor Phase 0 baseline scope + harness established.

In-scope modules for consolidation phases:
- interpreter/core/interpreter/evaluator.ts
- interpreter/core/interpreter/dispatch.ts
- interpreter/core/interpreter/handlers/*
- interpreter/core/interpreter/namespace-display.ts
- interpreter/eval/import/ObjectReferenceResolver.ts
- interpreter/eval/import/VariableImporter.ts
- interpreter/eval/import/variable-importer/*

Explicitly excluded (risky) until manual gates:
- Guard pre/post execution and retry semantics in interpreter/eval/exec/guard-policy.ts and interpreter/hooks/hook-decision-handler.ts
- Namespace display contract differences between cleanNamespaceForDisplay and JSONFormatter.stringifyNamespace
- Serialization/rehydration boundary behavior between ObjectReferenceResolver and VariableImporter (captured env + executable payload shape)
- Dispatch registry identity contract in interpreter/core/interpreter/handlers/handler-registry.ts

Baseline behavior contracts confirmed:
- Dispatch mapping + registry family contract anchored by interpreter/core/interpreter/dispatch.test.ts
- Namespace display output contract anchored by interpreter/core/interpreter/namespace-display.test.ts and interpreter/eval/show.characterization.test.ts
- Guard pre/post behavior differences treated as intentional; before/after retry and effect-guard behavior covered by security characterization fixtures and tests
- Import rehydration/serialization parity anchored by interpreter/eval/import/variable-importer.characterization.test.ts, interpreter/eval/import/variable-importer/executable-rehydration-matrix.test.ts, interpreter/eval/import/variable-importer/final-composition-parity.test.ts

**2026-02-12 05:12 UTC:** --dir=refactor Verification evidence:
- npm test: PASS (Test Files: 360 passed, 7 skipped; Tests: 3935 passed, 63 skipped)
- npm run build: PASS (quiet build completed successfully)
