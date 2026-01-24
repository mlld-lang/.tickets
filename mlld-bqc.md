---
id: mlld-bqc
status: closed
deps: []
links: []
created: 2025-12-17T17:26:24.546183-08:00
type: feature
priority: 2
---
# Add file/directory existence checks

Add general-purpose @exists() builtin that returns true if expression evaluates without error.

**Design:**
```mlld
@exists("file.md")              // String path - interpolates first
@exists("@base/dir/path")       // Resolver paths work
@exists(<file.md>)               // File load - true if loads
@exists(<dir/*.md>)              // Glob - true if any match
@exists(@var[@key])              // Array/object access
@exists(@obj.field.nested)       // Field access
```

**Semantics:**
- Evaluates the expression
- Returns true if no error, false if any error
- For globs, true if at least one file matches
- Catches ALL errors (ENOENT, EACCES, timeout, etc) and returns false
- Side effects happen (file actually loads in @exists(<file>))

**Implementation:**
- Add as builtin in interpreter/eval/exec-invocation.ts
- Wrap expression evaluation in try/catch
- For string args, treat as file paths and attempt load via <...> syntax

**Test cases needed:**
- @exists("existing.md") and @exists("missing.md")
- @exists("@base/tmp/file.md") with resolver
- @exists(<file.md>) with actual file load
- @exists(<dir/*.md>) with glob (true if any match)
- @exists(<dir/*.md>) with glob (false if no matches)
- @exists(@obj.field) existing vs missing field
- @exists(@arr[99]) out of bounds array access

**Follow-up:** mlld-??? (P4) for performance optimization - lazy fs.exists() for file paths


