---
id: mlld-nkv6
status: open
deps: []
links: []
created: 2026-01-11T16:34:27.189285-08:00
type: task
priority: 2
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


