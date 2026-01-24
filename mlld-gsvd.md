---
id: mlld-gsvd
status: closed
deps: [mlld-62ay, mlld-xc1h, mlld-2dbc, mlld-k0of]
links: []
created: 2025-12-21T02:16:34.248056-08:00
type: task
priority: 3
---
# Tests: Conditional inclusion test fixtures

Add test fixtures for all conditional inclusion patterns.

**Location:** `tests/cases/valid/feat/conditional-inclusion/`

**Test cases:**
1. Basic template: `@var?\`content\`` with truthy/falsy
2. Basic string: `@var?"content"` with truthy/falsy
3. Array elements: `[@a, @b?, @c]` with mixed truthiness
4. Object properties: `{key?: @val}` with truthy/falsy
5. Field access: `@obj.field?\`...\``
6. Nested: `@a?\`@b?\`inner\` outer\``
7. Whitespace handling
8. Empty values: `""`, `[]`, `{}`, `null`
9. Multiple conditionals in one command

**Also add invalid cases** for malformed syntax.


