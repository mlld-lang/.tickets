---
id: mlld-h767
status: open
deps: []
links: []
created: 2026-01-14T17:11:57.943012-08:00
type: bug
priority: 2
---
# exec-invocation.ts only passes first arg to transformerImplementation - keychain functions need all args

In exec-invocation.ts line 1392-1413, builtin transformer handling only extracts args[0] into inputValue and passes that single value to transformerImplementation. Keychain functions (get, set, delete) need ALL args passed as array. Fix: check for internal.keychainFunction or paramNames.length > 1, then evaluate all args and pass as array.


