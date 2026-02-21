---
id: m-f84d
status: open
deps: []
created: 2026-02-21T01:27:44Z
type: feature
priority: 1
assignee: Adam
---
# Exe labels should propagate into result taint

When an exe has user-declared labels (e.g., `exe untrusted @search(...)`), those labels should propagate into the taint of the exe's return value. Currently, exe results get source taint (`src:mcp`, `src:cmd`, etc.) and specific tool names in `@mx.sources` (`mcp:search`), but the exe's own labels are not carried forward.

This means guards have to hardcode tool names instead of reasoning about categories:

Bad (current): `@mx.sources.includes("mcp:search")`
Good (desired): `@mx.taint.includes("untrusted")`

The fix is likely in `interpreter/eval/exec-invocation.ts` around line 1430-1436 where source taint is derived — the exe's declared labels should be included in the `resultSecurityDescriptor` alongside the source taint.

This enables the pattern where you label tools by risk category and guards operate on those categories generically:

```mlld
exe untrusted @search(q) = ...
exe destructive @closeIssue(id) = ...

guard before destructive = when [
  @mx.taint.includes("untrusted") => deny "Untrusted data cannot reach destructive operations"
  * => allow
]

var @results = @search("query")   >> carries [untrusted, src:mcp]
run destructive @closeIssue(@results.id)  >> BLOCKED by guard
```

## Acceptance Criteria

- Exe user-declared labels appear in result `@mx.taint`
- Protected/source labels still work as before (additive)
- Guards can match on exe labels in downstream data
- Existing tests pass


**2026-02-21 01:41 UTC:** Investigation summary (2026-02-21):

- Direct exec invocation path (evaluateExecInvocation) already propagates executable labels into result descriptor via getVariableSecurityDescriptor(variable) merged into descriptorPieces before source taint (interpreter/eval/exec-invocation.ts:1417-1444).
- The gap is in /run exec dispatch path: run.ts computes exeLabels from exec descriptor (interpreter/eval/run.ts:250-251) but dispatchRunExecutableDefinition output descriptors for command/code/template only include source taint and policy-output labels, not executable labels (interpreter/eval/run-modules/run-exec-definition-dispatcher.ts:218-225, 541-549, 716-721).
- Result: /run @labeledExec(...) can lose the exec's user labels in output taint even while source taint is present.

Likely fix:
1) Ensure run-exec outputs merge exec labels into pending/output descriptor (centralized in run.ts after dispatch, or per-handler in dispatcher).
2) Add regression tests for /run labeled exec outputs, including template + command/code and commandRef wrapper coverage.
3) Keep additive behavior with existing source/protected labels.

**2026-02-21 01:49 UTC:** Implemented fix for /run exec label propagation.

Changes:
- `interpreter/eval/run.ts`
  - In the `runExec` branch, convert `exeLabels` into an output descriptor (`makeSecurityDescriptor({ labels: exeLabels })`) and merge it into pending output descriptors before final output wrapping.
  - This makes `/run @labeledExe(...)` carry the executable's user labels in `@mx.taint`/`@mx.labels`, additive with existing source/policy descriptors.

Regression tests:
- `interpreter/eval/run.characterization.test.ts`
  - Added `propagates executable labels into /run exec output taint`
  - Added `propagates /run exec output labels into downstream pipeline stages`

Validation run:
- `npx vitest run interpreter/eval/run.characterization.test.ts`
- `npx vitest run interpreter/eval/run.structured.test.ts`
