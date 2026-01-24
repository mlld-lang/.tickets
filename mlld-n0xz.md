---
id: mlld-n0xz
status: closed
deps: []
links: []
created: 2025-12-21T15:36:30.685758-08:00
type: bug
priority: 1
---
# @fm.id inside object literals returns whole frontmatter instead of field

When using @fm.id inside an object literal, field access is not resolved:

```mlld
/var @directId = @fm.id          # → "party" ✓
/var @meta = { "id": @fm.id }    # → { "id": { whole FM } } ✗
```

Workaround: Assign to intermediate variable first, then use in object.


