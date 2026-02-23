---
id: m-bd0e
status: closed
deps: []
created: 2026-02-22T17:20:03Z
type: feature
priority: 0
updated: 2026-02-22T18:48:46Z
---
# sh(@var) {} syntax should work in exe context, not just run context

## Current behavior

```mlld
exe @deploy(target) = sh(@target) {
  rsync -av ./dist/ "$target"
}
```

Fails with "Invalid exe syntax". The `sh(@var) { }` syntax only works in `run` context (`run sh(@var) { }`).

## Expected behavior

`sh(@var) { }` should be valid as an exe body, same as it is in `run` context. The grammar needs to accept parameterized `sh` blocks in exe definitions.

