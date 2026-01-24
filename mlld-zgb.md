---
id: mlld-zgb
status: closed
deps: []
links: []
created: 2025-12-08T12:52:13.204118-08:00
type: task
priority: 2
assignee: codex
---
# Grammar: Extend CmdCommandBrackets to support optional :path

## Context
Part of implementing cmd:path/sh:path feature. This task extends the `CmdCommandBrackets` pattern to accept the optional `:path` suffix.

## Prerequisites
- mlld-b0e must be complete (WorkingDirPath pattern exists)

## Task
Modify `CmdCommandBrackets` to optionally parse and include working directory path.

## Implementation

Location: `grammar/patterns/unified-run-content.peggy`

Current code (around line 84-87):
```peggy
CmdCommandBrackets "cmd command brackets"
  = "cmd" _ content:UnifiedCommandBrackets {
      return content;
    }
```

Change to:
```peggy
CmdCommandBrackets "cmd command brackets"
  = "cmd" workingDir:WorkingDirPath? _ content:UnifiedCommandBrackets {
      const result = content;
      if (workingDir) {
        result.values.workingDir = workingDir.parts;
        result.raw.workingDir = workingDir.raw;
        result.meta.hasWorkingDir = true;
        result.meta.workingDirMeta = workingDir.meta;
      }
      return result;
    }
```

## What This Does

1. Optionally matches `WorkingDirPath` after `cmd` keyword
2. If present, adds to the result node:
   - `values.workingDir` - AST node array for interpreter
   - `raw.workingDir` - raw string for debugging/display
   - `meta.hasWorkingDir` - boolean flag
   - `meta.workingDirMeta` - path metadata (hasVariables, etc)

## Testing

After implementation:
```bash
npm run ast -- '/run cmd:/tmp {ls}'
npm run ast -- '/run cmd:@mypath {pwd}'
npm run ast -- '/run cmd {ls}'  # without path still works
```

Check the AST output has `workingDir` field in values/raw/meta when path is present.

## Notes

Cmd :path grammar uses WorkingDirPath pattern; accepts absolute Unix paths only ('/' ok); no Windows or '~'.


