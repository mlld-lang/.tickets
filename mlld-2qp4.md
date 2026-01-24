---
id: mlld-2qp4
status: open
deps: []
links: []
created: 2026-01-04T13:58:42.973129-08:00
type: bug
priority: 3
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


