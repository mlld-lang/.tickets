---
id: m-dabe
status: closed
deps: []
created: 2026-02-10T06:50:50Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-12T14:31:45Z
---
# StructuredValue: wrapper leaks into JS exe blocks

mlld template interpolation (@data.findings) transparently unwraps StructuredValues through .data, but JS exe blocks receive the raw wrapper object with {type, text, data, metadata, toString, valueOf}. This means item.findings is undefined in JS even though @item.findings works in templates. Anyone writing JS helpers that process arrays of structured values must use (a.data || a).findings pattern. This is separate from m-6b97 (nested yield unwrap) — even top-level values show this behavior when passed inside arrays. The JS exe contract should match the template interpolation contract.

