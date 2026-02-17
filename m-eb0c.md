---
id: m-eb0c
status: closed
deps: []
created: 2026-02-16T06:38:29Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-16T07:09:27Z
---
# Fix console.log duplication in js/node blocks

js/node blocks output console.log twice when the return value is falsy/undefined. QA confirmed in run-basics topic.

## What to do
Find where js/node block output is collected in the interpreter. The likely cause is that both stdout capture (console.log) and the return value are being emitted. When the return value is falsy/undefined, the output should only include the console.log output once.

Check interpreter/env/executors/ for JavaScript/Node executor code. Compare how stdout and return values are combined.

## Acceptance criteria
- run js { console.log('hello') } outputs 'hello' once (not twice)
- run js { console.log('hello'); return 'world' } outputs 'world' (return value takes precedence)
- run node { console.log('hello') } outputs 'hello' once
- Add test case in tests/cases/

