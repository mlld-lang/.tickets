---
id: m-b2f5
status: closed
deps: []
created: 2026-02-20T20:06:00Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-20T20:10:42Z
---
# Expression args to exe drop taint descriptors

When an expression node is passed as an executable argument, security descriptor metadata from `evaluateUnifiedExpression` is dropped.

Confirmed repro:
```mlld
var pii @name = "John Doe"
var @flag = true
exe @echo(input) = cmd { printf "@input" }

var @direct = @echo(@name)
show @direct.mx.labels      # ["pii"]

var @tern = @echo(@flag ? @name : "x")
show @tern.mx.labels        # []  (expected ["pii"])
```

Root cause:
- `interpreter/eval/exec/args.ts` expression branches (`BinaryExpression`, `TernaryExpression`, `UnaryExpression`, `ArrayFilterExpression`, `ArraySliceExpression`, `WhenExpression`) call `evaluateUnifiedExpression` but only use `.value`, not `.descriptor`.
- This mirrors prior bug m-f680 (fixed for var RHS), but still exists in exec arg evaluation path.

Impact:
- Security labels can be stripped before command execution, weakening label-flow policy/guard enforcement.

Acceptance criteria:
1. Expression-derived descriptors are merged into exec invocation descriptor flow in `evaluateExecInvocationArgs`.
2. Repro above yields `@tern.mx.labels` containing `pii`.
3. Add tests covering expression args (at least ternary) in exec invocations.


**2026-02-20 20:10 UTC:** Fixed in interpreter/eval/exec/args.ts: expression argument branches now merge evaluateUnifiedExpression descriptor into invocation descriptor flow; when-expression arg branch now also merges descriptor extracted from result value. Regression test added in tests/interpreter/security-metadata.test.ts: preserves labels for expression exe arguments. Verified with vitest targeted runs.
