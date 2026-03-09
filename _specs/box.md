# Spec: `box` — Scoped Execution Workspaces (replaces `env`)

## Summary

Rename the `env` directive to `box`. Integrate VirtualFS + ShellSession as the default built-in runtime. Extract agent configurations (.claude/, .codex/) into importable modules. Add git-based workspace hydration. Breaking change for 2.1.

## Part 1: `env` → `box` rename

### What changes

The user-facing directive keyword `env` becomes `box`. The internal `Environment` class (interpreter execution state) does NOT rename — it's not user-facing.

### Rename scope — directive keyword only

Only the directive keyword and its directly associated grammar/AST/evaluator symbols are renamed. Other uses of the string "env" that represent different concepts are **not touched**.

### Rename map

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
| exe-rhs env patterns (exe-rhs.peggy) | exe-rhs box patterns | Exe env block syntax |

### What does NOT rename

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

### Files to change

**Grammar (6 files):**
- `grammar/directives/env.peggy` → rename to `box.peggy`, rename all rules
- `grammar/patterns/var-rhs.peggy` → `EnvExpression` → `BoxExpression`
- `grammar/patterns/exe-rhs.peggy` → exe env block patterns → exe box block patterns
- `grammar/deps/grammar-core.ts` → directive registry entry `'env'` → `'box'`
- `grammar/syntax-generator/build-syntax.js` → update directive name generation
- `grammar/mlld.peggy` → top-level dispatch: `SlashEnv` → `SlashBox` (line 240)

**Core types (4 files):**
- `core/types/env.ts` → rename to `box.ts`, `EnvDirectiveNode` → `BoxDirectiveNode`
- `core/types/primitives.ts` → `DirectiveKind 'env'` → `'box'`, `DirectiveSubtype 'env'` → `'box'`
- `core/types/index.ts` → re-export path update
- `core/policy/union.ts` → `KEYCHAIN_SHORT_SERVICE` constant: `mlld-env-{projectname}` → `mlld-box-{projectname}` (line 34)

**Evaluator (12 files):**
- `interpreter/eval/env.ts` → rename to `box.ts`, rename all functions, use push/pop API
- `interpreter/eval/directive.ts` → update import/dispatch
- `interpreter/eval/pipeline/builtin-effects.ts` → update directive case
- `interpreter/eval/var/execution-evaluator.ts` → update import
- `interpreter/eval/run-modules/run-exec-definition-dispatcher.ts` → migrate to executeInWorkingDirectory() helper
- `interpreter/eval/exec/code-handler.ts` → update import
- `interpreter/eval/pipeline/command-execution/handlers/execute-code.ts` → update import
- `interpreter/eval/exe/environment-declaration.ts` → update references
- `interpreter/eval/exe/control-flow-definition-builders.ts` → update env block patterns
- `interpreter/eval/run-modules/run-command-executor.ts` → migrate to executeInWorkingDirectory() helper
- `interpreter/eval/run-modules/run-code-executor.ts` → migrate to executeInWorkingDirectory() helper
- `interpreter/eval/code-execution.ts` → migrate to executeInWorkingDirectory() helper
- `interpreter/eval/pipeline/executor/inline-stage-executor.ts` → migrate to executeInWorkingDirectory() helper
- `interpreter/core/interpreter/handlers/command-handler.ts` → migrate to executeInWorkingDirectory() helper
- `interpreter/env/executors/CommandExecutorFactory.ts` → add WorkspaceProvider dependency, check at execution time
- `interpreter/utils/working-directory.ts` → add executeInWorkingDirectory() helper

**Documentation (6 atoms + supporting):**
- `docs/src/atoms/config/10-env--basics.md` → `10-box--basics.md`
- `docs/src/atoms/config/11-env--directive.md` → `11-box--directive.md`
- `docs/src/atoms/config/12-env--config.md` → `12-box--config.md`
- `docs/src/atoms/config/13-env--blocks.md` → `13-box--blocks.md`
- `docs/src/atoms/config/09-policy--auth.md` → update keychain service prefix (6 occurrences)
- `docs/src/atoms/config/14-auth.md` → update keychain service prefix (3 occurrences)
- Content updates in all docs referencing `env` directive

**Tests (~25+ directories):**
- `tests/cases/feat/env/` → `tests/cases/feat/box/`
- `tests/cases/feat/env-expression-var-rhs/` → `tests/cases/feat/box-expression-var-rhs/`
- `tests/cases/exceptions/env/` → `tests/cases/exceptions/box/`
- `tests/cases/docs/atoms/config/1[0-3]-env--*/` → rename to box
- `tests/cases/security/airlock-env-tools-composition/` → update content
- `tests/cases/security/guard-mcp-tools-env-taint-propagation/` → update content
- `tests/cases/feat/exe-shadow-env-capture/` → update content
- `tests/cases/feat/exe-shadow-env-chained/` → update content
- `tests/integration/env-tool-enforcement.test.ts` → update
- `grammar/tests/env.test.ts` → `box.test.ts`
- `core/policy/union.test.ts` → update keychain prefix expectations (5 lines)
- `interpreter/policy/keychain-policy.test.ts` → update keychain prefix expectations (4 lines)
- `interpreter/eval/exec-invocation.structured.test.ts` → update keychain prefix (line 408)
- `tests/cases/integration/security/keychain-linux-secret-tool/example.md` → update keychain prefix
- `tests/cases/exceptions/security/keychain-auth-deny-policy/example.md` → update keychain prefix
- All fixture `.md` files containing `/env` directives

