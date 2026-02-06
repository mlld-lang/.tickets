---
id: mlld-nu5x
status: open
deps: []
links: []
created: 2026-01-18T11:29:08.959709-08:00
type: task
priority: 3
tags: [size-s, complexity-s, risk-xs, impl-none]
---
# Provider @capabilities checking for better errors

## Summary

Providers can export `@capabilities` to declare what features they support:
```mlld
var @capabilities = { snapshot: true, warmPool: true }
export { @create, @execute, @release, @snapshot, @capabilities }
```

Core should read this before attempting operations to give better error messages.

**Current:** "provider does not export @snapshot"
**Better:** "provider declares it does not support snapshots"

## Status

Maybe/Later - Low priority QoL improvement. Only implement if users report confusion about provider capabilities.

## References

- Spec: Part 7.1
- Code: interpreter/env/environment-provider.ts:417


