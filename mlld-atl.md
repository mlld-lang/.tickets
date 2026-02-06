---
id: mlld-atl
status: open
deps: []
links: []
created: 2025-12-10T22:32:56.758945-08:00
type: feature
priority: 3
tags: [size-l, complexity-l, risk-l, impl-partial, needs-human-design]
updated: 2026-01-30T22:45:10Z
---
# Support template collections for registry modules

**Current:** Template collections only work with local directories:
```mlld
✅ /import templates from "@base/agents" as @agents(msg, ctx)
✅ /import templates from "./agents" as @agents(msg, ctx)
❌ /import templates from @alice/templates as @tmp(msg, ctx)  # fails
```

**Desired:** Enable template collections for published registry modules:
```mlld
/import templates from @alice/agents as @agents(message, context)
/show @agents["alice"](@msg, @ctx)
/show @agents.support["helper"](@msg, @ctx)
```

**Implementation challenges:**
1. **Packaging** - How to publish directory structures to registry?
   - Option A: Zip archive in module metadata
   - Option B: Manifest listing all template paths
   - Option C: Multi-file gist (GitHub API supports this)

2. **Resolution** - RegistryResolver needs to:
   - Detect `importType === 'templates'`
   - Fetch directory structure (not single file)
   - Provide directory walker interface

3. **Caching** - Template collections need directory-level cache invalidation

**Workaround:** Publish individual template executables:
```mlld
>> In @alice/templates module
/exe @alice(msg, ctx) = template "alice.att"
/exe @bob(msg, ctx) = template "bob.att"
/export { @alice, @bob }

>> Usage
/import { @alice, @bob } from @alice/templates
```

**Priority:** P3 - the workaround works, but UX would be better with collection syntax.

**Related:** mlld-drq (local template collections)


