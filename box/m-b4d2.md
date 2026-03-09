---
id: m-b4d2
status: closed
deps: []
links: []
created: 2026-03-04T01:37:44Z
type: epic
priority: 0
assignee: Adam
tags: [phase-1, rename, breaking-change]
updated: 2026-03-04T03:13:08Z
---
# Phase 1: env → box rename

Spec: `_specs/box.md` (Part 1, Part 6 migration section)

## Context

Rename the `env` directive to `box`. Breaking change for mlld 2.1.

The `env` keyword is overloaded — it collides with `.env` file loading, environment variables, the internal `Environment` class, and process env. `box` better communicates the directive's purpose: a scoped execution workspace with tool/MCP/network restrictions.

This is the entry ticket for the full `box` feature set defined in `_specs/box.md`. The spec covers 10 phases: rename (this ticket), workspace model, unified write abstraction, file/files directives, configless box blocks, cmd:@workspace, agent config extraction, git hydration, workspace metadata, and policy alignment. All subsequent phases depend on this rename completing first.

This phase is a pure mechanical rename. No new features. Everything that works with `env` works identically with `box` afterward.

## Rename Map (spec Part 1)

| Before | After | Notes |
|---|---|---|
| `env @config [...]` | `box @config [...]` | Directive syntax |
| `env { tools: [...] } [...]` | `box { tools: [...] } [...]` | Config form |
| `SlashEnv` | `SlashBox` | Grammar rule (env.peggy) |
| `EnvKeyword` | `BoxKeyword` | Grammar token |
| `EnvDirectiveNode` | `BoxDirectiveNode` | AST type |
| `kind: 'env'` (DirectiveKind) | `kind: 'box'` | Directive dispatch |
| `subtype: 'env'` (DirectiveSubtype) | `subtype: 'box'` | Directive subtype |
| `evaluateEnv()` | `evaluateBox()` | Evaluator function |
| `resolveEnvConfig()` | `resolveBoxConfig()` | Config resolver |
| `applyMcpConfigForEnv()` | `applyMcpConfigForBox()` | MCP setup |
| `EnvExpression` (var-rhs.peggy) | `BoxExpression` | Inline box as var RHS |
| exe-rhs env patterns | exe-rhs box patterns | Exe env block syntax |

## What does NOT rename (spec Part 1)

| Keep as-is | Reason |
|---|---|
| `Environment` class | Internal interpreter state, not user-facing |
| `EnvironmentConfig` type | Config structure, internal |
| `environment-provider.ts` | Provider execution, internal |
| `EnvLoader` class | Loads `.env` files (process env vars), unrelated |
| OutputTarget type `'env'` | Output target semantics, not the directive keyword |
| GuardDecisionType `'env'` | Guard decision semantics, not the directive keyword |
| `env` in MCP/tool JSON configs | External tool config, not mlld syntax |
| `mlld-env` CLI flag | Process env var loading, not the directive |

## Scope

### Grammar (6 files)
- `grammar/directives/env.peggy` → rename to `box.peggy`, rename all rules (SlashEnv→SlashBox, EnvKeyword→BoxKeyword, EnvConfigExpression→BoxConfigExpression, etc.)
- `grammar/patterns/var-rhs.peggy` → `EnvExpression` → `BoxExpression`
- `grammar/patterns/exe-rhs.peggy` → exe env block patterns → exe box block patterns
- `grammar/deps/grammar-core.ts` → directive registry entry `'env'` → `'box'`
- `grammar/syntax-generator/build-syntax.js` → update directive name generation
- `grammar/mlld.peggy` → top-level dispatch: `SlashEnv` → `SlashBox` (line ~240)

### Core types (4 files)
- `core/types/env.ts` → rename to `box.ts`, `EnvDirectiveNode` → `BoxDirectiveNode`
- `core/types/primitives.ts` → `DirectiveKind 'env'` → `'box'`, `DirectiveSubtype 'env'` → `'box'`
- `core/types/index.ts` → re-export path update
- `core/policy/union.ts` → `KEYCHAIN_SHORT_SERVICE`: `mlld-env-{projectname}` → `mlld-box-{projectname}` (line ~34). CRITICAL: must change together with CLI keychain prefix.

