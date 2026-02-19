---
id: m-ff34
status: closed
deps: []
created: 2026-02-13T21:49:07Z
type: feature
priority: 2
assignee: Adam
external-ref: mlld
updated: 2026-02-19T02:51:39Z
---
# using auth:* should work with sh/js/py/node blocks, not just cmd

Currently `using auth:*` only works with `cmd {}` blocks. This is limiting because `cmd {}` does direct `@variable` string interpolation, which breaks with complex multi-line prompts containing special characters. `sh {}` blocks use env vars (`$variable`) which handle quoting safely, but can't use `using auth:*` for keychain-backed secrets.

The workaround is inlining `security find-generic-password` in `sh {}` blocks, which is macOS-specific and bypasses the mlld policy system entirely.

`using auth:*` should inject the env var into the execution environment of any block type — sh, js, node, py — not just cmd.

## Acceptance Criteria

- `sh { ... } using auth:name` injects the auth env var into the shell environment
- `js { ... } using auth:name` makes it available via process.env
- Existing `cmd { ... } using auth:name` continues to work
- Policy checks (keychain allow/deny, label flow) still apply across all block types

## Implementation Plan

### Current State

`UsingClause` (`using auth:name` suffix) is defined in `grammar/patterns/with-clause.peggy:110-121`. It's attached to `CmdCommandBrackets`/`UnifiedCommandBrackets` and `UnifiedReference` patterns, but **not** to `RunLanguageCodeCore`/`RunLanguageCodeWithArgs` (the `sh {}`/`js {}`/`py {}`/`node {}` patterns).

The auth injection utility at `interpreter/utils/auth-injection.ts` (`resolveUsingEnvParts()`) is called by `command-handler.ts` and `run-command-executor.ts` for cmd blocks, but **never** by `code-handler.ts` or `run-code-executor.ts` for code blocks.

The execution layer already supports injecting values: `BashExecutor` converts params to env vars, `JavaScriptExecutor`/`NodeExecutor`/`PythonExecutor` inject params as scope variables or env vars.

### Phase 1: Grammar

**File: `grammar/core/code.peggy`** — Add `using:UsingClause?` to both core patterns.

`RunLanguageCodeCore` (line 97): Add after `UnifiedCodeBrackets`, merge into withClause:
```
= streamPrefix:StreamKeyword? _ language:RunCodeLanguage workingDir:WorkingDirPath? _ code:UnifiedCodeBrackets using:UsingClause? {
    ...
    let withClause = streamPrefix ? { stream: true } : undefined;
    if (using) {
      withClause = withClause ? { ...withClause, ...using } : using;
    }
```

Same change for `RunLanguageCodeWithArgs` (line 148).

This propagates to all consumers: `run.peggy`, `run-action.peggy`, `exe-rhs.peggy`.

No ambiguity risk: `UsingClause` starts with `__ "using"` which can't conflict with code bracket content (already consumed by `UnifiedCodeBrackets`).

### Phase 2: Interpreter — Auth secrets as env vars, not scope variables

**Decision: inject auth secrets into process environment, not code scope.**

The `as` field in auth config (`as: "ANTHROPIC_API_KEY"`) names an environment variable. The docs say "injects them as environment variables." The whole sealed-path security model is "secrets flow to OS env vars, never to string interpolation." Injecting into scope instead of env would undermine this design.

Env var injection gives uniform access across all block types:
- sh/bash: `$ANTHROPIC_API_KEY`
- js/node: `process.env.ANTHROPIC_API_KEY`
- python: `os.environ["ANTHROPIC_API_KEY"]`

Auth secrets must be kept **separate from regular params** — they go to `process.env`/spawn env, not into the scope/params object.

How each executor needs to handle it:

