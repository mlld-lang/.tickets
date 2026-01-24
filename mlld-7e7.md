---
id: mlld-7e7
status: closed
deps: []
links: []
created: 2025-12-08T12:52:42.69404-08:00
type: task
priority: 2
assignee: codex
---
# Executors: Add cwd support to CommandExecutionOptions and executors

## Context
Part of implementing cmd:path/sh:path feature. This task adds `cwd` support to the command executor options and modifies all executors to use it.

## Prerequisites
- mlld-e0b in progress or complete (run evaluator passing cwd)

## Task
1. Add `cwd?: string` to `CommandExecutionOptions` interface
2. Modify all executors to use `options.cwd` when provided

## Implementation

### Step 1: Update CommandExecutionOptions interface

Location: `interpreter/env/executors/BaseCommandExecutor.ts` (or wherever CommandExecutionOptions is defined)

Find the interface (likely around line 10-20):
```typescript
export interface CommandExecutionOptions {
  env?: Record<string, string>;
  timeout?: number;
  captureOutput?: boolean;
  // ... other options
  cwd?: string;  // NEW: Optional working directory
}
```

### Step 2: Modify ShellCommandExecutor

Location: `interpreter/env/executors/ShellCommandExecutor.ts`

Find where `spawn` is called (likely around line 340):

**Before:**
```typescript
const child = spawn(safeCommand, {
  cwd: this.workingDirectory,
  env,
  shell: true,
  // ...
});
```

**After:**
```typescript
const child = spawn(safeCommand, {
  cwd: options?.cwd || this.workingDirectory,  // Use options.cwd if provided
  env,
  shell: true,
  // ...
});
```

Also find the `execAsync` call (around line 297) and update similarly:
```typescript
const { stdout, stderr } = await execAsync(finalCommand, {
  encoding: 'utf8',
  cwd: options?.cwd || this.workingDirectory,  // Use options.cwd if provided
  env: { ...process.env, ...(options?.env || {}) },
  maxBuffer: 10 * 1024 * 1024
});
```

### Step 3: Modify BashExecutor

Location: `interpreter/env/executors/BashExecutor.ts`

Apply the same pattern - find spawn calls and update cwd:
```typescript
cwd: options?.cwd || this.workingDirectory
```

### Step 4: Modify other executors

Check and update these if they execute processes:
- `NodeExecutor.ts` - for `/run js {}`
- `PythonExecutor.ts` - for `/run python {}`
- `JavaScriptExecutor.ts` - if separate from NodeExecutor

For each, find where child processes are spawned and apply:
```typescript
cwd: options?.cwd || this.workingDirectory
```

## Pattern

The pattern is consistent across all executors:
1. Check if `options?.cwd` is provided
2. If yes, use it
3. If no, fall back to `this.workingDirectory` (existing behavior)

## Testing

After implementation, test that cwd is actually used:

```bash
echo '/run cmd:/tmp {pwd}' | ./dist/cli.cjs
# Should output: /tmp

echo '/run sh:/home {pwd}' | ./dist/cli.cjs  
# Should output: /home (if exists)
```

Check that commands without cwd still work:
```bash
echo '/run cmd {pwd}' | ./dist/cli.cjs
# Should output: current working directory
```


