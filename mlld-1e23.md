---
id: mlld-1e23
status: closed
deps: []
links: []
created: 2025-12-20T12:19:10.770486-08:00
type: bug
priority: 1
---
# Imported modules see caller's variables (scope isolation bug)

When calling an exe block from an imported module, the exe block incorrectly has visibility to the caller's variables. This causes VariableRedefinition errors when the module uses a `let` binding with the same name as a variable in the caller's scope.

**Example:**
- main.mld imports `@agentIds` from `@payload`  
- main.mld calls `@router()` from an imported module
- router.mld has `let @agentIds = @agentRegistry.mx.keys` inside its exe block
- Error: Variable 'agentIds' is already defined

**Expected behavior:** The router module should NOT see the caller's `@agentIds`. Exe blocks in imported modules should only see:
1. Their own module-level variables
2. Parameters explicitly passed to them

**Root cause:** Child environments created for exe block evaluation inherit variable visibility from the calling environment, but imported module exe blocks should be isolated.