**LSP & Syntax Tooling (3 files):**
- `cli/commands/chunk-parsing.ts` → update env references to box
- `tests/lsp/chunk-parsing.test.ts` → update env test cases to box
- `editors/vscode/syntaxes/mlld.tmLanguage.json` → update syntax highlighting patterns

**CLI (5 files):**
- `cli/commands/env.ts` → rename to `box.ts`, update command names
- `cli/index.ts` → update import: `{ envCommand }` → `{ boxCommand }` (line 16)
- `cli/execution/CommandDispatcher.ts` → update import, map key, handler name (lines 15, 66, 113)
- `cli/interaction/HelpSystem.ts` → update help text
- `docs/user/cli.md` → update CLI documentation
- Current commands: `env capture`, `env spawn`, `env shell` → `box capture`, `box spawn`, `box shell`

**Other:**
- `CHANGELOG.md` → add 2.1 breaking change note
- LLM docs (`docs/llm/`) → update references
- Error messages containing "env" directive references
- Generated parser artifacts (rebuilt after grammar change)
- `examples/env-modules/claude-dev/index.mld` → update keychain prefix (line 23)

## Part 2: Workspace model

Before defining cmd:@box or ShellSession integration, we need a formal workspace model that both depend on.

### Workspace value type

A workspace is a VFS-backed execution context. It has a runtime type:

```typescript
interface WorkspaceValue {
  type: 'workspace';
  fs: VirtualFS;
  descriptions: Map<string, string>;  // path → desc
  shellSession?: ShellSession;        // created lazily on first command
}
```

A workspace value is created by:
- `file`/`files` directives (named form creates/extends a resolver-backed workspace)
- `var @ws = files [...]` (anonymous form creates a standalone workspace value)
- `box` blocks with VFS config (implicit workspace creation)

### Workspace lifecycle

1. **Creation**: `file`/`files` or `box { fs: ... }` instantiates `VirtualFS.empty()` + registers resolver
2. **Population**: file/files writes populate the VFS shadow layer
3. **Activation**: first command execution creates `ShellSession.create(vfs)` lazily
4. **Execution**: commands route through ShellSession; file changes tracked in shadow
5. **Inspection**: `.mx.edits`/`.mx.diff` query VFS state
6. **Teardown**: workspace scope exits; VFS available for inspection via result

### ShellSession lifecycle

ShellSession is created **lazily** on first command execution within a workspace, not at workspace creation time. This means:
- Workspaces used only for file projection (no commands) pay no ShellSession cost
- The ShellSession persists for the lifetime of the workspace scope (state accumulates across commands)
- Nested box blocks create nested workspaces with separate ShellSessions

### Where ShellSession attaches

ShellSession attaches to the **WorkspaceValue** as optional state (line 117). Environment maintains a **stack** of active workspaces to support nesting:

```typescript
// In Environment class (new field)
private workspaceStack: WorkspaceValue[] = [];  // Stack for nested workspace contexts

// Public API
pushActiveWorkspace(workspace: WorkspaceValue): void {
  this.workspaceStack.push(workspace);
}

popActiveWorkspace(): WorkspaceValue | undefined {
  return this.workspaceStack.pop();
}

getActiveWorkspace(): WorkspaceValue | undefined {
  return this.workspaceStack[this.workspaceStack.length - 1];
}

// Child environment creation
createChild(options?: ChildEnvironmentOptions): Environment {
  const child = new Environment({
    ...options,
    // CRITICAL: Copy parent's workspace stack so child inherits context
    workspaceStack: [...this.workspaceStack],  // Shallow copy
  });
  return child;
}
```

**Important:** ShellSession state lives in WorkspaceValue, NOT Environment. The stack enables proper nesting: `box @outer [ cmd:@inner {...} ]` pushes `@inner`, then pops back to `@outer`.

**Child environment inheritance:** When `createChild()` is called, the child **copies** the parent's workspace stack (shallow copy of the array). This ensures:
- Commands inside box blocks see the box's workspace (exe invocations create child envs)
- Child can push/pop independently without affecting parent
- Parent and child stacks diverge after creation (no shared reference)

**Integration point:** `CommandExecutorFactory.executeCommand()` is the centralized integration point for ShellSession. At execution time (not factory creation), the method checks `env.getActiveWorkspace()?.shellSession` and routes to ShellSession if present. Otherwise, it falls through to standard executors (ShellCommandExecutor, BashExecutor) that execute on the real filesystem.

**Key timing:** The factory is a lazy-created singleton per Environment (created on first command, then cached). The activeWorkspace changes dynamically between commands, so the check must happen in `executeCommand()` at call time, not during factory creation.

