---
id: mlld-slk
status: closed
deps: []
links: []
created: 2025-12-11T10:46:49.078819-08:00
type: bug
priority: 0
---
# Exe blocks with let statements return null in for loops

**Critical regression - exe blocks with let statements broken.**

**Repro 1 - In for loop:**
```mlld
/exe @wrap(agent, turnP) = [
  let @turn = @turnP[@agent]
  => { name: @agent, turn: @turn }
]
/var @results = for @a in ["alice"] [@wrap(@a, @turnP)]
>> Returns: [null]
```

**Repro 2 - Direct call:**
```mlld
/exe @wrap(agent, turnP) = [
  let @turn = @turnP[@agent]
  => { name: @agent, turn: @turn }
]
/show @wrap("alice", @turnP)
>> Error: Variable not found for index: [object Object]
```

**Without let:**
```mlld
/exe @wrap(agent, turnP) = { name: @agent, turn: @turnP[@agent] }
>> Works perfectly
```

**Impact:** 
- Blocking @partydev.1's proto-3.6/3.7 testing
- Breaks all exe blocks that use let statements
- Introduced with recent parallel for blocks changes

**Priority:** P0 - critical regression in core functionality


