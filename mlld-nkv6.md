---
id: mlld-nkv6
status: closed
deps: []
links: []
created: 2026-01-11T16:34:27.189285-08:00
type: task
priority: 2
tags: [size-s, complexity-m, risk-m, impl-none]
updated: 2026-01-31T09:59:15Z
---
# Destructured @payload import fails when fields don't exist

**Problem**: Using destructured import from @payload throws an error if the field wasn't provided via CLI.

**Fails**:
```mlld
import { @topic, @tier } from @payload
>> Error: Import 'topic' not found in module '@payload'
```

This happens when running `mlld run script --tier 1` without `--topic`.

**Workaround** (verbose):
```mlld
import "@payload" as @p
var @topic = @p.topic ? @p.topic : ""
var @tier = @p.tier ? @p.tier : ""
```

**Expected behavior options**:
1. Destructured imports from @payload should default to undefined/null for missing fields
2. Support default values in destructured imports: `import { @topic = "" } from @payload`
3. At minimum, better error message suggesting the namespace import pattern

**Context**: @payload is a dynamic module injected by `mlld run` containing CLI flags. Fields are inherently optional - not all flags are always provided.

**Note**: A previous fix (commit 052237e) made `@payload.field ? @payload.field : default` work in ternary conditions. But destructured imports still fail.

**Discovered in**: qa.mld refactoring - tried cleaner destructured import syntax.



**2026-01-31 09:59 UTC:** Fixed: Destructured imports from @payload and @state now allow missing fields, setting them to null instead of throwing an error. This enables cleaner syntax like 'import { @topic, @tier } from @payload' where not all fields are required. Tests added in tests/sdk/dynamic-modules.test.ts.
