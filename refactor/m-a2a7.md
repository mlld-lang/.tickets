---
id: m-a2a7
status: closed
deps: [m-5b60]
created: 2026-02-09T06:59:37Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-943f
tags: [refactor, import-directive-evaluator, phase-6]
updated: 2026-02-10T13:52:21Z
---
# Refactor Program: Modularize interpreter/eval/import/ImportDirectiveEvaluator.ts - Phase 6: Extract policy, needs, and binding enforcement services



Objective:
Centralize policy/needs/binding safety logic.

Instructions for the implementing agent:
- In scope: policy import context helpers, needs enforcement, and binding/export validation helper extraction.
- Out of scope: transport-specific import handler extraction.
- Extract policy override/context methods (`withPolicyOverride`, `applyPolicyImportContext`) into a policy import context module.
- Extract needs enforcement (`enforceModuleNeeds`, `findUnmetNeeds`, runtime/package availability checks) into a dedicated validator module.
- Extract and reuse binding/export validation helpers without changing current error code and detail payload structures.

Module boundaries:
- Policy context module owns policy override/context transitions for imports.
- Needs validator module owns unmet-needs detection and runtime/package checks.
- Binding validator module owns alias/export collision and binding safety checks.

Preserve behavior checks:
- Needs and policy enforcement semantics remain unchanged.
- Binding collision guarantees remain unchanged.
- Error codes and detail payload structures remain unchanged.
- Policy context application semantics remain unchanged.

Micro-checklist (policy/needs/collision intersection):
- Add cases where directory-imported bindings feed needs enforcement and assert unchanged unmet-needs reporting.
- Add policy-override context cases that also trigger binding validation and assert unchanged error-code/detail payloads.
- Add collisions that occur after policy context application and assert unchanged collision precedence.
- Add runtime/package availability edge cases with mixed selected/namespace imports and assert unchanged enforcement behavior.

## Acceptance Criteria

1. Policy/needs/binding safety logic is extracted into dedicated modules.
2. Enforcement and collision behavior remain baseline-compatible.
3. Tests cover unmet-needs, policy context transitions, and binding collision failures.
4. Every item in the micro-checklist is backed by explicit tests before closing this phase.
5. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 13:50 UTC:** --dir refactor Implemented Phase 6 extraction:
- Added PolicyImportContextManager (withPolicyOverride + applyPolicyImportContext).
- Added ModuleNeedsValidator (enforceModuleNeeds + unmet-needs/runtime/package checks).
- Added ImportBindingValidator (selected import export/binding validation with unchanged IMPORT_EXPORT_MISSING payloads).
- Rewired ImportDirectiveEvaluator to compose extracted services and removed in-class policy/needs/binding helpers.
- Added and updated tests for phase intersections and extracted module boundaries.
Checklist status:
- [x] directory-imported bindings feeding needs enforcement with unchanged unmet-needs propagation.
- [x] policy-override context case combined with binding validation and unchanged error payload assertions.
- [x] collision precedence before policy-context application preserved.
- [x] runtime/package availability edge behavior covered across selected + namespace import paths.
Tests:
- PASS npx vitest run interpreter/eval/import/import-directive-evaluator.characterization.test.ts interpreter/eval/import/directory-import-handler.test.ts interpreter/eval/import/file-url-import-handler.test.ts interpreter/eval/import/import-runtime-validation.test.ts interpreter/eval/import/module-needs-validator.test.ts interpreter/eval/import/policy-import-context-manager.test.ts interpreter/eval/import/import-binding-validator.test.ts interpreter/eval/import/import-types.test.ts
- PASS npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 13:52 UTC:** --dir refactor Post-commit validation complete.
Commit: e495c8b72 (refactor(import-directive-evaluator): complete m-a2a7 extract policy needs binding services)
Gate:
- PASS npm run build
- PASS npm test
- PASS npm run test:tokens
- PASS npm run test:examples
Phase exit criteria satisfied.
