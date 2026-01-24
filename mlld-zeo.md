---
id: mlld-zeo
status: closed
deps: [mlld-0gd, mlld-b4f]
links: []
created: 2025-12-09T22:16:54.131728-08:00
type: task
priority: 1
---
# Phase 2.1: Interpreter - Export let functions from when.ts

## Summary

Export the existing let/augmented assignment evaluation functions from when.ts so they can be reused by exe and for block evaluators.

## 📚 Required Reading

Before starting Phase 2 interpreter work:
- **docs/dev/DATA.md** - StructuredValue system (.text, .data, .ctx)
- **interpreter/utils/structured-value.ts** - asData/asText helpers

Key patterns:
- `asData()` for computation boundaries (JS args, comparisons)
- `asText()` for display boundaries (templates, shell commands)
- Let assignments store StructuredValue wrappers
- Return values should preserve wrappers

## Why This Matters

The plan identifies that exe blocks are ~80% the same as exe..when. The let/augmented assignment logic already exists and works - we just need to export it for reuse.

## Files to Modify

- `interpreter/eval/when.ts` - Export helper functions

## Implementation

Change from private to exported functions (around lines 31 and 66):

```typescript
// Change: async function evaluateLetAssignment(...)
// To: export async function evaluateLetAssignment(...)

export async function evaluateLetAssignment(
  entry: LetAssignmentNode,
  env: Environment
): Promise<Environment> {
  // ... existing implementation unchanged
}

export async function evaluateAugmentedAssignment(
  entry: AugmentedAssignmentNode,
  env: Environment
): Promise<Environment> {
  // ... existing implementation unchanged
}
```

## Key Implementation Details

From when.ts (lines 31-112):
- `evaluateLetAssignment()` creates child environment with new binding
- `evaluateAugmentedAssignment()` finds existing let variable and mutates
- Both return new Environment (immutable pattern)

## Augmented Assignment Semantics (lines 66-112)

- Arrays: concat (`[1, 2] += 3` → `[1, 2, 3]`, `[1] += [2, 3]` → `[1, 2, 3]`)
- Strings: append (`"hello" += " world"` → `"hello world"`)
- Objects: shallow merge (`{a: 1} += {b: 2}` → `{a: 1, b: 2}`)
- Only works with local `let` bindings (not global variables)

## Testing

After export, verify imports work:

```typescript
import { evaluateLetAssignment, evaluateAugmentedAssignment } from './when';
```

## Validation

- [ ] Functions exported without breaking existing when evaluation
- [ ] Type signatures remain unchanged
- [ ] All existing when tests still pass

## Notes

✅ PREREQUISITE UPDATE: Type enums completed in mlld-b4f. DirectiveKind now includes 'while' and 'for'. DirectiveSubtype now includes 'exeBlock', 'while', and 'for'. All types compile successfully. Ready to proceed with exporting let functions.


