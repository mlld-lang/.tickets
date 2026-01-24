---
id: mlld-fsa
status: closed
deps: []
links: []
created: 2025-12-10T22:48:01.085959-08:00
type: feature
priority: 1
---
# Add @root as alias for @base

**Rationale:** `@root` is a more common/intuitive term for project root than `@base`. Should work as an alias.

**Desired:**
```mlld
/var @readme = <@root/README.md>    # same as @base/README.md
/import { @cfg } from "@root/config.mld"
/import templates from "@root/templates" as @tmp(msg)
```

**Implementation:**
1. Add `'root'` to reserved function resolvers list (Environment.ts:516)
2. Update resolver checks to include `root`:
   - ImportDirectiveEvaluator.ts:245 - static import validation
   - ImportDirectiveEvaluator.ts:265 - templates import validation
   - Any other places checking `resolverName === 'base'`
3. Add `@root` to resolver config with same behavior as `@base`
4. Update docs to mention `@root` as alias

**Priority:** P3 - nice to have, `@base` works fine but `@root` is more intuitive for new users.