**Dependency wiring:** Add `WorkspaceProvider` interface to factory dependencies:
```typescript
export interface WorkspaceProvider {
  getActiveWorkspace(): WorkspaceValue | undefined;
}

export interface ExecutorDependencies {
  // ... existing dependencies
  workspaceProvider: WorkspaceProvider;  // NEW
}

// In Environment.getCommandExecutorFactory()
const dependencies: ExecutorDependencies = {
  // ... existing
  workspaceProvider: this,  // Environment implements WorkspaceProvider
};
```

The factory accesses workspace through the provider interface, not direct Environment access.

**Provider routing:** Provider-based execution (Docker, cloud) routes through `executeProviderCommand()` at three call sites (`run-exec-definition-dispatcher.ts:354`, `run-command-executor.ts:409`, `execute-command.ts:124`) and bypasses CommandExecutorFactory entirely. ShellSession integration targets local execution only; providers handle their own isolation mechanisms.

**Provider + cmd:@workspace precedence:** When both provider and `cmd:@workspace` are specified, the provider takes precedence and the workspace reference is ignored (commands execute in provider environment, not local VFS). This is consistent with existing provider semantics where provider config overrides local execution context. A warning may be emitted if both are detected.

### Nesting behavior

```mlld
box @outer-workspace [
  >> commands here use outer workspace's ShellSession
  run cmd { echo "outer" }

  box { fs: @inner-workspace, tools: ["Read"] } [
    >> commands here use inner workspace's ShellSession (separate VFS)
    >> tool restrictions from inner box apply
    run cmd { cat file.md }
  ]

  >> back to outer workspace's ShellSession
  run cmd { echo "still outer" }
]
```

Child box blocks create child environments with a **copy** of the parent's workspace stack. The child then pushes its own workspace (in `evaluateBox()`). The parent's workspace stack is unaffected by child modifications. When the child environment exits, the parent's workspace stack remains unchanged.

**Child environment inheritance:** Child environments **copy** the parent's workspace stack at creation (`[...this.workspaceStack]`). This ensures commands inside box blocks (which create child envs for exe invocations) see the box's workspace:
```mlld
box @workspace [
  file "task.md" = @content
  exe @agent-handler        // exe invocation creates child env
                            // child MUST see @workspace to access task.md
]
```

The copy is shallow - both parent and child point to the same WorkspaceValue objects, but modifications to the stack array itself are independent.

## Part 3: `cmd:@workspace` — workspace-aware exe definitions

### Current behavior

`cmd:/path` on exe definitions sets the working directory. Grammar parses it as path interpolation (working-dir-path.peggy). Runtime resolution forces a string absolute Unix directory that must exist on the real filesystem.

### New behavior

`cmd:` accepts either a filesystem path OR a workspace reference:

```mlld
exe @lint(src) = cmd:@src { eslint . }
exe @test(ws) = cmd:@ws { npm test }
exe @claude(prompt, tools, ws) = cmd:@ws { claude -p "@prompt" --allowed-tools @tools }
```

### Grammar changes required

**No grammar changes needed.** The existing `cmd:` parsing already supports variable interpolation via `@resolver` references through the `UnifiedVariableNoTail` pattern. The distinction happens at runtime, before interpolation.

**Runtime resolution:**
Working directory resolution checks for WorkspaceValue **before** calling `interpolate()` (which always returns strings and would JSON-stringify objects):
```typescript
// In resolveWorkingDirectory()
export type WorkingDirectoryResult =
  | { type: 'path'; path: string; workspacePushed: false }
  | { type: 'workspace'; workspacePushed: true }
  | { type: 'none'; workspacePushed: false };

export async function resolveWorkingDirectory(
  workingDir: ContentNodeArray | string | undefined,
  env: Environment,
  options: WorkingDirectoryOptions = {}
): Promise<WorkingDirectoryResult> {
  if (!workingDir || (Array.isArray(workingDir) && workingDir.length === 0)) {
    return { type: 'none', workspacePushed: false };
  }

  // Check if workingDir is a single variable reference
  if (Array.isArray(workingDir) && workingDir.length === 1) {
    const node = workingDir[0];
    if (node.type === 'VariableReference' || node.type === 'TemplateVariable') {
      const varName = node.identifier || node.name;

      // Get variable (may be direct or resolver-backed)
      let variable = env.getVariable(varName);

      // Handle resolver-backed variables (async resolution)
      if (!variable && env.hasVariable(varName)) {
        variable = await env.getResolverVariable(varName);
      }

      // Check if this is a WorkspaceValue BEFORE interpolating
      if (variable?.value && typeof variable.value === 'object' &&
          (variable.value as any).type === 'workspace') {
        // CRITICAL: Push active workspace with stack semantics
        // Return workspacePushed: true so caller knows to pop
        env.pushActiveWorkspace(variable.value as WorkspaceValue);
        return { type: 'workspace', workspacePushed: true };
      }
    }
  }

  // Fall back to string interpolation for paths
  const rawPath = typeof workingDir === 'string'
    ? workingDir
    : await interpolate(workingDir, env, InterpolationContext.FilePath);

  const normalized = validateAndNormalizeWorkingDirectory(rawPath.trim());
  return { type: 'path', path: normalized, workspacePushed: false };
}
```

