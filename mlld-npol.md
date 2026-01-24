---
id: mlld-npol
status: closed
deps: []
links: []
created: 2026-01-20T03:56:53.686329-08:00
type: task
priority: 1
---
# Named policy syntax (simpler alternative to selective imports)

## Summary

Implement named policy syntax as a simpler alternative to selective imports (mlld-xbn2).

## Syntax

### Define Named Policy

```mlld
policy @production = {
  defaults: {
    unlabeled: untrusted,
    rules: ["no-secret-exfil", "no-untrusted-destructive"]
  },
  capabilities: {
    allow: { cmd: ["git:*", "npm:*"] },
    deny: [sh]
  }
}

export { @production }
```

### Import Named Policy

```mlld
import policy @production from "@company/policies"
```

Uses standard import syntax - no new grammar for selective imports or except clauses.

## Why This Approach

- **80/20 rule**: Gets most of the value with much less complexity
- **No new grammar**: Leverages existing `policy @name = ...` and standard imports
- **Composable**: Can still use `union(@a, @b)` for merging
- **Explicit**: User decides what to export, not what to cherry-pick on import

## Grammar Changes

Minimal - just ensure:
1. `policy @name = { ... }` parses policy object literals
2. Policy values can be exported like any other value
3. `import policy @name from` works (may already work)

## Implementation

1. Policy object literal parsing (may need grammar work)
2. Policy values in scope as first-class values
3. Standard export/import handles the rest

## Exit Criteria

- [ ] `policy @name = { ... }` parses inline policy object
- [ ] Policy values can be exported: `export { @policy }`
- [ ] Policy values can be imported: `import policy @p from "module"`
- [ ] Imported policies register guards correctly (see mlld-hncp)
- [ ] `union(@a, @b)` works with named policies

## Estimated Effort

~3 hours (vs ~6 hours for mlld-xbn2)


