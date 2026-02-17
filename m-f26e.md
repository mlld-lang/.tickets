---
id: m-f26e
status: closed
deps: []
links: []
created: 2026-02-06T20:17:37Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [parser, when, arithmetic]
updated: 2026-02-12T14:51:58Z
---
# Multiplication * on return line parsed as when wildcard

In an exe block, `=> @fn() * 1` parses `*` as a when wildcard instead of multiplication. Workaround: assign to a let first, then return. E.g. `let @x = @fn(); => @x * 1` works but `=> @countFiles(@dir, @pattern) * 1` does not.

