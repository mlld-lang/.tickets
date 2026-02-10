---
id: m-6a4f
status: open
deps: [m-b451]
created: 2026-02-09T06:58:07Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-c494
tags: [refactor, guard-pre-hook, phase-5]
updated: 2026-02-09T06:58:21Z
---
# Refactor Program: Modularize interpreter/hooks/guard-pre-hook.ts - Phase 5: Extract guard runtime evaluator


Objective:
Move `evaluateGuard` orchestration into a dedicated runtime component.

Instructions for the implementing agent:
- In scope: runtime evaluator orchestration for guard execution and decision shaping.
- Out of scope: helper injection factory logic and block replacement/action helper extraction.
- Extract the core runtime evaluator that creates guard envs, seeds input/output variables, executes policy conditions, and dispatches action decisions.
- Keep return contract identical (`GuardResult` shape, metadata fields, timing, hints, replacements).
- Preserve `env` decision semantics and guard-context wrapping boundaries.
- Add focused tests for allow/deny/retry/env paths, including policy-condition short-circuit behavior.

Module boundaries:
- Runtime evaluator module orchestrates guard execution lifecycle only.
- Policy condition evaluation remains invoked through stable interfaces.
- Runtime evaluator consumes materialization/retry modules and returns normalized `GuardResult`.

Preserve behavior checks:
- Allow/deny/retry/env decision semantics remain unchanged.
- `GuardResult` metadata, hints, timing, and replacement contracts remain unchanged.
- Policy-condition short-circuit behavior remains unchanged.

Micro-checklist (runtime decision invariants):
- Add a decision-path matrix with explicit cases for `allow`, `deny`, `retry`, and `env`.
- Add one case where policy-condition short-circuits action evaluation and assert no downstream action execution.
- Assert `GuardResult` shape equality against baseline for each decision type (including hints/timing/metadata/replacements).
- Assert retry-attempt metadata isolation remains correct when running sequential attempts in the same test.

## Acceptance Criteria

1. Guard runtime evaluation is encapsulated behind a dedicated module API.
2. Metadata contracts and decision semantics remain unchanged.
3. Tests cover all guard decision branches with representative inputs.
4. Every item in the micro-checklist is backed by explicit tests before closing this phase.
5. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
