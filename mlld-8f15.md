---
id: mlld-8f15
status: open
deps: [mlld-b2nu]
links: []
created: 2026-01-15T10:55:30.422573-08:00
type: task
priority: 2
parent: mlld-urik
---
# D2-GAP: Bidirectional publish verification

## Problem

Publish verification only checks one direction:
- ✅ Warns if declared needs are missing details
- ❌ Does NOT warn about undeclared capabilities

Example: Module uses @keychain but doesn't declare /needs { keychain } - no warning.

## Current State

From `cli/commands/publish/validation/DependencyValidator.ts`:

```typescript
// Lines 42-55: Only checks declared needs
if (module.metadata.needs.includes('js')) {
  // Warn if missing needs-js details
}
```

The `DependencyDetector` class CAN detect capabilities, but the validator doesn't compare analyzed vs declared bidirectionally.

## Required Fix

Add reverse check - analyze module and compare to declarations:

```typescript
async validate(module: ModuleDefinition): Promise<ValidationResult> {
  const warnings: string[] = [];
  
  // Existing: Check declared needs have details
  // ...
  
  // NEW: Check analyzed capabilities are declared
  const ast = await parseModule(module.entryPath);
  const analyzed = this.detector.detectRuntimeNeeds(ast);
  const declared = module.metadata.needs || [];
  
  // Check for undeclared capabilities
  if (analyzed.includes('keychain') && !declared.includes('keychain')) {
    warnings.push(
      'Module uses @keychain but does not declare /needs { keychain }'
    );
  }
  
  if (this.usesNetworkCommands(ast) && !declared.includes('network')) {
    warnings.push(
      'Module uses network commands but does not declare /needs { network }'
    );
  }
  
  // ... similar for sh, filesystem
  
  return { valid: true, warnings };
}
```

## Files to Modify

1. `cli/commands/publish/validation/DependencyValidator.ts`
   - Add bidirectional checking
   - Use DependencyDetector to analyze module
   - Compare analyzed vs declared

2. May need to enhance DependencyDetector for keychain/network detection

## Exit Criteria

- [ ] Publish warns when @keychain used without declaration
- [ ] Publish warns when network commands used without declaration
- [ ] Publish warns when shell used without declaration
- [ ] Warnings are clear and actionable
- [ ] Test verifies bidirectional checking

## Estimated Effort
3-4 hours


