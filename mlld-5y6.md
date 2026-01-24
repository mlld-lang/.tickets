---
id: mlld-5y6
status: closed
deps: []
links: []
created: 2025-12-08T19:40:56.370878-08:00
type: task
priority: 2
parent: mlld-53mk
---
# Docs: clarify pipeline data shape vs parallel stage output

Docs promise StructuredValue pipeline stages, but still mention parallel groups handing JSON array strings to next stage. Validate actual behavior and update docs to reflect structured pipeline inputs/outputs (including parallel aggregation semantics) once aligned with executor/state machine.


