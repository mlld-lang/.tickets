---
id: mlld-2qp4
status: closed
deps: []
links: []
created: 2026-01-04T13:58:42.973129-08:00
type: bug
priority: 3
tags: [size-xs, complexity-xs, risk-xs, impl-none]
updated: 2026-01-31T07:58:01Z
---
# Grammar: for directive error message incorrectly says => is required

## Issue

Error message says:
```
Missing '=>' in /for directive. Expected: /for @var in @collection => action
```

But block syntax is valid too:
```mlld
for @x in @xs [
  show @x
]
```

## Expected
Error message should mention both forms:
```
Expected: for @var in @collection => action  OR  for @var in @collection [...]
```

## Notes

**2026-01-30**: Verified still valid. Error message still shows only `=> action` form, doesn't mention block syntax `[...]`.

**2026-01-31 07:57 UTC:** Fixed. Error messages for 'for' directive now mention both syntax forms: '=> action' and '[...]' block syntax. Updated three error patterns in grammar/directives/for.peggy.