**Key insight:** `interpolate()` has a string-only contract (`Promise<string>`). Workspace values must be detected and handled before interpolation to avoid JSON-stringification.

**Example:**
```mlld
var @dir = "/tmp"                              // String value
var @workspace = files ["task.md": @content]   // WorkspaceValue

exe @local = cmd:@dir { pwd }                  // Interpolates to string → filesystem
exe @isolated = cmd:@workspace { pwd }         // Detected as WorkspaceValue → ShellSession
```

### Runtime resolution

In `interpreter/utils/working-directory.ts`:

1. Check if `cmd:@x` is a single variable reference
2. If yes, look up variable value directly (before interpolation)
3. If variable value is WorkspaceValue → push onto workspace stack, return `{ type: 'workspace', workspacePushed: true }`
4. Otherwise, interpolate to string → validate as filesystem path

**Clarification:** Parameters that are workspace values work the same way:
```mlld
exe @fn(ws) = cmd:@ws { cat task.md }

var @workspace = files ["task.md": "content"]
run @fn(@workspace)  // @ws resolves to WorkspaceValue, detected before interpolation
```

**Why before interpolation?** The `interpolate()` function has a string-only contract (`Promise<string>`). If it encounters an object, it JSON-stringifies it. We must check the variable's type before interpolation to preserve the WorkspaceValue object.

**Resolver-backed variables:** The check handles both direct variables (`getVariable()`) and resolver-backed variables (`getResolverVariable()`). For `cmd:@workspace` where `@workspace` is a resolver (like from `file`/`files` directives), the async resolution happens before the type check.

### Integration with Environment.executeCommand()

When `cmd:@workspace` is used, the exe's command execution sets `env.activeWorkspace` and routes through the workspace's ShellSession. Commands check for an active workspace before falling back to local execution.

**Cleanup semantics:** Use a shared helper to ensure proper stack push/pop:
```typescript
// In interpreter/utils/working-directory.ts
export async function executeInWorkingDirectory<T>(
  workingDir: ContentNodeArray | string | undefined,
  env: Environment,
  execute: (resolvedPath?: string) => Promise<T>
): Promise<T> {
  const result = await resolveWorkingDirectory(workingDir, env);

  try {
    // Pass resolved path to execute callback for policy checks and command options
    const path = result.type === 'path' ? result.path : undefined;
    return await execute(path);
  } finally {
    // Only pop if resolveWorkingDirectory() actually pushed
    if (result.workspacePushed) {
      env.popActiveWorkspace();  // Restore previous workspace context
    }
  }
}
```

**All call sites** that use `resolveWorkingDirectory()` should migrate to this helper:
- `command-handler.ts`
- `code-execution.ts`
- `run-code-executor.ts`
- `run-command-executor.ts`
- `run-exec-definition-dispatcher.ts`
- `inline-stage-executor.ts`
- `exec-invocation.ts`
- `pipeline/command-execution.ts`

This ensures consistent stack semantics and prevents workspace leakage across nested contexts.

## Part 4: Built-in box runtime

### Current state

The `box` directive can delegate to providers (Docker, cloud). Without a provider, commands run locally with tool/MCP restrictions but no filesystem isolation.

### New behavior: VFS with ShellSession for local execution

When a `box` block has a VFS-backed filesystem (via `fs` config or VFS resolver shorthand) and no explicit provider, ShellSession becomes the automatic runtime for local command execution.

```mlld
>> Config form with fs
box { fs: @workspace } [
  run cmd { grep -r "TODO" . }
]

>> VFS resolver shorthand
box @workspace [
  run cmd { npm test }
]
```

### Grammar change: configless box blocks

Current grammar (`grammar/directives/env.peggy`) requires a config expression or with-clause. New forms needed:

```
BoxDirective
  = BoxKeyword config:BoxConfig WithClause? Block     // existing: box { ... } [...]
  / BoxKeyword resolver:ResolverRef WithClause? Block  // new: box @workspace [...]
  / BoxKeyword Block                                    // new: box [...] (anonymous VFS)
```

The configless `box [...]` form creates an anonymous `VirtualFS.empty()` workspace. Useful when `file`/`files` inside the block populate it.

### Evaluator changes

In `evaluateBox()` (formerly `evaluateEnv()`):

1. **Resolve config**: determine if config is object, VFS resolver, or absent
2. **Create workspace**: if VFS resolver → use its VFS; if absent → create `VirtualFS.empty()`
3. **Push on child environment**: `childEnv.pushActiveWorkspace(workspaceValue)`
4. **Apply defaults**: when workspace is VFS-backed:
   - Network disabled (unless `net:` specified)
   - MCP disabled (unless `mcps:` specified)
   - Bash/Read/Write tools enabled (unless `tools:` restricts)
5. **Execute block**: local commands route through `Environment.executeCommand()` which detects active workspace's ShellSession
6. **Pop workspace**: `childEnv.popActiveWorkspace()` in finally block (child env destroyed anyway, but explicit is better)
7. **Return**: workspace state available for inspection on result

### Default precedence

Box config defaults merge with existing policy/needs/auth/tool constraints from the parent environment:

