---
id: m-e0d9
status: closed
deps: []
created: 2026-02-05T19:12:54Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, blocking-adversarial]
updated: 2026-02-05T19:17:12Z
---
# Resolve colon-label syntax gap in Target Example

## Problem

The Target Example in the job specification uses syntax like:
```mlld
exe net:w @postToServer(data) = run cmd { ... }
```

But the grammar doesn't support colon-namespaced labels. The DataLabelIdentifier rule in grammar/patterns/security.peggy uses BaseIdentifier which only allows `[a-zA-Z_][a-zA-Z0-9_]*` - no colons.

Verified by testing:
- `exe exfil @fn` - WORKS
- `exe net:w @fn` - PARSE ERROR

## Current Working Syntax

The 4 security protections work correctly using direct risk labels:
```mlld
exe exfil @postToServer(data) = run cmd { ... }
exe destructive @deleteFile(path) = run cmd { ... }
```

The atoms (policy-operations.md, etc.) document this working syntax.

## Options

### Option A: Update Target Example to match implementation

Change the Target Example to use direct risk labels:
```mlld
exe exfil @postToServer(data) = ...
exe destructive @deleteFile(path) = ...
```

This matches what the atoms document and what works today. The two-step pattern (semantic labels → policy mapping) becomes optional/future work.

Pros: No code changes, adversarial verification can proceed
Cons: Loses the semantic label abstraction the spec envisioned

### Option B: Extend grammar to support colon-namespaced labels

Update DataLabelIdentifier in grammar/patterns/security.peggy to allow patterns like `net:w`, `fs:r`, etc.

Pros: Matches spec vision, enables semantic/risk separation
Cons: Grammar change, rebuild required, may have cascading effects

## Recommendation

Option A is simpler and the implementation already works. The atoms are accurate. The colon syntax can be added as a future enhancement.

## Exit Criteria

Decide which option and implement. The Target Example must either be updated to parse, or the grammar must be extended to support it.

