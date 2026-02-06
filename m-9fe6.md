---
id: m-9fe6
status: closed
deps: []
created: 2026-02-02T22:06:07Z
type: task
priority: 2
assignee: Adam
updated: 2026-02-02T22:12:38Z
---
# @base variable not interpolated in sh blocks

## Bug

When using `@base` inside a sh block, it doesn't interpolate correctly.

## Reproduction

```mlld
var @eventsFile = \`@base/tmp/test-events/events.jsonl\`
var @lines = sh { cat "$eventsFile" }
```

Results in: `cat: : No such file or directory`

The `@base` in the template literal doesn't resolve, so the path is empty.

## Expected

`@base` should resolve to the repo root in template literals that are then used in sh blocks.

## Workaround

Use absolute paths instead of `@base`.

