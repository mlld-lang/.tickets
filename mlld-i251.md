---
id: mlld-i251
status: closed
deps: []
links: []
created: 2025-12-20T13:21:25.421188-08:00
type: bug
priority: 1
---
# let bindings inside exe blocks cannot shadow imported variables

**Reported by:** @partydev in mm chat

**Symptom:** When a module imports a variable (e.g., `@agentIds` from `@payload`) and then an exe block uses `let @agentIds = ...`, it throws VariableRedefinitionError instead of allowing the let binding to shadow the imported variable.

**Reproduction:**
```mlld
/import { @agentIds } from @payload

/exe @test() = [
  let @agentIds = ["internal"]  // FAILS: Variable 'agentIds' is already defined
  => @agentIds
]

/show @test()
```

**Real-world case:** proto-5.1's router (`response-required.router.mld`) has:
- Line 80: `let @agentIds = @agentRegistry.mx.keys`
- Main file imports `@agentIds` from `@payload`
- When router's exe block runs, it fails

**Root cause analysis:**
1. `evaluateLetAssignment` in `when.ts:134-151` creates a child env and calls `createVariableFromValue` with importPath='let'
2. `createVariableFromValue` passes importPath to `buildMetadata` which should set `mx.importPath`
3. BUT: Debug shows `variable.mx?.importPath` is `undefined` when `setVariable` is called
4. The check in `VariableManager.ts:217-238` uses `isLetBinding = variable.mx?.importPath === 'let'` which fails
5. So the parent scope check throws an error instead of allowing shadowing

**Investigation notes:**
- Added debug: `[setVariable] name=localIds, isLetBinding=false, importPath=undefined`
- The `importPath` is not being preserved through the variable creation chain
- Need to trace: `buildMetadata` → `createArrayVariable` → `normalizeFactoryOptions` → `mx` field

**Partial fix attempted:**
Added check in VariableManager.ts line 219:
```typescript
const isLetBinding = variable.mx?.importPath === 'let';
if (parent?.hasVariable(name) && !isLetBinding) { ... }
```
But `importPath` is undefined so the check doesn't work.

**Files involved:**
- `interpreter/eval/when.ts` - evaluateLetAssignment
- `interpreter/eval/import/VariableImporter.ts` - createVariableFromValue
- `interpreter/env/VariableManager.ts` - setVariable (parent scope check)
- `core/types/variable/VariableFactories.ts` - normalizeFactoryOptions, createArray

**Next steps:**
1. Trace why `importPath` from `buildMetadata` isn't reaching `variable.mx.importPath`
2. Check `normalizeFactoryOptions` to see how it builds the `mx` object
3. Fix the chain so let bindings are properly identified


