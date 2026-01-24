---
id: mlld-72k
status: closed
deps: []
links: []
created: 2025-12-14T12:15:11.541168-08:00
type: task
priority: 1
---
# Pipeline functions should highlight as functions not variables

Pipeline functions should highlight as functions (purple italic) not variables (light blue).

Example: @data | @transform | @format
Currently: @transform and @format highlight as light blue (variable)
Should: Purple italic (function) since they're function invocations with implicit stdin

Also: Pipeline highlighting inconsistent across different contexts:
- Works in some places
- Broken in others (variables in pipeline position don't get function token type)

Solution: When VariableReference appears in pipeline context, check if it's in a pipe chain and use 'function' token type instead of 'variable'.

Files: VariableVisitor.ts - check context.inPipeline or similar flag


