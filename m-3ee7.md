---
id: m-3ee7
status: closed
deps: []
links: [m-d777]
created: 2026-02-07T00:43:39Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [interpreter, for, flow-control]
---
# Fix evaluateSequence flow control from if directives in for-expression blocks

evaluateSequence in evaluateForExpression (for.ts:~877) doesn't check evaluate() results for flow control signals (ExeReturnControl, continue, done) after evaluating if directives. if @cond [=> value], if @cond [continue], if @cond [done], and nested if all fail to propagate in for-expression block bodies. Affects both parallel and sequential.

## Acceptance Criteria

1. if @cond [=> value] works as early return from for iteration
2. if @cond [continue] skips the iteration (omitted from results)
3. if @cond [done] stops the loop early
4. Nested if [if [=> value]] propagates correctly
5. Tests: tests/cases/feat/for/for-block-if-early-return/, for-block-if-continue/, for-block-if-done/, for-block-nested-if-return/ (each with parallel+sequential variants)


## Notes

**2026-02-07T00:44:34Z**

Related: m-d777 (when block nested returns), m-1fd4 (when => block return reverted — design tension), m-1145 (when semantics design). Our bug is in a DISTINCT code path (evaluateSequence inside evaluateForExpression) but same class of issue: block evaluation doesn't propagate flow control signals.

**2026-02-07T00:47:44Z**

EXPANDED SCOPE: Directive-level for also affected (G1, G2 tests). The bug exists in BOTH code paths:
1. evaluateForExpression → evaluateSequence (line 877): no flow control check after evaluate()
2. evaluateForDirective → block loop (line 624): checks isExeReturnControl but NOT isControlCandidate (continue/done)

The loop evaluator (loop.ts:196-211) has the correct pattern — checks both isExeReturnControl AND isControlCandidate. For evaluator needs both.

if evaluator (if.ts:59-66) correctly propagates ExeReturnControl via line 63, but returns continue/done as plain lastValue via line 70. The calling code must interpret these.
