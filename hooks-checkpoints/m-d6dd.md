---
id: m-d6dd
status: closed
deps: []
created: 2026-02-19T23:02:01Z
type: bug
priority: 1
assignee: Adam Avenir
tags: [severity-medium, pre-merge]
updated: 2026-02-20T00:00:36Z
---
# Add error isolation to checkpoint hooks

## Problem

Both checkpoint hooks perform I/O without try/catch. Filesystem errors crash the pipeline instead of degrading to no-cache behavior, contradicting the spec's "just memoization" design principle.

## Evidence

- `checkpoint-pre-hook.ts:115`: `manager.get()` not wrapped — permission denied/I/O errors propagate
- `checkpoint-post-hook.ts:123-133`: `manager.put()` not wrapped — disk full at item 500 of 732 crashes

## Fix

Wrap `manager.get()` and `manager.put()` in try/catch. On error, log the failure and degrade gracefully (treat as cache miss for pre-hook, skip caching for post-hook). The pipeline should never crash due to checkpoint I/O.

