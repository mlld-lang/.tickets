---
id: mlld-k0of
status: closed
deps: []
links: []
created: 2025-12-20T21:47:36.892444-08:00
type: feature
priority: 3
---
# Conditional command argument syntax (@?{...} or similar)

## Conditional Inclusion Syntax: `@var?`

Universal conditional inclusion pattern - if `@var` is falsy, the attached content is omitted entirely.

### Syntax by Context

| Context | Syntax | Example | Behavior |
|---------|--------|---------|----------|
| **Commands/Templates** | `@var?\`...\`` | `cmd { claude @tools?\`--allowedTools "@tools"\` }` | Omit template snippet if falsy |
| **Strings** | `@var?"..."` | `"Hello "@title?"@title ""` | Omit string fragment if falsy |
| **Arrays** | `@var?` | `[@a, @b?, @c]` | Omit element if falsy |
| **Objects** | `key?: @val` | `{name: @n, title?: @t}` | Omit key-value pair if falsy |

### Truthiness Rules (existing `isTruthy()`)
**Falsy**: `null`, `undefined`, `""`, `"false"`, `"0"`, `0`, `NaN`, `[]`, `{}`

### Design Decisions
- Field access: `@obj.field?` - full path evaluated, then truthiness checked
- Nested conditionals: Work naturally via recursive interpolation  
- Whitespace: When falsy, nothing added (clean output)
- Undefined variables: Treated as falsy (omit content)

### Implementation
See child issues for grammar, interpreter, and type changes.


