---
id: m-9edc
status: open
deps: [m-fe13]
created: 2026-02-09T07:02:43Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-845d
tags: [refactor, pipeline-command-execution, phase-1]
updated: 2026-02-09T07:02:43Z
---
# Refactor Program: Modularize interpreter/eval/pipeline/command-execution.ts - Phase 1: Extract structured input parsing and wrapping helpers



Objective:
Isolate structured-input parse/sanitize/wrap helpers behind a stable internal interface.

Instructions for the implementing agent:
- In scope: extract only structured-input helpers used before execution dispatch.
- Out of scope: command reference resolution, parameter binding, guard/policy orchestration, or branch handler extraction.
- Move parse/sanitize/wrap utilities (`parseStructuredJson`, control-char sanitize, text hooks/proxy wrappers, JSON-like wrappers) into `interpreter/eval/pipeline/command-execution/structured-input.ts` (or equivalent).
- Keep call sites in `command-execution.ts` as orchestration wrappers only.
- Add focused tests for malformed JSON sanitization, JSON-like wrapper behavior, text fallback coercion, and metadata preservation.

Module boundaries:
- New helper module exposes parse/sanitize/wrap functions only; it does not access environment state, guards, policies, or executor dispatch.
- `command-execution.ts` remains the only file wiring these helpers into runtime flow.

Preserve behavior checks:
- Structured parse rejects/accepts inputs with the same error semantics as baseline.
- Control-character sanitization behavior remains identical.
- Structured wrappers preserve `toString`/text fallback behavior expected by downstream transformers.
- Wrapped structured payloads keep the same descriptor/metadata shape consumed by later stages.

## Acceptance Criteria

1. Structured-input logic is extracted to a dedicated module with no behavior changes.
2. `command-execution.ts` references the new helpers without adding new branch logic.
3. Preserve-behavior checks are asserted in tests and pass.
4. Exit criteria: test gate command succeeds and output is attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples
