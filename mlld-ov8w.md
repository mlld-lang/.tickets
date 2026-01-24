---
id: mlld-ov8w
status: closed
deps: []
links: []
created: 2026-01-14T15:17:08.077496-08:00
type: task
priority: 1
parent: mlld-x60s
---
# Phase 2.2: Add keychain capability to grammar

## Summary
Add `keychain` as a valid capability in `/needs` and `/wants` declarations.

## Context
Environment modules need to declare `keychain` capability for secure credential access. The grammar currently only supports: sh, bash, network, net, filesystem, fs.

## Implementation

### Files to Modify

1. **`grammar/directives/needs-wants.peggy:183-189`**
   Current:
   ```peggy
   NeedsBooleanKey
     = "sh" / "bash" / "network" / "net" / "filesystem" / "fs"
   ```
   
   Change to:
   ```peggy
   NeedsBooleanKey
     = "sh" / "bash" / "network" / "net" / "filesystem" / "fs" / "keychain"
   ```

2. **`core/policy/needs.ts`**
   - Add to `BOOLEAN_CAPABILITY_ALIASES` (around line 16-23)
   - Add `keychain?: boolean` to `PolicyCapabilities` interface

3. **Run grammar build**
   ```bash
   npm run build:grammar
   ```

## Exit Criteria
- [ ] `/needs { keychain }` parses successfully
- [ ] `/wants [ { tier: "secure", keychain } ]` parses successfully
- [ ] Grammar builds without errors
- [ ] AST shows keychain as boolean capability
- [ ] All existing needs/wants tests pass

## Tests to Add
Create `tests/cases/slash/needs/keychain-capability/`
```
example.md:
  /needs { keychain }
  /show "Has keychain access"
expected.md:
  Has keychain access
```
Note: Actual keychain implementation is Phase 4. This test just validates parsing.

## Dependencies
None - grammar change only

## Estimated Effort
2 hours


