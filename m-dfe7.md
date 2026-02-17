---
id: m-dfe7
status: closed
deps: []
created: 2026-02-17T17:13:58Z
type: bug
priority: 0
assignee: Adam
tags: [variables, dx, reserved-variables]
updated: 2026-02-17T17:47:16Z
---
# @root vs @base inconsistency: @root doesn't interpolate in templates

## Problem

`@root` and `@base` are documented as aliases (`mlld howto reserved-variables`), but they behave differently:

- `@base` works as a variable in backtick templates: `` `@base/path` `` → `/Users/adam/dev/mlld/path`
- `@root` does NOT interpolate in templates: `` `@root/path` `` → literal `@root/path`
- `@root` DOES work as a path resolver in file loading: `<@root/path>` resolves correctly
- `@base` also works in file loading: `<@base/path>` resolves correctly

This means `@root` is a file-loading resolver but not a variable, while `@base` is both. The docs say `@root` is "preferred" and `@base` is the "alias", but in practice `@base` is more capable.

## Reproduction

```mld
show `root: @root`
show `base: @base`
```

Output:
```
root: @root
base: /Users/adam/dev/mlld
```

## Impact

This caused real confusion when building orchestrators. The orchestrator skill uses `"@root"` for the `@claudePoll` dir parameter (following the reserved-variables doc), but this produces the literal string `@root` instead of the resolved path. Scripts silently fail because `cd "@root"` in the shell block finds no such directory.

## Expected behavior

Either:
- Make `@root` resolve as a variable in all contexts (templates, expressions), same as `@base`
- Or update docs to reflect that `@base` is the actual variable and `@root` is only a file-loading resolver

## Files

- `interpreter/` — wherever reserved variables are registered
- `docs/src/atoms/syntax/reserved-variables.md` — docs say @root is "preferred"


**2026-02-17 17:47 UTC:** Initialized @root as a reserved project-root path variable alongside @base in VariableManager; added CLI integration regression coverage for direct @root output and backtick interpolation parity; tests: npx vitest run tests/integration/cli/base-path-resolution.test.ts and full npm test; commit 303570997.
