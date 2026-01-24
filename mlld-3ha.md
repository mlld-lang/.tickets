---
id: mlld-3ha
status: closed
deps: []
links: []
created: 2025-12-11T05:04:30.901599-08:00
type: feature
priority: 2
---
# Add error accumulation for parallel execution (for blocks + pipeline groups)

**Scope:** Error accumulation for parallel execution - both for loops and pipeline groups.

**1. Parallel for blocks**

**Current:** Block bodies blocked:
```mlld
✅ /for parallel @x in @xs => show @x
❌ /for parallel @x in @xs [ show @x; let @count += 1 ]
```

**Desired:**
```mlld
/for parallel(3) @agent in @respondingAgents [
  let @prompt = @agents[@agent.agent](@messageBody)
  let @response = cmd { claude -p "@prompt" }
  run { bdm post --as @agent.agent\.1 "@response" }
]
```

**2. Parallel pipeline groups**

**Current:** `|| @a || @b || @c` fails entire pipeline if one fails

**Desired:** Best-effort with error accumulation:
```mlld
/var @data = || @fetchA() || @fetchB() || @fetchC() | @combine

/exe @combine(results) = when [
  @ctx.errors.length == 0 => @merge(@results)
  * => @handlePartial(@results, @ctx.errors)  # 2/3 succeeded
]
```

**Use cases:**
- Multi-agent orchestration (invoke 5 agents, proceed if 3 respond)
- Best-of-N with fallback (try multiple LLMs, use what works)
- Multi-source aggregation (fetch 3 APIs, ok if 2/3 succeed)
- Validation pipeline (run multiple validators, collect all failures)

**Unified design:**

**Error handling:**
- Errors accumulate in `@ctx.errors` array instead of throwing
- Results array contains error markers for failed operations (index-aligned)
- Error marker: `{ index, key?, message, error, value? }`
  - `index`: position in input
  - `key`: for object iteration
  - `message`: human-readable
  - `error`: stringified detail
  - `value`: original input (for retry logic)
- `@ctx.errors` clears per parallel operation (no state leakage)
- Caller decides repair via `when @ctx.errors.length > 0`

**Isolation (parallel for only):**
- Block-scoped `let` only
- Error on writes to outer variables (race prevention)

**Ordering:**
- Directive form: streams unordered (fast)
- Expression form: preserves input order

**Sequential blocks keep current behavior:**
- Fail fast (no benefit to continuing)
- Outer writes allowed (no races)
- No error accumulation

**Pattern:**
```mlld
/exe @invokeAll(agents, msg) = [
  let @results = for parallel @a in @agents => @invoke(@a, @msg)
  => when [
    @ctx.errors.length == 0 => @results
    * => @repair(@results, @ctx.errors, @msg)  # AI-driven repair
  ]
]

/exe @aggregate(sources) = [
  let @data = || @fetch(@sources[0]) || @fetch(@sources[1]) || @fetch(@sources[2])
  => when [
    @ctx.errors.length == 0 => @data
    @data.length >= 2 => @data  # 2/3 is good enough
    * => retry "Need at least 2 sources"
  ]
]
```

Design from @partydev.1, implementation by gpt5.1.


