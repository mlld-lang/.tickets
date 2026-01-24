---
id: mlld-qxb
status: closed
deps: [mlld-b0e, mlld-zgb, mlld-rn5, mlld-u8r, mlld-1x2, mlld-e0b, mlld-7e7, mlld-k69]
links: []
created: 2025-12-08T11:49:05.80685-08:00
type: task
priority: 2
assignee: codex
---
# cmd:path / sh:path for rooted commands

One thing that both `cmd` and `sh` often need is to set the path for their operation. 

`cmd` intentionally disallows chaining which makes it impossible to just do `cd /some/dir && mycommand`

But given this is a common use case—and one which enforces security and clarity—we should allow a way to set the path for `cmd` and `sh`

```
/run cmd:/Users/adam/dev/mlld {mlld setup}
/run sh:@mypath {echo "hello world"}
/run cmd:@base/path/to/file {mycommand param}
/run sh:/Users/@username/dev/@dir {somecommand --flag}
```

the `:path` would be an option everywhere `sh` and `cmd` are used; if not set the path would be set to whatever the system defaults to currently (which has its own rules)

## Design

## Design Overview

This feature adds `:path` syntax to `cmd` and language executors (`sh`, `bash`, `js`, `python`, etc.) to set the working directory for command execution.

## Motivation

Currently, `cmd` intentionally disallows chaining (no `&&` inside `cmd {...}`), which makes it impossible to do:
```
/run cmd {cd /some/dir && mycommand}  # NOT ALLOWED
```

The `:path` syntax provides a clean, explicit alternative that:
1. Enforces security through validation
2. Makes working directory changes explicit in the syntax
3. Supports variable interpolation
4. Maintains cmd's no-chaining principle

## Syntax Examples

```mlld
# Absolute path
/run cmd:/Users/adam/dev/mlld {mlld setup}

# Variable path
/var @mypath = "/tmp"
/run sh:@mypath {echo "hello world"}

# Interpolated path
/var @base = "/Users/adam"
/run cmd:@base/dev/mlld {git status}

# With multiple variables
/run sh:/Users/@username/dev/@project {make test}

# Works with all language executors
/run bash:/tmp {pwd}
/run js:/home/user/scripts {console.log(process.cwd())}
/run python:@workspace {import os; print(os.getcwd())}
```

## Design Decisions

### 1. Rooted Paths Only

Paths MUST be rooted (absolute):
- ✅ `/tmp` - absolute path
- ✅ `@mypath` - variable (must resolve to absolute)
- ✅ `@base/subdir` - variable + path (variable must be absolute)
- ❌ `./relative` - REJECTED
- ❌ `relative` - REJECTED

**Rationale**: Relative paths are ambiguous (relative to what?). Rooted paths are explicit and secure.

### 2. Variable Interpolation

Variables in paths are interpolated at runtime using existing `interpolate()` infrastructure:
```mlld
/var @user = "alice"
/run cmd:/home/@user/work {ls}  # Resolves to /home/alice/work
```

**Rationale**: Consistent with how variables work elsewhere in mlld.

### 3. Fail Fast Validation

Path validation happens BEFORE command execution:
1. Interpolate variables (error if undefined)
2. Check path starts with `/` (error if not)
3. Check directory exists (error if not)
4. Check is directory not file (error if file)

**Rationale**: Better error messages, prevents confusing command failures.

### 4. No Security Policy (Yet)

Initial implementation allows ANY absolute path the user has filesystem access to. Security policy integration is deferred to mlld-bgl.

**Future**: Policy will allow restricting to specific path prefixes:
```mlld
/needs cmd.cwd:/Users/adam/dev/**  # Only allow paths under this
```

### 5. Scope is Per-Command

The `:path` only affects the specific command it's attached to, not global state:
```mlld
/run cmd:/tmp {pwd}        # runs in /tmp
/run cmd {pwd}             # runs in default cwd
/run @data | cmd:/app {process.sh}  # only process.sh runs in /app
```

**Rationale**: Explicit scope prevents action-at-a-distance bugs.

## Implementation Order

The subtasks have dependencies:

**Phase 1: Foundation (can be parallel)**
1. mlld-u8r - Types (no dependencies)
2. mlld-b0e - Grammar: WorkingDirPath pattern

**Phase 2: Grammar Integration (depends on phase 1)**
3. mlld-zgb - Grammar: cmd support (depends on mlld-b0e)
4. mlld-rn5 - Grammar: sh/bash support (depends on mlld-b0e)

**Phase 3: Runtime (depends on phase 1-2)**
5. mlld-1x2 - cwd-resolver utility (depends on mlld-u8r)
6. mlld-e0b - run evaluator (depends on mlld-1x2, mlld-u8r)
7. mlld-7e7 - executors (depends on mlld-e0b)

**Phase 4: Testing (depends on all)**
8. mlld-k69 - comprehensive tests

## Technical Architecture

```
Grammar Layer:
  WorkingDirPath pattern → parses :path syntax
  ↓
  CmdCommandBrackets / RunLanguageCodeCore → includes workingDir in AST
  ↓
AST:
  values.workingDir: ContentNodeArray (nodes with variables)
  raw.workingDir: string (raw text)
  meta.hasWorkingDir: boolean
  meta.workingDirMeta: PathMeta
  ↓
Interpreter:
  run evaluator → checks for workingDir in AST
  ↓
  cwd-resolver → interpolates variables, validates path
  ↓
  executeCommand/executeCode → passes cwd in options
  ↓
Executors:
  ShellCommandExecutor / BashExecutor / etc → use options.cwd || this.workingDirectory
```

## Error Handling

Clear, actionable error messages:

```
Error: Cannot resolve working directory path
  Path: /home/@username/dev
  Reason: Variable @username is not defined
  Location: example.mld:5:10
```

```
Error: Working directory must be an absolute path (start with /)
  Provided: ./relative
  Location: example.mld:8:15
```

```
Error: Working directory does not exist
  Path: /nonexistent/directory
  Location: example.mld:12:10
```

## Testing Strategy

1. **Grammar tests**: Verify AST structure for various syntax forms
2. **Valid cases**: Test execution with different path types
3. **Invalid cases**: Test parse errors for unsupported syntax
4. **Exception cases**: Test runtime errors for undefined vars, bad paths
5. **Integration**: Test with loops, pipelines, different executors

## Notes

Decisions: '/' allowed; paths must be absolute Unix; no Windows or '~'; reuse existing missing/non-dir path errors at runtime; :path applies to cmd/sh/etc including /exe forms like /exe @run(path, foo) = cmd:@path {...}.


