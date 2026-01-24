---
id: mlld-dk4
status: open
deps: [mlld-76c]
links: []
created: 2025-12-13T05:31:59.470602-08:00
type: bug
priority: 2
---
# Object keys should match JavaScript key highlighting

Object property keys (agent:, replyPressure:, etc) are currently highlighted as Type (purple/magenta). They should match the same color as JavaScript object keys in the same colorscheme for consistency and familiarity.

Example: { agent: @agent, score: 0.5 }
Keys should use colorscheme's JS object key color.


