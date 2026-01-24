---
id: mlld-kxut
status: closed
deps: [mlld-bfnc]
links: []
created: 2026-01-20T16:30:57.054629-08:00
type: task
priority: 1
parent: mlld-awwp
---
# Variable Passing Tests

Add variable passing tests covering all mlld Variable types:

Test cases:
1. exe-py-variable-passing-simple: String and number parameters
2. exe-py-variable-passing-objects: Dict/object parameters
3. exe-py-variable-passing-arrays: List/array parameters  
4. exe-py-undefined-params: Handle undefined params gracefully

Validates python-variable-helpers.ts correctly:
- Converts Variables to Python wrapper classes
- Preserves metadata (__mlld_type__, __mlld_metadata__)
- Passes values correctly to Python code
- Handles edge cases (null, undefined, nested objects)

Acceptance: All variable types pass correctly with metadata intact


