---
id: mlld-sus
status: closed
deps: []
links: []
created: 2025-12-11T11:13:41.312532-08:00
type: bug
priority: 1
---
# let statements should support all /var RHS forms (cmd, sh, js, template, etc)

**Issue:** `let` statements execute commands but variable interpolation broken for sh/js forms.

**Working (executes AND interpolates):**
```mlld
let @x = cmd { echo "@item" }  # ✅ interpolates @item correctly
```

**Broken (executes but NO interpolation):**
```mlld
let @x = sh { echo "@item" }   # ❌ outputs literal "@item"
let @x = js { return "@item" } # ❌ outputs literal "@ITEM"
```

**Test results:**
```mlld
/for @item in ["alice"] [
  let @a = cmd { echo "@item" }  # "alice" ✅
  let @b = sh { echo "@item" }   # "@item" ❌
  let @c = js { return "@item" } # "@ITEM" ❌
]
```

**Impact:** Limits @partydev.1 to `cmd` form only. Most use cases work with `cmd` for claude calls, but sh/js interpolation needed for complex orchestration.

**Root cause:** sh/js code execution in let context missing interpolation step that cmd has.


