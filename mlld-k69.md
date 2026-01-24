---
id: mlld-k69
status: closed
deps: []
links: []
created: 2025-12-08T12:52:49.842606-08:00
type: task
priority: 2
assignee: codex
---
# Tests: Add comprehensive test coverage for cmd:path/sh:path

## Context
Part of implementing cmd:path/sh:path feature. This task creates comprehensive test coverage for the new syntax.

## Prerequisites
- All implementation tasks complete (grammar, types, interpreter, executors)

## Task
Create test cases covering valid syntax, invalid syntax, runtime exceptions, and edge cases.

## Test Structure

mlld uses a fixture-based test system:
- `tests/cases/` - valid test cases
- `tests/cases/invalid/` - syntax/parse errors
- `tests/cases/exceptions/` - runtime errors
- `tests/fixtures/` - generated fixture files (gitignored)

### Test File Naming

**CRITICAL**: Test names must be unique across ALL tests. Use prefixes like:
- `cmd-path-absolute.md` 
- `cmd-path-variable.md`
- `sh-path-interpolation.md`

NOT just `path.md` or `test.md`!

## Valid Test Cases

Location: `tests/cases/cmd-path/` (create new directory)

### 1. cmd-path-absolute.md
```markdown
/run cmd:/tmp {pwd}
```

expected.md:
```
/tmp
```

### 2. cmd-path-variable.md
```markdown
/var @mypath = "/tmp"
/run cmd:@mypath {pwd}
```

expected.md:
```
/tmp
```

### 3. cmd-path-interpolation.md
```markdown
/var @base = "/tmp"
/var @subdir = "test"
/run cmd:@base/@subdir {pwd}
```

expected.md:
```
/tmp/test
```

### 4. sh-path-absolute.md
```markdown
/run sh:/tmp {pwd}
```

### 5. bash-path-variable.md
```markdown
/var @dir = "/tmp"
/run bash:@dir {pwd}
```

### 6. cmd-path-loop-iteration.md
```markdown
/var @dirs = ["/tmp", "/home"]
/for @dir in @dirs {
  /run cmd:@dir {pwd}
}
```

expected.md:
```
/tmp
/home
```

### 7. cmd-no-path-still-works.md
Test that existing cmd without :path still works:
```markdown
/run cmd {pwd}
```

## Invalid Test Cases (Parse Errors)

Location: `tests/cases/invalid/cmd-path/`

### 1. cmd-path-invalid-relative.md
```markdown
/run cmd:./relative {ls}
```

error.md:
```
Parse error: working directory must start with / or @
```

### 2. cmd-path-invalid-bare.md
```markdown
/run cmd:relative {ls}
```

Note: This might parse successfully but fail at runtime if it doesn't start with `/` or `@`. Adjust test category as needed.

## Exception Test Cases (Runtime Errors)

Location: `tests/cases/exceptions/cmd-path/`

### 1. cmd-path-exception-undefined-var.md
```markdown
/run cmd:@undefined {pwd}
```

error.md:
```
Variable @undefined is not defined
```

### 2. cmd-path-exception-not-rooted.md
```markdown
/var @rel = "relative"
/run cmd:@rel {pwd}
```

error.md:
```
Working directory must be an absolute path
```

### 3. cmd-path-exception-not-exists.md
```markdown
/run cmd:/does/not/exist/path {ls}
```

error.md:
```
Working directory does not exist
```

### 4. cmd-path-exception-not-directory.md
```markdown
/run cmd:/etc/hosts {ls}
```

error.md:
```
Working directory path is not a directory
```

## Grammar Tests

Location: `grammar/tests/run.test.ts`

Add test cases to verify AST structure:

```typescript
describe('cmd:path syntax', () => {
  it('should parse cmd with absolute path', () => {
    const result = parse('/run cmd:/tmp {ls}');
    expect(result.values.workingDir).toBeDefined();
    expect(result.raw.workingDir).toBe('/tmp');
  });
  
  it('should parse cmd with variable path', () => {
    const result = parse('/run cmd:@mypath {ls}');
    expect(result.values.workingDir).toHaveLength(1);
    expect(result.values.workingDir[0].type).toBe('VariableReference');
  });
  
  it('should parse sh with interpolated path', () => {
    const result = parse('/run sh:@base/subdir {pwd}');
    expect(result.values.workingDir).toHaveLength(2); // variable + text
    expect(result.meta.hasWorkingDir).toBe(true);
  });
});
```

## Running Tests

```bash
# Generate fixtures
npm run build:fixtures

# Run specific test directory
npm run test:case -- cmd-path

# Run all tests
npm test
```

## Acceptance Criteria

- [ ] All valid syntax parses and executes correctly
- [ ] Undefined variables produce clear errors
- [ ] Non-rooted paths are rejected
- [ ] Non-existent directories are detected
- [ ] File paths (not directories) are rejected  
- [ ] Loop iterations work with different paths
- [ ] Existing cmd/sh without :path still works
- [ ] Grammar tests verify AST structure


