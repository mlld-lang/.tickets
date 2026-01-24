---
id: mlld-dsyr
status: closed
deps: [mlld-j5bw.1]
links: []
created: 2026-01-14T20:04:20.320004-08:00
type: task
priority: 1
parent: mlld-j5bw
---
# C1: Require /needs and /wants for module imports

## Summary

Enforce that all importable modules must have /needs and /wants declarations.

## Rationale

Modules are opaque. The /needs and /wants declarations are the capability contract:
- Policy can make decisions based on declarations before code runs
- Users can see what a module requires before importing
- Auditing is simple: grep for /needs

## Implementation

### 1. Module Loader Check

When loading a module for import, verify declarations exist:

```typescript
// In ModuleLoader or similar
async function loadModule(modulePath: string): Promise<Module> {
  const ast = await parseModule(modulePath);
  
  // Check for /needs directive
  const hasNeeds = ast.directives.some(d => d.kind === 'needs');
  const hasWants = ast.directives.some(d => d.kind === 'wants');
  
  if (!hasNeeds || !hasWants) {
    throw new MlldModuleError(
      `Module must declare /needs and /wants to be importable. ` +
      `Missing: ${!hasNeeds ? '/needs' : ''} ${!hasWants ? '/wants' : ''}`.trim(),
      { code: 'MISSING_CAPABILITY_DECLARATION' }
    );
  }
  
  return evaluateModule(ast);
}
```

### 2. Empty Declarations Valid

Modules that need nothing special:
```mlld
/needs { }
/wants [ ]

/exe @formatDate(d) = ...
/export { @formatDate }
```

This is an explicit "I need nothing" statement - low friction.

### 3. Local Scripts Exception?

Decision needed: Do local scripts (not imported) need declarations?

Options:
- Yes, everywhere (strictest)
- No, only imported modules (more practical)
- Only registry/published modules (mixed)

Recommend: Only imported modules require declarations. Local scripts can run without.

## Files to Modify

1. `core/registry/ModuleLoader.ts` or equivalent
   - Add declaration check before module evaluation

2. `interpreter/eval/import/ImportDirectiveEvaluator.ts`
   - May need to verify loaded module has declarations

## Test Cases

```mlld
// Module without declarations
/exe @helper() = "help"
/export { @helper }
```

```mlld
// Importing it should fail
/import { @helper } from "./no-declarations.mld"
// Error: Module must declare /needs and /wants
```

```mlld
// Module with empty declarations (valid)
/needs { }
/wants [ ]
/exe @helper() = "help"
/export { @helper }
```

## Exit Criteria

- [ ] Modules without /needs cannot be imported
- [ ] Modules without /wants cannot be imported
- [ ] Empty declarations accepted (/needs { }, /wants [ ])
- [ ] Clear error message indicates what's missing
- [ ] Local/direct scripts still work without declarations

## Estimated Effort
4-6 hours


