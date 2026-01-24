---
id: mlld-ptfx
status: closed
deps: [mlld-k0of]
links: []
created: 2025-12-21T02:15:24.402699-08:00
type: task
priority: 3
---
# Grammar: key?: @val in objects

Add grammar for conditional object properties.

**File:** `grammar/patterns/data-values.peggy`

**Change:** Modify `DataObjectPair` to support optional `?` after key:
```peggy
DataObjectPair
  = key:PropertyKey optional:"?"? _ ":" _ value:DataPropertyValue {
      return {
        type: optional ? 'conditionalPair' : 'pair',
        key: key,
        value: value,
        isConditional: !!optional
      };
    }
```

**Example:**
```mlld
/var @config = {
  name: @name,
  title?: @title,   # Omit if @title is falsy
  role?: @role
}
```