### Evaluator (8 files — rename only, no new features)
- `interpreter/eval/env.ts` → rename to `box.ts`, rename evaluateEnv→evaluateBox, resolveEnvConfig→resolveBoxConfig, applyMcpConfigForEnv→applyMcpConfigForBox
- `interpreter/eval/directive.ts` → update import/dispatch
- `interpreter/eval/pipeline/builtin-effects.ts` → update directive case
- `interpreter/eval/var/execution-evaluator.ts` → update import
- `interpreter/eval/exec/code-handler.ts` → update import
- `interpreter/eval/pipeline/command-execution/handlers/execute-code.ts` → update import
- `interpreter/eval/exe/environment-declaration.ts` → update references
- `interpreter/eval/exe/control-flow-definition-builders.ts` → update env block patterns

### CLI (5 files)
- `cli/commands/env.ts` → rename to `box.ts`, update command names (env capture→box capture, env spawn→box spawn, env shell→box shell)
- `cli/index.ts` → update import: `{ envCommand }` → `{ boxCommand }`
- `cli/execution/CommandDispatcher.ts` → update import, map key, handler name
- `cli/interaction/HelpSystem.ts` → update help text
- `cli/commands/keychain.ts` → update SERVICE_PREFIX from `mlld-env` to `mlld-box`. CRITICAL: must match `core/policy/union.ts` KEYCHAIN_SHORT_SERVICE.

### LSP & Syntax (3 files)
- `cli/commands/chunk-parsing.ts` → update env references to box
- `tests/lsp/chunk-parsing.test.ts` → update test cases
- `editors/vscode/syntaxes/mlld.tmLanguage.json` → update syntax highlighting

### Tests (~25+ dirs) — ALL renames in this phase
- `tests/cases/feat/env/` → `tests/cases/feat/box/`
- `tests/cases/feat/env-expression-var-rhs/` → `tests/cases/feat/box-expression-var-rhs/`
- `tests/cases/exceptions/env/` → `tests/cases/exceptions/box/`
- `tests/cases/docs/atoms/config/1[0-3]-env--*/` → rename to box
- All fixture `.md` files containing `env` directives → update to `box`
- `grammar/tests/env.test.ts` → `box.test.ts`
- `core/policy/union.test.ts` → update keychain prefix expectations (5 lines)
- `interpreter/policy/keychain-policy.test.ts` → update keychain prefix expectations (4 lines)
- `interpreter/eval/exec-invocation.structured.test.ts` → update keychain prefix (line ~408)
- `tests/cases/integration/security/keychain-linux-secret-tool/example.md` → update keychain prefix
- `tests/cases/exceptions/security/keychain-auth-deny-policy/example.md` → update keychain prefix
- Security test fixture content referencing env directives

### Docs
- `docs/src/atoms/config/10-env--basics.md` → `10-box--basics.md`
- `docs/src/atoms/config/11-env--directive.md` → `11-box--directive.md`
- `docs/src/atoms/config/12-env--config.md` → `12-box--config.md`
- `docs/src/atoms/config/13-env--blocks.md` → `13-box--blocks.md`
- `docs/src/atoms/config/09-policy--auth.md` → update keychain prefix (6 occurrences)
- `docs/src/atoms/config/14-auth.md` → update keychain prefix (3 occurrences)
- Content updates in all docs referencing `env` directive
- `docs/user/cli.md` → update CLI documentation
- `examples/env-modules/claude-dev/index.mld` → update keychain prefix (line ~23)
- LLM docs (`docs/llm/`) → rebuild after atom changes via `mlld run llmstxt`

### Other
- `CHANGELOG.md` → add 2.1 breaking change note for env→box rename
- Error messages containing "env" directive references

## Acceptance Criteria
- All existing tests pass with `box` keyword
- `npm run build:grammar` succeeds
- `npm run build` succeeds
- `npm test` passes (full suite)
- Keychain prefix updated in BOTH `core/policy/union.ts` AND `cli/commands/keychain.ts`
- Docs atoms renamed and content updated
- `mlld run llmstxt` rebuilds cleanly
- CHANGELOG.md has 2.1 breaking change note
- Committed before proceeding to Phase 2
