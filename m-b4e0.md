---
id: m-b4e0
status: closed
deps: [m-a623]
created: 2026-02-21T12:41:35Z
type: feature
priority: 0
assignee: Adam
updated: 2026-02-21T14:16:29Z
---
# Support env as exe RHS

Add env as a valid exe right-hand side, enabling reusable functions that run their body in a scoped environment.

**Syntax:**
```mlld
exe @claude(tools, prompt) = env with { tools: @tools } [
  cmd { claude -p "@prompt" }
]
```

**Requires two changes:**

1. **Grammar**: Add env to ExeRHSContent in grammar/patterns/exe-rhs.peggy. Also requires the configless env form (env with {...} [...]) from the related env syntax ticket.
2. **Interpreter**: exe evaluator needs to handle env scoping at call time — create a child environment with the env config, execute the body within it.

**Motivation:** Currently there's no way to define a reusable exe that scopes tools/capabilities per invocation. This is essential for the agent pattern where different calls need different tool sets:

```mlld
exe @agent(tools, task) = env with { tools: @tools } [
  run cmd { claude -p "@task" }
]

var @result = @agent(@readOnlyTools, "summarize this")
var @result2 = @agent(@fullTools, "fix this bug")
```

Without this, users must write verbose inline env blocks at every call site.

## Acceptance Criteria

- `exe @fn(args) = env @config [...]` parses in both strict and markdown mode
- `exe @fn(args) = env with { ... } [...]` parses (configless form)
- exe evaluator creates scoped environment at call time
- Tool restrictions from env config are enforced during exe body execution
- @mx.tools.calls scoped to env block within exe


**2026-02-21 14:16 UTC:** Implemented in commit 4c2310768: grammar support for exe env RHS ( / ), configless env form, and interpreter execution path for env-scoped exe bodies. Covered by grammar/tests/env.test.ts and interpreter/eval/exe-env-rhs.test.ts.

**2026-02-21 14:16 UTC:** Corrected note: implemented in commit 4c2310768 with exeEnv grammar support, mlld-env execution path, configless env syntax, and coverage in grammar/tests/env.test.ts plus interpreter/eval/exe-env-rhs.test.ts.
