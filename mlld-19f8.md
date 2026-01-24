---
id: mlld-19f8
status: open
deps: []
links: [mlld-2qp4]
created: 2026-01-11T14:23:07.05174-08:00
type: bug
priority: 2
---
# Confusing error when mixing => arrow with block form in for loops

Users attempt 'for @x in @items => [...]' mixing arrow form with block form. The error message doesn't clearly explain that => is for single expressions and [...] is block form - don't mix them. Related to mlld-2qp4.


