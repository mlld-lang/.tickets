---
id: mlld-m69f
status: closed
deps: [mlld-idy6]
links: []
created: 2026-01-20T16:30:57.053349-08:00
type: task
priority: 0
parent: mlld-awwp
---
# Error Handling

Add error handling tests and enhance PythonExecutor error reporting:

Tests needed:
1. exceptions/python/py-syntax-error: Invalid Python syntax
2. exceptions/python/py-runtime-error: ZeroDivisionError, etc.
3. exceptions/python/py-name-error: Undefined variable reference

Code improvements in PythonExecutor.ts:
- Add error enhancement (similar to enhanceJSError for node)
- Include stderr/stdout/exitCode in error metadata
- Add workingDirectory and directiveType to error details
- Improve error message formatting

Acceptance: Python errors show helpful context like node {} errors do


