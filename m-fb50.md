---
id: m-fb50
status: closed
deps: []
created: 2026-02-02T06:07:14Z
type: task
priority: 0
assignee: Adam
tags: [bug, shell, interpreter]
updated: 2026-02-12T15:13:32Z
---
# Path objects passed to sh blocks should stringify to path

When a Path object (like @base) is passed as a parameter to an exe that uses sh {}, it gets JSON stringified instead of converting to the path string.

Example:
```mlld
exe @foo(dir) = sh { cd "$dir" && pwd }
var @result = @foo(@base)  >> Fails: cd tries to enter JSON object
```

Expected: $dir should receive "/Users/adam/dev/mlld"
Actual: $dir receives '{"resolvedPath":"/Users/adam/dev/mlld",...}'

Workaround: Pass "@base" (interpolated string) instead of @base

This should probably auto-stringify Path objects when injecting into shell context, similar to how template interpolation works.

