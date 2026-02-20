---
id: m-3dec
status: closed
deps: []
created: 2026-02-19T23:02:01Z
type: feature
priority: 1
assignee: Adam Avenir
tags: [severity-high, pre-ga]
updated: 2026-02-20T00:00:36Z
---
# Implement op:loop per-iteration hooks

## Problem

The spec's hook triggers table says `op:loop` fires "Before/after each loop() iteration." But the loop evaluator has no per-iteration hook lifecycle.

## Evidence

- `loop.ts:149-215` uses `withExecutionContext('loop', loopCtx)` per iteration for @mx.loop.* variables but does NOT call `runUserBeforeHooks`/`runUserAfterHooks` or create OperationContext per iteration
- Contrast with `for.ts:180-206` which has `runForOperationBoundary` with full hook lifecycle
- Grep for hook invocation patterns in loop.ts returns no matches

## Impact

`hook @progress after op:loop = [ ... ]` fires once after the entire loop, not per iteration as spec and docs claim.

## Fix

Add per-iteration hook lifecycle to loop.ts mirroring the `runForOperationBoundary` pattern in `for.ts`.

