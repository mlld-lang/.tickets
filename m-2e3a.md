---
id: m-2e3a
status: closed
deps: []
created: 2026-02-04T11:32:24Z
type: bug
priority: 0
assignee: Adam
tags: [security, phase-3-4, prevent-exfil]
updated: 2026-02-04T11:32:27Z
---
# Security: Property/array element access strips security labels from primitive values

When a secret-labeled value is stored in an array or object, accessing it via @arr[0] or @obj.property returns the primitive value without security labels. This is because ExpressionProvenance uses a WeakMap which cannot attach to primitive types (strings, numbers, booleans). This allows a trivial bypass of policy label flow rules: put a secret in an array/object, extract it, and exfil freely. Reproduction: var secret @key = 'sk-test'; var @arr = [@key]; var @first = @arr[0]; @post(@first) -- this succeeds when it should be blocked.

## Acceptance Criteria

1. @arr[0] on a secret-containing array preserves the secret label. 2. @obj.prop on a secret-containing object preserves the secret label. 3. Existing adversarial tests tmp/adversarial-exe-array-map.mld and tmp/adversarial-array-access-direct.mld and tmp/adversarial-object-access-strip.mld are all blocked by policy.


## Notes

**2026-02-07T01:28:33Z**

Verified fixed. field-access.ts:785-796 wraps primitive values extracted from labeled collections in StructuredValue with security descriptor applied. Confirmed with 8 test cases: direct array/object access, template interpolation, exe passthrough, for-map, and deep nesting — all correctly blocked by guards. Tests added under tests/cases/security/collection-element-guard-*/ and collection-element-access-*/.
