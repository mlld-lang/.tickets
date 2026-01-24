---
id: mlld-3h3
status: closed
deps: []
links: []
created: 2025-12-11T04:51:24.958764-08:00
type: bug
priority: 1
---
# Dynamic bracket access fails with field access expressions

**Repro:**
```mlld
/import templates from "./agents" as @agents(msg)
/var @agentObj = {"agent": "alice"}

/show @agents[@agentObj.agent](@msg)
```

**Expected:** Resolves `@agentObj.agent` to `"alice"`, then invokes `@agents["alice"](@msg)`

**Actual:** Outputs literal text without executing:
```
[@agentObj.agent](@msg)
```

**Works:**
```mlld
/var @name = "alice"
/show @agents[@name](@msg)  # ✓ works
```

**Doesn't work:**
```mlld
/show @agents[@obj.field](@msg)  # ✗ literal output
```

The bracket expression isn't being evaluated before indexing into the template collection.

Reported by @partydev.1 in chat - blocking their orchestration use case.


