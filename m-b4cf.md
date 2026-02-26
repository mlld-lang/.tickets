---
id: m-b4cf
status: closed
deps: []
created: 2026-02-25T06:21:41Z
type: bug
priority: 0
assignee: Adam Avenir
updated: 2026-02-25T17:42:14Z
---
# Validator false positives for @p, resolver vars, hook names, op:log

## Bug

`mlld validate` produces false "undefined variable" and "unknown operation" warnings for valid code. Four distinct cases:

### Case 1: @p in pipelines
```mlld
var @result = @items | for @item => @item.name
```
Validator flags `@p` (pipeline context variable) as "undefined variable".
**Fix:** Register `@p` as a known variable when the validator is inside a pipeline context.

### Case 2: Resolver prefix path variables
```mlld
>> Given mlld-config.json with resolver prefix @lib/ -> ./src/lib
import "@lib/utils" as @utils
```
Validator flags `@lib` as "undefined variable".
**Fix:** Read resolver prefixes from mlld-config.json and register their prefix variables as known in the validator.

### Case 3: Hook name declarations
```mlld
hook @myHook before @someFn = [...]
```
Validator flags `@myHook` as "undefined variable".
**Fix:** Treat the name position in `hook @name ...` as a variable definition, not a reference.

### Case 4: op:log unknown operation
```mlld
policy @p = { defaults: { rules: ["op:log"] } }
```
Validator flags `op:log` as "unknown operation type".
**Fix:** Add `"log"` to the list of known operation types in the validator.

## Acceptance criteria

1. `mlld validate` on a file using `@p` inside a pipeline produces zero warnings about `@p` being undefined
2. `mlld validate` on a file importing via a configured resolver prefix produces zero warnings about the prefix variable being undefined
3. `mlld validate` on a file with `hook @name before ...` produces zero warnings about `@name` being undefined
4. `mlld validate` on a file with `op:log` in a policy produces zero warnings about unknown operation type
5. `mlld validate` on a file with a genuinely undefined variable (e.g., `show @doesNotExist`) still correctly warns (regression check)
6. All existing tests pass: `npm test`

## Where to look

The validator code (search for "validate" in src/ or cli/). Find where known variables are registered and where operation types are checked. Each of the four fixes is a small, independent change to the same validation subsystem.
