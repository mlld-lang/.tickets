---
id: mlld-i6s2
status: closed
deps: []
links: []
created: 2025-12-20T01:19:13.849605-08:00
type: feature
priority: 2
---
# Support var blocks: var @x = [ let @y = ...; => @y ]

User wants to do: `var @toInvoke = [ let @combined = @requiredAgents; @combined += @optionalAgents; => @combined ]`. We have blocks for exe/for/when but not var. This is a reasonable pattern to support.


