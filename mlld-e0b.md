---
id: mlld-e0b
status: closed
deps: []
links: []
created: 2025-12-08T12:52:35.93044-08:00
type: task
priority: 2
assignee: codex
---
# Interpreter: Modify run evaluator to resolve and pass cwd

## Context
Part of implementing cmd:path/sh:path feature. This task modifies the run evaluator to check for workingDir in the AST, resolve it, and pass it to command executors.

## Prerequisites
- mlld-1x2 complete (cwd-resolver exists)
- mlld-u8r complete (workingDir types defined)
- Grammar tasks complete (workingDir in AST)

## Task
Modify `interpreter/eval/run.ts` to handle the optional `workingDir` field from the AST.

## Implementation

Location: `interpreter/eval/run.ts`

### Step 1: Import the resolver

Add import at top of file:
```typescript
import { resolveCwd } from '../utils/cwd-resolver';
```

### Step 2: Find command execution locations

Look for calls to:
- `env.executeCommand()`
- `env.executeCode()`

These are likely in functions like:
- `evaluateRunCommand()` - for `cmd {...}`
- `evaluateRunCode()` - for `sh {...}`, `bash {...}`, etc.

### Step 3: Add cwd resolution before execution

For each execution point, add cwd resolution:

```typescript
// Check if workingDir is present in AST
let resolvedCwd: string | undefined;
if (node.values?.workingDir) {
  resolvedCwd = await resolveCwd(
    node.values.workingDir,
    env,
    node.location
  );
}

// Pass to executor
const result = await env.executeCommand(command, {
  ...existingOptions,
  cwd: resolvedCwd
});
```

### Step 4: Handle both cmd and code execution

Make sure both paths are covered:

**For cmd execution:**
```typescript
const result = await env.executeCommand(command, {
  env: envVars,
  cwd: resolvedCwd,  // NEW
  // ... other options
});
```

**For code execution (sh, bash, js, python):**
```typescript
const result = await env.executeCode(language, code, {
  args: resolvedArgs,
  cwd: resolvedCwd,  // NEW
  // ... other options
});
```

## Edge Cases

1. **workingDir is optional**: Only resolve if present in AST
2. **Error propagation**: Let `resolveCwd` errors bubble up naturally
3. **Pipeline context**: Each command gets its own cwd resolution

## Testing

After implementation, test with actual mlld scripts:

```bash
echo '/var @mypath = "/tmp"
/run cmd:@mypath {pwd}' | ./dist/cli.cjs

# Should output: /tmp
```

```bash
echo '/run cmd:/nonexistent {ls}' | ./dist/cli.cjs

# Should error: Working directory does not exist
```


