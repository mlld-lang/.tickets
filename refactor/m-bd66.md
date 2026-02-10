---
id: m-bd66
status: open
deps: []
created: 2026-02-09T06:42:54Z
type: epic
priority: 1
assignee: Adam Avenir
---
# Refactor Program: Modularize interpreter/eval/run.ts

Objective:
Break interpreter/eval/run.ts into focused modules with stable interfaces while preserving runtime semantics, policy enforcement, security descriptor flow, streaming behavior, and output effects.

Scope:
- Keep behavior stable across all /run subtypes (runCommand, runCode, runExec, runExecInvocation, runExecReference, runPipeline).
- Preserve operation-context updates, policy checks, taint/descriptor propagation, environment provider execution, and effect emission semantics.
- Preserve pipeline + streaming interactions and final display/output behavior.

Phase plan:
- Phase 0: Baseline and characterization.
- Phase 1: Extract pure helpers and pre-extracted run input readers.
- Phase 2: Extract operation-context and policy/taint guard helpers.
- Phase 3: Extract run-command execution orchestration.
- Phase 4: Extract direct run-code execution path.
- Phase 5: Extract executable reference resolution.
- Phase 6: Extract executable definition handler dispatcher.
- Phase 7: Extract pipeline, streaming, and output-finalization lifecycle.
- Phase 8: Final composition cleanup and architecture verification.

Linked phase tickets:
- Phase 0: m-f706
- Phase 1: m-1f33
- Phase 2: m-e62f
- Phase 3: m-43a7
- Phase 4: m-3887
- Phase 5: m-ba71
- Phase 6: m-4313
- Phase 7: m-4a76
- Phase 8: m-a3d9

Execution rule:
Each phase runs and passes the full test gate before the next phase starts.

## Acceptance Criteria

1. All phase tickets exist and are ordered by dependencies.
2. Each phase ticket includes explicit implementation instructions and clear acceptance criteria.
3. Exit criteria for every phase requires the full test gate to pass before the next phase starts.
4. Final exit criteria: full test gate passes with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
