---
id: m-6ee8
status: closed
deps: []
links: []
created: 2026-02-07T00:43:54Z
type: bug
priority: 2
assignee: Adam Avenir
tags: [interpreter, for, parallel, errors]
---
# Surface parallel for-expression iteration errors visibly

When a for parallel iteration throws, the error is caught (for.ts:~937-953) and returned as a marker object {index,key,message,error,value} in the results array with no visible output. Users see normal-looking results that contain error objects instead of expected data. Downstream filtering silently returns 0 matches because error markers don't have expected fields.

## Acceptance Criteria

1. When a parallel iteration throws, the error is visible (logged/warned)
2. Error markers are distinguishable from normal results (or errors surface before results are used)
3. @mx.errors metadata is populated and accessible after for-parallel completes
4. Tests: tests/cases/feat/for/for-parallel-error-visibility/ (iteration error produces visible output, not silent marker)

