---
id: m-5bf5
status: open
deps: [m-1078]
created: 2026-02-09T07:06:40Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9747
tags: [refactor, core-interpreter, phase-6]
updated: 2026-02-09T07:06:40Z
---
# Refactor Program: Modularize interpreter/core/interpreter.ts - Phase 6: Extract variable value resolution and namespace display utilities



Objective:
Modularize variable value resolution and namespace display utilities while preserving helper API stability.

Instructions for the implementing agent:
- In scope: extract `resolveVariableValue` and `cleanNamespaceForDisplay` logic into utility modules.
- Out of scope: dispatch/resolver orchestration changes and interpolation/security behavior changes.
- Move helper logic to `interpreter/core/interpreter/value-resolution.ts` and `.../namespace-display.ts` (or equivalent).
- Keep exported helper APIs stable and behavior-compatible.
- Add tests for namespace display formatting, hidden/internal filtering behavior, and variable-type-specific resolution semantics.

Module boundaries:
- Value resolution utility handles wrapper/raw extraction semantics only.
- Namespace display utility handles display sanitization/formatting only.
- Utilities avoid interpreter orchestration dependencies.

Preserve behavior checks:
- `cleanNamespaceForDisplay` output shape and formatting remain baseline-compatible.
- `resolveVariableValue` behavior across variable types remains unchanged.
- Export signatures and call contracts for these helpers remain stable.

## Acceptance Criteria

1. Helper logic is extracted into dedicated utility modules with explicit boundaries.
2. `cleanNamespaceForDisplay` and variable resolution semantics remain baseline-compatible.
3. Preserve-behavior checks are covered by focused helper tests.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
