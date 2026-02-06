---
id: m-eea6
status: closed
deps: []
created: 2026-02-02T19:25:43Z
type: task
priority: 2
assignee: Adam
tags: [urgency-critical, bug, interpreter]
updated: 2026-02-02T20:57:44Z
---
# BUG: for...=> loses file metadata (.mx.filename becomes null)

## Summary

When using `for @item in @files => @item` to build a result array from file globs, the `.mx` metadata (filename, path, etc.) is lost. The content is preserved but `.mx.filename` returns null.

## Reproduction

```mlld
var @jobs = <@base/j2bd/security/jobs/*.md>

// Direct iteration - metadata preserved
for @j in @jobs [
  show @j.mx.filename  // Works: "audit-guard-pattern.md"
]

// Map to array - metadata LOST
var @mapped = for @j in @jobs => @j
for @m in @mapped [
  show @m.mx.filename  // Returns null!
]

// Filter with when - also loses metadata
var @filtered = for @j in @jobs when @j.mx.filename == "foo.md" => @j
// @filtered[0].mx.filename is null
```

## Impact

Cannot filter file lists and preserve metadata. The j2bd orchestrator's job filtering was broken because of this.

## Workaround

Find the index and access original array:
```mlld
exe @findIndex(items, target) = [
  let @idx = 0
  for @i, @item in @items [
    when @item.mx.filename == @target => [
      let @idx = @i
      done
    ]
  ]
  => @idx
]
var @idx = @findIndex(@files, "target.md")
var @file = @files[@idx]  // Preserves metadata
```

## Location

Likely in for-loop result building or value copying logic.

