---
id: m-8ab7
status: closed
deps: [m-fcb4]
links: [m-5451, m-d4e5]
created: 2026-03-04T06:54:01Z
type: bug
priority: 0
assignee: Adam
tags: [docs, box, accuracy]
updated: 2026-03-04T07:24:08Z
---
# box doc examples use unnecessary @root and have inaccurate .mx descriptions

## Issues

### 1. Unnecessary @root in box command examples

`docs/src/atoms/config/11-box--directive.md` lines 84, 95 and `13-box--blocks.md` lines 31, 43, 55:

```mlld
box [
  file "task.md" = "inside box"
  run cmd { cat @root/task.md }
]
```

The `@root` prefix is unnecessary — ShellSession cwd is set to project root, so `cat task.md` works. QA confirmed both forms work, but `@root` adds noise and makes it look like a special requirement. Use bare relative paths in examples.

### 2. show <file>.mx.diff does not parse (blocked by m-fcb4)

`13-box--blocks.md` line 60:

```mlld
show <@root/task.md>.mx.diff
```

Does not parse — `show` directive doesn't support field access on `<...>` expressions (see m-fcb4). Once m-fcb4 is fixed, this example will work. Until then, use workaround in docs:

```mlld
var @file = <@workspace/task.md>
show @file.mx.diff
```

### 3. .mx.diff vs .mx.edits return values are vague

`13-box--blocks.md` lines 63-65 describe:
- `.mx.edits` — "returns the workspace change list" (correct)
- `.mx.diff` — "returns workspace diff inspection output" (vague)

In practice:
- Workspace-level `.mx.diff` returns the same change list as `.mx.edits` (array of `{path, type, entity}`)
- Per-file `.mx.diff` returns a unified diff string (`--- /dev/null\n+++ b/path\n...`)

Docs should be precise about what each returns and distinguish workspace-level from per-file behavior.

## Fix

1. Remove `@root` from box command examples — use bare relative paths
2. Fix or work around the `show <file>.mx.diff` example (depends on m-fcb4)
3. Clarify `.mx.diff` return value descriptions (workspace-level vs per-file)
4. Rebuild LLM docs: `mlld run llmstxt`
