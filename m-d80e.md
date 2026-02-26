---
id: m-d80e
status: closed
deps: []
created: 2026-02-23T16:34:16Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [interpreter, for-loop, parallel]
updated: 2026-02-24T18:27:30Z
---
# Sequential parallel loop crash when both loops have errors

## Current behavior

When a `for parallel` loop encounters errors AND a subsequent `for parallel` loop's first item also errors, the runtime crashes with an unhandled exception.

## Expected behavior

Each `for parallel` loop should have independent error state. Errors in one parallel loop must not corrupt the state for subsequent parallel loops.

## Fix

Find the parallel loop error state management and ensure it properly resets between loops. Add test case with two sequential `for parallel` blocks where both contain items that error.

