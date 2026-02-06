---
id: mlld-bgl
status: open
deps: []
links: []
created: 2025-12-08T12:53:10.831611-08:00
type: task
priority: 3
tags: [size-m, complexity-m, risk-m, impl-none, needs-human-design]
updated: 2026-01-30T22:45:10Z
---
# Security: Policy-based working directory path restrictions

Add security policy support for restricting working directory paths in cmd:path/sh:path syntax.

## Background
Currently cmd:path and sh:path allow any absolute path the user has filesystem access to. We need policy-based restrictions to:
- Define allowed path prefixes (e.g., only /Users/adam/dev/)
- Prevent access to sensitive directories (/etc, /root, etc.)
- Support guards that test path compliance before execution

## Requirements
1. Extend security policy to include working directory constraints
2. Add path validation against policy in cwd-resolver
3. Create guards that can inspect and approve/deny workingDir
4. Clear error messages when path violates policy

## Examples
Policy:
```
/needs cmd.cwd:/Users/adam/dev/**
```

Guard:
```
/guard [cmd.cwd => {
  # Validate working directory is within allowed paths
}]
```

This issue should be tackled after the basic cmd:path/sh:path feature is implemented and working.


