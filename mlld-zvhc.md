---
id: mlld-zvhc
status: open
deps: []
links: []
created: 2026-01-12T23:22:18.599888-08:00
type: task
priority: 2
---
# Destructured import shows null when printed but works when called

When using `import { @claude } from @mlld/claude`, the variable @claude displays as "null" in show statements, but actual function calls work correctly.

## Reproduction

```mlld
import { @claude } from @mlld/claude
show `claude: @claude`  >> prints "claude: null"
var @result = @claude("Say hi", "haiku", @base, "")
show `Result: @result`  >> works, prints actual response
```

## Expected
@claude should display as something like "<function()>" or "<executable>" when printed.

## Actual
Displays as "null", which is confusing and led to debugging in wrong direction.