| Config field | Box default (VFS mode) | Parent override? |
|---|---|---|
| `tools` | `["Bash", "Read", "Write"]` | Parent can only restrict further, never expand |
| `net` | `{ allow: [] }` (disabled) | Box config can allow; parent restrictions still apply |
| `mcps` | `[]` (disabled) | Box config can allow; parent restrictions still apply |
| `auth` | inherited from parent | Box config can add; parent auth carries through |
| `provider` | ShellSession (built-in) | Explicit provider overrides ShellSession |

### Escalation path

```mlld
>> Level 0: no box (commands run on real FS, no restrictions)
run cmd { npm test }

>> Level 1: box with restrictions only (no VFS, no ShellSession)
box { tools: ["Bash", "Read"] } [
  run cmd { npm test }
]

>> Level 2: box with VFS (in-memory sandbox via ShellSession)
box @workspace [
  run cmd { npm test }
]

>> Level 3: box with Docker provider (full OS isolation)
box { provider: "@mlld/docker", fs: @workspace } [
  run cmd { npm test }
]
```

## Part 5: `file`/`files` inside box blocks

### Context

`file`/`files` directives are defined in spec-file-projection.md. Inside a `box` block, they target the box's workspace VFS rather than creating a named resolver.

### Behavior inside box

```mlld
box [
  file "task.md" = @task
  files "src/" = [
    { "index.js": @source, desc: "Entry point" }
  ]
  exe @agent-handler
]
```

When `file`/`files` appear inside a `box` block without a `<@resolver/...>` target:
- They write to the box's workspace VFS
- No resolver is created — the files are scoped to the box
- The `file`/`files` directives must be valid inside block statement grammar

### Grammar requirement

Currently, exe/box block statement grammar (`grammar/mlld.peggy`, `grammar/patterns/exe-rhs.peggy`) does not include `file`/`files` as valid block statements. These must be added to the directive list that's valid inside blocks.

### Prerequisite: unified write abstraction

