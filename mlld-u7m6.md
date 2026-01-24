---
id: mlld-u7m6
status: closed
deps: []
links: []
created: 2025-12-20T19:42:51.758429-08:00
type: feature
priority: 2
---
# Better error for cmd:. (dot path)

When user writes `cmd:. { ... }`, the error is:
```
Working directory must start with "/"
```

Should say something like:
```
'.' is unnecessary - commands run in the current directory by default. Use `cmd { ... }` without a path suffix.
```

Reported by @partydev.


