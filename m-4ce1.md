---
id: m-4ce1
status: open
deps: []
created: 2026-02-20T01:21:28Z
type: bug
priority: 1
assignee: Adam Avenir
---
# Investigate policy loading: union(@config) required instead of direct import/creation

When defining policies, users must wrap the config in `union(@config)` for it to take effect. Policies created via direct object assignment or imported from modules don't seem to work without this wrapper. Investigate why `policy @p = { ... }` or importing a policy module doesn't Just Work — the `union()` call shouldn't be required for basic policy activation. This may be a resolution/evaluation order issue, a missing normalization step, or an intentional design choice that needs documentation.