`file`/`files` need the unified write abstraction (spec-file-projection.md, runtime contract #4) before they can work. The write path must support resolver routing, VFS writes, and workspace-scoped writes inside box blocks.

## Part 6: Agent config modules

### Current state

Agent configurations are set up via CLI:
- `mlld env capture` (not `env create` — corrected from earlier draft)
- Copies settings, instructions, hooks to `.mlld/env/{name}/.claude/`
- Extracts OAuth token and stores in keychain
- Generates `module.yml` and `index.mld`

This is baked into mlld core.

### Target state

Agent setup becomes a module concern. mlld core provides the primitives (`box`, `file`/`files`, auth/keychain). Agent-specific knowledge lives in registry modules.

```mlld
import { @claude-setup } from "@mlld/agents/claude"

box [
  files ".claude/" = @claude-setup
  file "task.md" = @task
  files "src/" = @source-files
  exe @claude-agent
]
```

### What the module exports

A claude config module (`@mlld/agents/claude`) exports:

```mlld
>> @mlld/agents/claude/index.mld

var @settings = {
  "permissions": {
    "allow": ["Bash(*)", "Read(*)", "Write(*)"]
  }
}

var @claude-md = "You are working in a sandboxed mlld workspace..."

export { @setup }
var @setup = [
  { "settings.json": @settings, desc: "Claude Code permissions" },
  { "CLAUDE.md": @claude-md, desc: "Agent instructions" }
]
```

Users can fork, extend, or compose:

```mlld
import { @setup } from "@mlld/agents/claude"
import { @my-hooks } from "./my-hooks.mld"

var @my-setup = @setup + [
  { "hooks.json": @my-hooks, desc: "Custom safety hooks" }
]

box [
  files ".claude/" = @my-setup
  exe @agent
]
```

### Auth handling

Auth extraction and keychain storage become a module-level concern:

```mlld
import { @setup, @configure-auth } from "@mlld/agents/claude"

var @auth = exe @configure-auth

box { auth: @auth } [
  files ".claude/" = @setup
  exe @agent
]
```

Or as a generic keychain tool:

```mlld
var @auth = keychain import "claude" from "~/.claude/.credentials.json" path ".oauthToken"
```

### `mlld box capture` CLI (replaces `mlld env capture`)

The CLI command becomes a convenience wrapper that:
1. Discovers the agent type (claude, codex, etc.)
2. Pulls the appropriate module from registry (`@mlld/agents/claude`)
3. Runs the module's auth setup (keychain import)
4. Generates a local config file (`.mlld/box/{name}/index.mld`)

The generated file is a plain mlld module — users can inspect, modify, and extend it.

### Migration from mlld 2.0

**No automatic migration:** Existing `.mlld/env/*` directories and `mlld-env` keychain entries will NOT be auto-migrated.

**Manual migration steps:**
1. **Directories:** Rename `.mlld/env/` → `.mlld/box/` (if they exist)
2. **Scripts:** Update all `env` → `box` in your `.mld` files
3. **Keychain (two storage patterns):**
   - **Project keychain service prefix** (`mlld-env-{projectname}` → `mlld-box-{projectname}`): Automatically updated when you use new CLI (service prefix changes in code)
   - **Flat environment storage** (`.mlld/env/{name}` with `mlld-env` namespace): No automatic migration:
     - Re-run `mlld box capture {name}` to recreate entries with new structure
     - Or manually re-add: `mlld keychain add {KEY_NAME}` for each entry

**Justification:** mlld 2.0 was the first public release (January 2025) with minimal adoption. Clean break preferred over maintaining backward compatibility shims.

**Critical: Policy layer dependency** - The keychain service prefix is used in TWO places:
1. **CLI storage** (`cli/commands/keychain.ts:11`) - where credentials are stored
2. **Policy expansion** (`core/policy/union.ts:34`) - where short-form `from: "keychain"` is expanded

Both MUST be updated together. If only CLI changes, policy short-form expansion will break (looking for `mlld-env-{projectname}` while CLI stores to `mlld-box-{projectname}`).

### Changes required

**CLI:**
- `mlld env capture` → `mlld box capture`
- `mlld env spawn` → `mlld box spawn`
- `mlld env shell` → `mlld box shell`
- `mlld env list` → `mlld box list`
- Factor out Claude-specific knowledge into a module template
- Factor out Codex-specific knowledge into a module template
- Update help text in `cli/interaction/HelpSystem.ts`
- Update directory paths: `.mlld/env/` → `.mlld/box/`
- Update keychain service prefix: `mlld-env-` → `mlld-box-` (in `cli/commands/keychain.ts:11`)
- Update keychain namespace: `mlld-env` → `mlld-box` (for flat storage)

**Registry modules (new):**
- `@mlld/agents/claude` — Claude Code config, auth setup, default CLAUDE.md
- `@mlld/agents/codex` — Codex config, auth setup (when ready)
- `@mlld/agents/base` — shared agent config utilities

**Keychain integration:**
- Extract keychain import/export as a standalone tool or directive
- Not agent-specific — any module can store/retrieve credentials

## Part 7: Git-based workspace hydration

### Syntax

```mlld
files <@workspace/> = git "https://github.com/user/repo" auth:@github-token
files <@workspace/> = git "https://github.com/user/repo" branch:"feature-x" auth:@github-token
files <@workspace/src/> = git "https://github.com/user/repo" path:"src/" auth:@token
```

### Semantics

`git` as a source in `files` clones a repository (or subtree) into the VFS:

1. Clone repo to a temp location (shallow clone for performance)
2. Walk file tree, reading each file as text
3. Write text content into VFS shadow layer
4. Clean up temp clone
5. All files carry `src:git` taint label
6. Auth flows through keychain reference

### Fidelity constraints

VirtualFS stores **string content only**. Git hydration is text-file-focused:

- **Text files**: loaded into VFS as-is
- **Binary files**: skipped by default (or base64-encoded with a `.binary` marker — TBD)
- **Symlinks**: not supported (VirtualFS has no symlink concept). Skipped with warning.
- **File permissions**: not preserved (VirtualFS has no permission model). Executable bit lost.

For full binary/symlink fidelity, use `box { provider: "@mlld/docker" }` with a real filesystem.

### Options

| Option | Required | Description |
|---|---|---|
| URL | yes | HTTPS or SSH git URL |
| `auth:` | depends | Keychain reference for private repos |
| `branch:` | no | Branch, tag, or commit (default: default branch) |
| `path:` | no | Subdirectory to clone (sparse checkout) |
| `depth:` | no | Clone depth (default: 1, shallow) |

### Built-in GitHub Authentication

mlld has built-in GitHub OAuth authentication for publishing. This auth is automatically available for git hydration:

```mlld
>> Use built-in GitHub auth (if user is logged in via `mlld auth login`)
files <@workspace/> = git "https://github.com/private-org/repo"

>> Or explicit keychain reference
files <@workspace/> = git "https://github.com/private-org/repo" auth:@github-token
```

**Authentication Resolution:**
1. If `auth:` specified → use that keychain reference
2. Else if repo is GitHub → check for built-in GitHub auth (service: `mlld-cli`, account: `github-token`)
3. Else if SSH URL → use SSH agent (no explicit auth needed)
4. Else → unauthenticated clone (public repos only)

**Credential Redaction:**
All taint sources and provenance entries MUST redact credentials:
- `src:git:https://github.com/user/repo` (token removed)
- `src:git:git@github.com:user/repo` (SSH key path not included)
- Guard logs and audit trails must not leak auth tokens

### Taint

All files loaded via `git` carry:
- `src:git` label
- `src:git:{remote-url}` source entry for provenance

```mlld
var @file = <@workspace/src/index.js>
show @file.mx.taint    >> ["src:git"]
show @file.mx.sources  >> ["git:https://github.com/acme/api@main"]
```

### Security & Determinism Rules

**Network Policy Enforcement:**
Git clones are network operations and MUST respect the environment's network policy:
- If `net: { allow: [] }` (network disabled) → git clone fails unless explicitly allowed
- Policy should support git URL patterns: `net: { allow: ["github.com", "gitlab.com"] }`
- Private repos require both network policy AND auth

**Shallow Clone Semantics:**
- Default `depth: 1` creates shallow clone (performance, reduces attack surface)
- Shallow clones fetch only the specified branch/commit, not full history
- For reproducibility, prefer explicit `branch: "v1.2.3"` (tag) over `branch: "main"` (moving target)

**Commit Pinning (Recommended):**
For maximum determinism, pin to specific commit SHAs:
```mlld
files <@workspace/> = git "https://github.com/user/repo"
  branch: "abc123def456..."  # Full commit SHA
  depth: 1
```

**Taint Propagation:**
Files from git carry `src:git` taint. When processed/transformed:
- `output @file { ... }` → output inherits `src:git` taint
- Guards can enforce: "no git-sourced files sent to external APIs"
- Policy can require: "git repos must be on allowlist"

### Error behavior

| Condition | Behavior |
|---|---|
| Clone fails (network, auth) | Runtime error with clear message |
| Binary file encountered | Skip, emit warning |
| Symlink encountered | Skip, emit warning |
| Empty repo | No files written, no error |

### Changes required

**Grammar:**
- `git` as a source expression in `files` RHS
- `auth:`, `branch:`, `path:`, `depth:` as named options

**Evaluator:**
- Shell out to `git` CLI for clone (handles SSH auth natively, no new dependency)
- File tree walking and VFS population (text files only)
- Taint labeling for git-sourced files
- Temp directory cleanup

## Part 8: Policy/Environment Config Alignment

### Context

Currently, `PolicyConfig` and `EnvironmentConfig` are separate structures with minimal bridging. Policy controls security (labels, capabilities, guards) while EnvironmentConfig controls execution (provider, tools, MCP servers). This separation makes it difficult to express security constraints as declarative policy.

### Target State: Policy as Environment Configuration Source

**Goal:** Make environment configuration fully expressible as policy, enabling a clear cascade:
```
Global Policy → Module Policy → Local Policy → Derived EnvConfig → Scoped Execution
```

**Cascade Rules:**
1. `exe params > env config > policy > default (no restrictions)`
2. Each level can only **attenuate** (restrict further), never expand
3. Policy enforcement ALWAYS applies (can't be superseded)
4. Policy can express all env config constraints declaratively

### Extend PolicyConfig

```typescript
type PolicyConfig = {
  // ... existing policy fields (labels, capabilities, guards, auth, keychain) ...

  // NEW: Environment constraints (subsumes EnvironmentConfig concerns)
  env?: {
    // Provider policy
    default?: string;              // Default provider reference
    providers?: {                  // Provider-specific constraints
      [providerRef: string]: {
        allowed?: boolean;         // Explicitly allow/deny provider
        auth?: string | string[];  // Required auth for provider
        taint?: DataLabel[];       // Labels to apply to provider output
        profiles?: Record<string, unknown>;  // Provider-specific profiles
      };
    };

    // Tool policy
    tools?: {
      allow?: string[];            // Allowed tools (if unset, all allowed)
      deny?: string[];             // Explicitly denied tools
      attenuation?: 'intersection' | 'union';  // How to merge with parent (default: intersection)
    };

    // MCP server policy
    mcps?: {
      allow?: string[];            // Allowed MCP servers
      deny?: string[];             // Denied MCP servers
    };

    // Network policy (existing, documenting for completeness)
    net?: {
      allow?: string[];            // Allowed domains/patterns
      deny?: string[];             // Denied domains/patterns
    };
  };
}
```

### Derive EnvironmentConfig from Policy

```typescript
function deriveEnvironmentConfigFromPolicy(
  policy: PolicyConfig,
  localConfig?: EnvironmentConfig
): EnvironmentConfig {
  const derived: EnvironmentConfig = { ...localConfig };

  // Apply provider default
  if (!derived.provider && policy.env?.default) {
    derived.provider = policy.env.default;
  }

  // Enforce provider constraints
  const providerRules = policy.env?.providers?.[derived.provider ?? ''];
  if (providerRules?.allowed === false) {
    throw new MlldError(`Provider ${derived.provider} denied by policy`);
  }

  // Apply tool attenuation (intersection of policy allow + requested tools)
  if (policy.env?.tools) {
    const policyAllowed = new Set(policy.env.tools.allow ?? []);
    const policyDenied = new Set(policy.env.tools.deny ?? []);
    const requestedTools = normalizeToolList(derived.tools);

    derived.tools = requestedTools.filter(t =>
      !policyDenied.has(t) &&
      (policyAllowed.size === 0 || policyAllowed.has(t))
    );
  }

  // Apply MCP attenuation (similar to tools)
  // ... (analogous logic for mcps)

  return derived;
}
```

### Migration Path

1. **Phase 1**: Extend `PolicyConfig.env` structure, implement derivation function
2. **Phase 2**: Update `evaluateBox()` to use derived configs, update guard envConfig resolution
3. **Phase 3**: Allow `policy` directives to express environment constraints
4. **Phase 4**: Guards can return policy fragments that merge into active policy

### Benefits

- **Unified security model**: All environment constraints expressible as policy
- **Declarative configuration**: Policy objects become composable, version-controlled security configs
- **Guard-enforced restrictions**: Guards can enforce environment restrictions via policy fragments
- **Clear cascade**: Security policy naturally flows down to execution environment
- **1:1 alignment**: Policy objects map directly to environment configurations

### Files to Modify

**Core types:**
- `core/policy/union.ts` — Extend PolicyConfig.env
- `core/types/environment.ts` — Add _policyDerivedConstraints field
- `core/types/guard.ts` — Add policy fragment return type to GuardAction

**Policy system:**
- `interpreter/eval/policy.ts` — Support extended policy syntax
- `core/policy/guards.ts` — Generate guards from env policy

**Guard system:**
- `grammar/directives/guard.peggy` — Add policy fragment action syntax (if needed)
- `interpreter/hooks/guard-runtime-evaluator.ts` — Handle policy fragment returns

**Environment system:**
- `interpreter/env/environment-provider.ts` — Implement derivation, update applyEnvironmentDefaults()
- `interpreter/eval/box.ts` — Use derived configs in evaluateBox() (renamed from env.ts in Phase 1)
- `interpreter/hooks/guard-runtime-evaluator.ts` — Allow policy fragments in guard results

**Documentation:**
- `docs/llm/POLICY.md` — Document env policy syntax
- `docs/src/atoms/security/` — Add examples of policy-based env config

## Implementation order

Dependencies are explicit. Each phase has a clear gate.

### Phase 1: `env` → `box` rename
Pure rename across grammar, types, evaluator, tests, docs, CLI. No new features.
**Gate:** all existing tests pass with `box` keyword.
**Depends on:** nothing.

### Phase 2: Workspace model + Environment integration
Add `workspaceStack` field to Environment. Add `pushActiveWorkspace()`, `popActiveWorkspace()`, and `getActiveWorkspace()` stack API. Define `WorkspaceValue` and `WorkspaceProvider` types. Add `workspaceProvider` to CommandExecutorFactory dependencies. Modify `CommandExecutorFactory.executeCommand()` to check active workspace at execution time and route through ShellSession if present. Create `executeInWorkingDirectory()` helper with try/finally for consistent stack semantics. Migrate all `resolveWorkingDirectory()` call sites to use helper.
**Gate:** commands inside a programmatically-created workspace route through ShellSession, workspace stack correctly handles nesting (box @outer [ cmd:@inner {...} ] restores @outer context).
**Depends on:** Phase 1.

### Phase 3: Unified write abstraction
Consolidate all write paths (output, append, pipeline effects) into one `executeWrite()` function with policy/audit/routing.
**Gate:** all existing write tests pass through new abstraction.
**Depends on:** Phase 1 (but independent of Phase 2).

### Phase 4: `file`/`files` directives
Grammar, AST, evaluator for file/files (named + anonymous forms). Resolver creation on first use. Path validation. Immutability enforcement.
**Gate:** `file`/`files` work both standalone and inside box blocks.
**Depends on:** Phase 3 (unified write abstraction).

### Phase 5: Configless box + VFS resolver shorthand
Grammar changes for `box [...]` (anonymous) and `box @resolver [...]` (shorthand). Evaluator creates VFS workspace from config. Sandbox defaults applied.
**Gate:** `box @resolver [...]` and `box [...]` parse, evaluate, and create ShellSession-backed workspaces.
**Depends on:** Phase 2 (workspace model), Phase 4 (resolvers from file/files).

### Phase 6: `cmd:@workspace`
Pre-interpolation type checking in `resolveWorkingDirectory()` to detect WorkspaceValue before string interpolation. ShellSession activation for workspace targets.
**Gate:** `exe @fn(ws) = cmd:@ws { ... }` works with WorkspaceValue arguments (from file/files or box blocks).
**Depends on:** Phase 2 (workspace model), Phase 4 (workspace-producing directives).

### Phase 7: Agent config extraction
Create `@mlld/agents/claude` and `@mlld/agents/codex` registry modules. Extract keychain tool. Update CLI commands.
**Gate:** `mlld box capture` generates a working module that replaces current `mlld env capture` flow.
**Depends on:** Phase 1 (rename), Phase 4 (file/files for config projection).

### Phase 8: Git hydration
`files <@ws/> = git "url"` syntax. Clone → VFS population. Taint labeling. Text-only fidelity. Built-in GitHub auth integration. Network policy enforcement.
**Gate:** private repo clone via auth into VFS workspace, taint labels applied, credentials redacted.
**Depends on:** Phase 4 (file/files directives).

### Phase 9: `.mx.edits`/`.mx.diff` + audit chain
Metadata extensions. Workspace-level diff. Taint reconstruction from audit log.
**Gate:** `.mx.edits` and `.mx.diff` return correct data for workspace and per-file scope.
**Depends on:** Phase 4 (file/files), Phase 2 (workspace model).

### Phase 10: Policy/Environment Alignment
Extend `PolicyConfig.env` to subsume environment constraints. Implement derivation function. Update evaluators to use policy-derived configs. Enable guards to return policy fragments.
**Gate:** Policy objects can fully express environment constraints; guards can enforce env restrictions via policy.
**Depends on:** Phase 1-9 (all box features implemented).
