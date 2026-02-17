---
id: m-8df3
status: closed
deps: []
created: 2026-02-17T04:59:08Z
type: chore
priority: 0
assignee: Adam
updated: 2026-02-17T18:59:14Z
---
# Clean up 1100+ debug console.log statements in production code

1,100+ console.log/debug statements in non-test source code. LSP visitors are the worst offenders (CommandVisitor.ts alone has 70+). 30+ scattered process.env.DEBUG_* checks (DEBUG_LSP, DEBUG_FUZZY, DEBUG_WHEN, DEBUG_EXEC, etc.). Either consolidate into a proper debug/logging system or strip the debug statements for release.

