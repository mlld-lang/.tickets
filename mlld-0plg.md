---
id: mlld-0plg
status: open
deps: [mlld-7jp9]
links: []
created: 2026-01-14T20:06:06.882555-08:00
type: task
priority: 2
tags: [stale-check-9]
parent: mlld-su55
---
# D2: Publish verification for capability declarations

## Summary

When publishing a module, verify that declared /needs matches statically analyzed /needs.

## Flow

```bash
$ mlld publish my-module.mld

Analyzing module...
Declared: /needs { keychain }
Detected: /needs { keychain, network }

⚠️  Mismatch detected:
  - Missing declaration: network (curl command at line 12)

Options:
  1. Update /needs declaration and retry
  2. Publish anyway (module may fail if network blocked by policy)

Publish with mismatch? [y/N]
```

## Implementation

```typescript
// cli/commands/publish.ts
async function publishCommand(args: string[]): Promise<void> {
  const modulePath = args[0];
  const ast = await parseFile(modulePath);
  
  // Get declared needs
  const declaredNeeds = extractNeedsDeclaration(ast);
  
  // Get analyzed needs
  const analyzedNeeds = analyzeCapabilities(ast);
  
  // Compare
  const missing = findMissingDeclarations(declaredNeeds, analyzedNeeds);
  const extra = findExtraDeclarations(declaredNeeds, analyzedNeeds);
  
  if (missing.length > 0 || extra.length > 0) {
    console.warn('⚠️  Capability declaration mismatch:');
    
    for (const m of missing) {
      console.warn(`  - Missing: ${m.capability} (${m.reason} at line ${m.line})`);
    }
    
    for (const e of extra) {
      console.warn(`  - Extra: ${e.capability} (declared but not detected)`);
    }
    
    const proceed = await confirm('Publish with mismatch?');
    if (!proceed) {
      console.log('Publish cancelled.');
      return;
    }
  }
  
  // Proceed with publish
  await doPublish(modulePath);
}
```

## Dependencies

Requires C4 (mlld analyze) for capability detection logic.

## Exit Criteria

- [ ] Publish analyzes module capabilities
- [ ] Warns on declaration vs usage mismatch
- [ ] Allows override with confirmation
- [ ] Blocks by default on mismatch

## Estimated Effort
3-4 hours


