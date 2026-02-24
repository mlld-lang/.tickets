---
id: m-b9a2
status: closed
deps: []
links: []
created: 2026-02-24T10:58:56Z
type: bug
priority: 0
assignee: Adam
tags: [interpreter, json, property-access]
updated: 2026-02-24T11:19:53Z
---
# JSON property access on non-existent fields returns truthy reference instead of null

When accessing a property that doesn't exist on a JSON-loaded object, the ternary operator and @exists both see a truthy value instead of null/falsy.

## Reproduction

```mlld
var @obj = { name: "alice" }

>> @obj.missing should be falsy, but ternary sees it as truthy
let @result = @obj.missing ? "truthy" : "falsy"
>> Expected: "falsy"
>> Actual: "truthy" (the unresolved reference wrapper is truthy)

>> @exists also incorrect
show @exists(@obj.missing)
>> Expected: false
>> Actual: true
```

Same behavior with JSON loaded from file:

```mlld
var @config = <config.json>
>> config has { groups: [{ atoms: ["a"], standalone: true }] }
>> group[0] has no 'source' field

var @group = @config.groups[0]
let @src = @group.source ? @group.source : "default"
>> Expected: "default"
>> Actual: returns metadata wrapper object
```

## Root cause (probable)

Property access returns a lazy reference wrapper object. The ternary and @exists test the truthiness of the wrapper itself (always truthy) rather than resolving the value first.

## Workaround

Coerce through a template, then string-compare:
```mlld
let @hasSrc = \`@group.source\`
let @srcDir = @hasSrc != "null" ? @hasSrc : @catName
```

## Expected behavior

A missing JSON property should be falsy in all contexts:
- Ternary condition: falsy branch taken
- @exists(): returns false
- Template interpolation: "null" (already works correctly)


## Notes

**2026-02-24T11:04:39Z** Fixed field-access metadata collision for object/array variables: missing .source/.metadata now resolve as missing data (null/undefined in condition mode) instead of leaking Variable metadata. Added regressions for ternary and @exists truthiness with missing source fields.
