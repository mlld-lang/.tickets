---
id: m-b5fc
status: closed
deps: []
created: 2026-02-16T05:06:11Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-16T05:42:08Z
---
# Fix @output in after op:exe guards to contain return value

In after op:exe guards, @output contains the exe's INPUT instead of its return value. @input and @output both point to the same value. This was masked because all existing tests use identity exes where input == output.

## Root cause

In interpreter/hooks/guard-post-runtime-evaluator.ts (lines 92-94):
```typescript
inputVariable = dependencies.cloneVariable(options.activeInput);
outputVariable = inputVariable;   // <-- BUG: output is set to input
```

The orchestrator in guard-post-orchestrator.ts correctly separates outputVariables (from result.value) and inputVariables (from inputs), but the runtime evaluator collapses them.

## What to do

In guard-post-runtime-evaluator.ts, derive inputVariable and outputVariable from different sources:
- inputVariable should come from the original operation inputs (the exe's arguments)
- outputVariable should come from result.value (the exe's return value)

The decision engine in guard-post-decision-engine.ts needs to pass both input variables and output value separately to the guard runtime evaluator, rather than collapsing them into a single activeInput.

## Key files
- interpreter/hooks/guard-post-runtime-evaluator.ts (lines 92-94): where outputVariable = inputVariable
- interpreter/hooks/guard-post-orchestrator.ts (line 101): executePostGuard correctly receives both result and inputs
- interpreter/hooks/guard-post-decision-engine.ts (line 90): passes outputRaw resolved from currentInput

## Acceptance criteria
- In after op:exe guards, @output contains the exe's return value
- In after op:exe guards, @input contains the exe's input arguments
- @output in after op:cmd also contains the command's output (same code path)
- Add test cases with NON-IDENTITY exes (input != output) to verify
- Existing identity-exe tests continue to pass

