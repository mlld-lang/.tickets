---
id: mlld-xrsy.5
status: closed
deps: [mlld-xrsy.2, mlld-xrsy.3, mlld-xrsy.4]
links: []
created: 2026-01-22T08:00:04.399776-08:00
type: task
priority: 3
parent: mlld-xrsy
---
# Import directive for Python packages

Add `py` import type to ImportDirectiveEvaluator.

- Route `@import { from: 'py' }` to Python package resolution
- Alias `python` for discoverability
- Introspect Python packages for exposed objects
- Integrate with existing import system patterns


