---
id: m-fd91
status: closed
deps: [m-11c8]
created: 2026-02-09T07:06:40Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-9747
tags: [refactor, core-interpreter, phase-2]
updated: 2026-02-10T12:00:10Z
---
# Refactor Program: Modularize interpreter/core/interpreter.ts - Phase 2: Extract single-node dispatch table



Objective:
Separate node-type dispatch wiring from handler implementations while preserving dispatch semantics.

Instructions for the implementing agent:
- In scope: extract dispatch table and dispatch selection logic only.
- Out of scope: rewriting node handler logic, variable resolver internals, or interpolation/security behavior.
- Introduce a dispatcher module under `interpreter/core/interpreter/dispatch.ts` (or equivalent) mapping node types to handler entry points.
- Preserve fall-through and unknown-node error behavior exactly.
- Add tests validating dispatch parity for representative node types and unsupported-node errors.

Module boundaries:
- Dispatch module receives node and context, then routes to existing handler functions.
- Dispatch module does not implement handler side effects.
- `evaluate` entrypoint remains owner of top-level orchestration.

Preserve behavior checks:
- Node-type routing semantics remain unchanged.
- Unknown-node error category and message path remain stable.
- Handler invocation order and observable side effects remain baseline-compatible.

## Acceptance Criteria

1. Dispatch table logic is extracted into a dedicated module with explicit interfaces.
2. Dispatch routing, fall-through behavior, and unknown-node errors match baseline.
3. Preserve-behavior checks are covered by dispatch-focused tests.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 11:58 UTC:** --dir refactor Implemented phase slice: extracted single-node dispatch selection into interpreter/core/interpreter/dispatch.ts with explicit DispatchTarget mapping and centralized unknown-node error helper. Updated interpreter/core/interpreter.ts to route all single-node branches through dispatch target selection while keeping existing handler logic and side effects unchanged. Completed checklist items: dedicated dispatch module, preserved fall-through/error path, added dispatch-focused tests in interpreter/core/interpreter/dispatch.test.ts for representative routing and unknown-node parity. Tests: npm test -- interpreter/core/interpreter/dispatch.test.ts interpreter/core/interpreter.characterization.test.ts interpreter/core/interpreter/traversal.test.ts (PASS); npm run build && npm test && npm run test:tokens && npm run test:examples (PASS).

**2026-02-10 12:00 UTC:** --dir refactor Phase complete after commit 0578e812a. Post-commit validation: npm run build && npm test && npm run test:tokens && npm run test:examples (PASS).
