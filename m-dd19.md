---
id: m-dd19
status: closed
deps: [m-3ee7]
links: [m-d777]
created: 2026-02-07T00:43:47Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [interpreter, for, flow-control]
---
# Unwrap control signal wrappers in for-expression runOne results

runOne() in evaluateForExpression (for.ts:~884-934) doesn't unwrap or interpret control signal objects from when expressions before adding to results. {__exeReturn:true,value:X} leaks as-is, {__whileControl:'continue'} leaks instead of becoming SKIP, {__whileControl:'done'} leaks instead of stopping the loop. Affects both parallel and sequential.

## Acceptance Criteria

1. when @cond => [=> value] returns clean unwrapped value in results (no __exeReturn wrapper)
2. when @cond => continue omits iteration from results (SKIP)
3. when @cond => done stops collecting results (break)
4. Nested for: inner for when => [=> value] doesn't leak wrappers to outer
5. Tests: tests/cases/feat/for/for-block-when-return-unwrap/, for-block-when-continue/, for-block-when-done/


## Notes

**2026-02-07T00:47:51Z**

ADDITIONAL FINDING: Deep nesting (I1 test) shows double leaking — for [when => [if [=> value]]] produces __exeReturn wrapper for BOTH item 2 (deep-early) and item 3 (when-normal). The for-when path leaks the wrapper even for non-early-return cases where when matches and evaluates a block action.

Also: nested for inside if (I2 test) — the inner for expression works but the if => value that wraps it doesn't propagate as early return, so item 2 gets inner=[] instead of the computed inner array.

**2026-02-07T01:16:32Z**

Fixed in flow control propagation commit. ExeReturnControl unwrap, __whileControl continue→SKIP and done→DONE, and isControlCandidate checks all implemented in for.ts evaluateForExpression. Tests: for-block-when-unwrap, for-block-when-continue, for-block-when-done.
