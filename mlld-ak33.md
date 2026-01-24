---
id: mlld-ak33
status: closed
deps: []
links: []
created: 2026-01-11T14:22:57.286477-08:00
type: bug
priority: 0
---
# @payload dynamic module not resolving in interpreter

When passing dynamicModules via execute(), @payload references don't resolve. The payload is being passed correctly through parseInjectOptions() and run.ts, but the interpreter isn't making them available. Investigation should start at interpreter/index.ts:245-249. See tmp/qa-handoff.md for full context.


