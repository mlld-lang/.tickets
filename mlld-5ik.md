---
id: mlld-5ik
status: closed
deps: [mlld-jw7]
links: []
created: 2025-12-09T22:20:01.49389-08:00
type: task
priority: 2
---
# Phase 4: Integration Tests and Polish (Optional)

## Summary

Extended integration tests and polish after core features work. Optional but recommended for production readiness.

## Prereq

Phase 3 core tests (mlld-jw7) must pass first.

## Comprehensive Test Cases

### Test A: Exe Block with Nested For and When

```
/exe @summarize(projects) = [
  let @out = []
  let @errors = []

  for @project in @projects [
    let @rows = []

    for @task in @project.tasks [
      when [
        @task.state == "done" => let @rows += { id: @task.id, pts: @task.points }
        @task.state == "blocked" => let @errors += { id: @task.id, reason: @task.blocker }
      ]
    ]

    let @out += {
      name: @project.name,
      total: @rows.length,
      tasks: @rows
    }
  ]

  show "Projects: @out.length, errors: @errors.length"
  => { results: @out, errors: @errors }
]
```

### Test B: For Block with Nested Loop (wrapped in exe)

```
/exe @processUsers(users) = [
  for @user in @users [
    let @active = []

    for @msg in @user.inbox [
      when [
        @msg.status == "unread" => let @active += @msg
      ]
    ]

    show `User @user.name: @active.length unread`
  ]
  => "done"
]

/show @processUsers(@users)
```

### Test C: Simple Accumulation Pattern (no var +=)

```
/exe @buildResults(rows) = [
  let @results = []

  for @row in @rows [
    let @acc = { id: @row.id, tags: [] }

    for @tag in @row.tags [
      let @acc.tags += @tag
    ]

    let @results += @acc
  ]

  => @results
]

/show @buildResults(@rows) | @json
```

**Note**: Accumulation uses `let` within exe block scope. No `var +=` syntax exists.

### Test D: Strict Mode (No Slashes)

```
exe @process(items) = [
  let @results = []

  for @item in @items [
    let @results += @item.value
    show "Processed: @item.id"
  ]

  => @results
]

var @output = @process([{ id: 1, value: "a" }])
show @output
```

### Test E: Loose Mode (Top-Level Slashes Only)

```
/exe @process(items) = [
  let @results = []

  for @item in @items [
    let @results += @item.value
    show "Processed: @item.id"
  ]

  => @results
]

/var @output = @process([{ id: 1, value: "a" }])
/show @output
```

## Edge Cases

- [ ] Empty blocks: `[ ]` should be valid, returns undefined
- [ ] Return without value: `=>` alone should error
- [ ] Let without value: Should error at parse time
- [ ] Nested parallel: Verify parallel specs stack correctly
- [ ] Error mid-block: Should preserve completed let assignments

## Validation

- [ ] All integration tests pass
- [ ] Edge cases covered
- [ ] No regressions in existing functionality

## Notes

Scope tightened: no standalone error fixtures; docs handled in mlld-3e6.

## Notes

Scope tightened: drop error fixtures; focus on integration/edge tests. Docs handled in mlld-3e6.