| Executor | Execution model | Auth injection approach |
|----------|----------------|------------------------|
| BashExecutor | Subprocess via stdin | Already works — params become env vars on child process |
| NodeExecutor (in-process) | VM sandbox, shares parent `process.env` | Set on `process.env` before execution, clean up after |
| NodeExecutor (subprocess) | `spawn('node', ...)` | Add secrets to `spawn()` env: `{ ...process.env, ...authSecrets }` |
| JavaScriptExecutor | AsyncFunction in-process | Set on `process.env` before execution, clean up after |
| PythonExecutor (subprocess) | `spawn('python3', ...)` | Add secrets to `spawn()` env: `{ ...process.env, ...authSecrets }` |
| PythonExecutor (shadow) | In-process shadow env | Set on `process.env` before execution, clean up after |

#### 2a. `/run` code blocks: `run-code-executor.ts`

- Add `withClause?: WithClause` to `RunCodeExecutionParams`
- In `executeRunCode()`, after arg extraction (line 104), resolve auth:
  ```typescript
  const usingParts = await resolveUsingEnvParts(env, params.withClause);
  ```
- Pass `usingParts.secrets` separately from `argValues` — secrets go to env vars, not scope
- Pass secrets through `env.executeCode()` via a new `options.env` field or handle at the executor level
- Add label-flow policy check (same pattern as `run-command-executor.ts:387-402`)
- Caller: `interpreter/eval/run.ts:226` — pass `withClause: directive.values?.withClause`

#### 2b. `/exe` code blocks: `code-handler.ts`

- In `executeCodeExecutable()` else branch (line 134), before `execEnv.executeCode()`:
  ```typescript
  const usingParts = await resolveUsingEnvParts(execEnv, definition.withClause, node.withClause);
  ```
- Auth secrets (`usingParts.secrets`) go to env vars via `options.env` on the executeCode call — NOT into `codeParams`
- Regular `using @var as ENV` values (`usingParts.vars`) also go to env vars for consistency
- Add label-flow policy check (same pattern as `command-handler.ts:153-170`)

#### 2c. Executor-level env var support

`Environment.executeCode()` and `CommandExecutorFactory.executeCode()` already accept `options?: CommandExecutionOptions` which has an `env` field. The plumbing exists — code-handler and run-code-executor just need to populate `options.env` with the resolved secrets.

For in-process executors (js, node VM, python shadow): set `process.env[key]` before execution, clean up in a `finally` block. This keeps the sealed-path guarantee — secrets never enter the code's variable scope.

### Phase 3: Tests

- **Grammar**: `grammar/tests/regression/run-code-using-auth.test.ts` — verify AST shape for `run sh { } using auth:name` and `run js (@arg) { } using auth:name`
- **Fixtures**: `tests/cases/feat/run-sh-using-auth/`, `tests/cases/feat/exe-js-using-auth/` with mock keychain
- **Policy**: Verify label-flow checks fire for code blocks with auth

### Files Changed

| File | Change |
|------|--------|
| `grammar/core/code.peggy` | Add `UsingClause?` to 2 patterns |
| `interpreter/eval/run-modules/run-code-executor.ts` | Auth resolution + label-flow check (~15 lines) |
| `interpreter/eval/run.ts` | Pass `withClause` to `executeRunCode` (~1 line) |
| `interpreter/eval/exec/code-handler.ts` | Auth resolution + label-flow check (~15 lines) |
| Executor-level (js/node/python) | `process.env` set/cleanup for in-process; spawn env for subprocess |
| + 3-4 test files | Grammar regression + fixture tests |

**2026-02-19 02:51 UTC:** Implemented using auth support for run/exe code blocks (sh/js/node/py), wired auth/using env injection through run-code-executor + exec code-handler + run exec code dispatcher, and enabled executor-level env overrides for Bash/JavaScript/Node/Python. Updated grammar to parse using on RunLanguageCode* and merge with existing withClause/tail. Added grammar regression + integration coverage for auth injection and label-flow enforcement. Verified with: npx vitest run grammar/tests/regression/run-code-using-auth.test.ts tests/integration/policy-label-flow.test.ts and npx vitest run interpreter/env/variable-passing-integration.test.ts.
