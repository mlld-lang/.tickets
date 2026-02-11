---
id: m-4c1a
status: closed
deps: [m-3dd9]
created: 2026-02-09T06:59:36Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-943f
tags: [refactor, import-directive-evaluator, phase-3]
updated: 2026-02-10T13:22:04Z
---
# Refactor Program: Modularize interpreter/eval/import/ImportDirectiveEvaluator.ts - Phase 3: Extract resolver and input import handlers



Objective:
Separate resolver transport behavior from top-level import orchestration.

Instructions for the implementing agent:
- In scope: input/resolver import handler extraction and resolver export-data adapters.
- Out of scope: MCP handlers and module/node/file/url/dir handlers.
- Extract input/resolver handler paths (`evaluateInputImport`, `evaluateResolverImport`, resolver export-data adapters) into standalone handler modules.
- Preserve resolver capability checks, keychain denial behavior, and data/module content branching semantics.
- Add targeted tests covering resolver fallback data parsing and selected-import resolution behavior.

Module boundaries:
- Input handler module owns `@input` import flow.
- Resolver handler module owns resolver invocation and export-data adaptation.
- Policy/needs and binding checks remain delegated through existing interfaces.

Preserve behavior checks:
- Resolver capability and keychain denial behavior remain unchanged.
- Data/module content branching semantics remain unchanged.
- Selected-import resolution behavior remains unchanged.

## Acceptance Criteria

1. Input/resolver import logic is extracted into dedicated handlers.
2. Resolver behavior and branching semantics remain baseline-compatible.
3. Tests cover fallback parsing and selected-import resolution parity.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 13:20 UTC:** --dir refactor Extracted input/resolver import paths from ImportDirectiveEvaluator into dedicated handlers and adapter modules: InputImportHandler (input flow), ResolverImportHandler (resolver invocation + capability/keychain/module branching), ResolverImportDataAdapter (getExportData/fallback parsing/selected import mapping). Updated evaluator routing to delegate input+resolver cases while preserving policy and binding contracts. Added targeted parity tests in interpreter/eval/import/resolver-import-data-adapter.test.ts for fallback JSON/object/non-JSON parsing and selected-import resolution behavior.\n\nChecklist/acceptance progress: (1) input/resolver logic extracted into dedicated handlers ✅, (2) resolver capability checks, keychain denial, and module-vs-data branching preserved ✅, (3) fallback parsing + selected-import resolution tests added ✅.\n\nTests run:\n- npx vitest run interpreter/eval/import/resolver-import-data-adapter.test.ts interpreter/eval/import/import-types.test.ts interpreter/eval/import/import-runtime-validation.test.ts interpreter/eval/import/import-directive-evaluator.characterization.test.ts -> PASS\n- npm run build && npm test && npm run test:tokens && npm run test:examples -> PASS

**2026-02-10 13:22 UTC:** --dir refactor Committed phase slice as 31109d417 (refactor(import-directive-evaluator): complete m-4c1a resolver-input-handlers).\n\nPost-commit closure gate:\n- npm run build && npm test && npm run test:tokens && npm run test:examples -> PASS\n\nPhase exit criteria satisfied; ready to close.
