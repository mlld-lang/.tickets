---
id: mlld-5d2
status: open
deps: []
links: []
created: 2025-12-18T08:58:34.56179-08:00
type: task
priority: 2
parent: mlld-53mk
---
# Clarify template vs module directory imports in docs

Users are confused about when to use:
- `/import templates from "dir" as @name(params)` - for .att/.mtt files
- `/import "dir" as @name` - for index.mld modules

**Confusion:**
Both import directories but have different syntax and use cases. Not obvious which to use when.

**Where to clarify:**
- docs/user/modules.md (import section)
- llms.txt RULE_9 
- Error messages when wrong syntax is used

**Better error example:**
```
Directory contains .att/.mtt files. Did you mean:
  /import templates from "dir" as @name(param1, param2)
```

Identified by @partydev during scaffold work.


