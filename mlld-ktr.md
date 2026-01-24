---
id: mlld-ktr
status: closed
deps: []
links: []
created: 2025-12-12T18:08:09.362773-08:00
type: bug
priority: 0
---
# @exe() function calls not highlighted

Exec invocations like @getReplyPressure(agent, msg) appear as plain text. Should be highlighted as function calls. Currently tokenized as 'function' type but not showing in editor. Seen in: orchestrate.mld lines 10, 26, 27

## Notes

Fixed by refactoring ExecInvocation handling in CommandVisitor - now properly combines @ with function name and handles method calls


