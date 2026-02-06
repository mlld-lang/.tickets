---
id: mlld-lm6t
status: closed
deps: []
links: []
created: 2026-01-22T21:19:09.322228-08:00
type: task
priority: 2
tags: [size-xs, complexity-xs, risk-xs, impl-none]
updated: 2026-01-30T12:09:42Z
---
# Glob pattern **/ does not work for recursive directory matching

In file reference glob patterns like `<@base/qa/wt-cleanup/**/assessment.json>`, the `**/` pattern does not match files recursively.

**Expected:** `**/` should match files at any depth (like standard glob behavior)
**Actual:** Returns 0 matches

**Workaround:** Use explicit depth patterns:
```mlld
var @single = <@qaDir/*/file.json>
var @nested = <@qaDir/*/*/file.json>
var @all = @single.concat(@nested)
```

Discovered while building wt-cleanup.mld orchestration script.


