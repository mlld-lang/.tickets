---
id: mlld-lszg
status: closed
deps: []
links: []
created: 2025-12-20T20:24:03.354169-08:00
type: bug
priority: 1
---
# Fixed: null values converted to empty string in exe returns

When `exe ... = when first [... * => null]` returned null, it was being converted to empty string by `ensureStructuredValue()`. This broke filtering patterns like `for @c in @items when @c != null => @c`.

Fix: `ensureStructuredValue()` now preserves `null` as a distinct value with type 'null' and text 'null', rather than converting to empty string.

Reported by @party-dev in mm chat.


