---
id: mlld-d9y
status: closed
deps: []
links: []
created: 2025-12-18T06:29:43.574038-08:00
type: feature
priority: 1
---
# Add .mx.keys, .mx.values, .mx.entries for objects

Add discoverable object utility fields instead of relying on the _key suffix pattern:

```mlld
/var @obj = {"a": 1, "b": 2, "c": 3}

@obj.mx.keys      // ["a", "b", "c"]
@obj.mx.values    // [1, 2, 3]  
@obj.mx.entries   // [["a", 1], ["b", 2], ["c", 3]]
```

**Current workaround:** Use the _key suffix during iteration:
```mlld
/exe @getKeys(obj) = for @val in @obj => @val_key
```

This is not discoverable and requires explaining a hidden pattern. Proper fields would be much more intuitive.

**Use case:** @partydev needs to iterate over agent registry keys for multi-agent routing.

**Implementation:** Add to StructuredValue metadata system, similar to how arrays have .length.


