---
id: m-0ccb
status: open
deps: []
links: []
created: 2026-02-07T02:02:06Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [error-handling, ergonomics]
---
# Runtime errors in for-loop bodies silently become data instead of throwing

When an exe function called inside a `for` loop body hits a runtime error (e.g., variable redefinition), the error is silently packaged into an `{__error: true, __message: '...'}` object and returned as the loop iteration result. No warning is logged to stderr. The loop continues.

This makes debugging pipelines extremely painful — the caller has no idea anything went wrong. In a real pipeline, `@r.status == 'collected'` was false (because status was an error object), so it counted 0 collected and reported success.

Repro:
```mlld
exe @fileExists(path) = sh { test -f "$path" && echo "yes" || echo "no" }

exe @doWork(n) = [
  let @exists = @fileExists("/tmp/nope")
  => "done"
]

var @results = for @n in [1, 2, 3] => {
  number: @n,
  status: @doWork(@n)
}
show `Results: @results`
```

Result: each status is `{__error: true, __message: 'variable-redefinition...'}` with zero visible output.

Suggestions:
1. Errors in for-loop bodies should at minimum log a warning to stderr
2. Consider making error propagation (throw) the default; require explicit opt-in (e.g., `with { ok: true }`) to capture errors as data
3. The __error wrapper pattern is useful but should be opt-in, not the silent default

