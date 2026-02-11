---
id: m-fe13
status: closed
deps: []
created: 2026-02-09T07:01:50Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-845d
tags: [refactor, pipeline-command-execution, phase-0]
updated: 2026-02-10T10:37:31Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/command-execution.ts - Phase 0: Baseline and characterization



Objective:
Lock down baseline behavior before extraction.

Instructions for the implementing agent:
- In scope: map current responsibilities in `command-execution.ts` and add characterization tests only.
- Out of scope: moving logic to new files, renaming exports, or changing runtime behavior.
- Produce a branch matrix for `command`, `provider command`, `code`, `node`, `template`, and `commandRef` execution paths with expected output/metadata/retry behavior.
- Add focused tests for first-parameter `@input` binding, structured wrapping behavior, guard preflight outcomes, policy label/descriptor propagation, and retry signaling.
- Record extraction seams and invariants in ticket notes or a short design artifact referenced by this ticket.

Module boundaries for later phases:
- Boundary map identifies candidate modules under `interpreter/eval/pipeline/command-execution/` and owner functions for each module.
- Existing exported API remains unchanged: `resolveCommandReference`, `executeCommandVariable`.

Preserve behavior checks:
- `@input` binds only to the first unbound parameter in pipeline contexts.
- Structured input wrapping keeps text fallback behavior and metadata contracts.
- Guard preflight denial and fallback behavior matches current observable outcomes.
- Policy descriptors and label-flow checks stay consistent across all execution branches.
- Retry signals preserve shape and propagation rules.

## Acceptance Criteria

1. No production code is moved; this phase adds characterization coverage and a concrete extraction map only.
2. Characterization tests cover command/provider/code/node/template/ref branch parity and the listed preserve-behavior checks.
3. Baseline error surface and metadata shape are captured in assertions so later phases can detect drift.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

**2026-02-10 10:37 UTC:** --dir refactor
