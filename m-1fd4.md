---
id: m-1fd4
status: closed
deps: []
created: 2026-01-30T11:44:23Z
type: bug
priority: 1
assignee: Adam
tags: [interpreter, when, control-flow, llm-expectations, needs-human-design]
updated: 2026-01-30T22:44:19Z
---
# when block with => does not return early from exe function

Using `when @condition => [statements; => value]` inside an exe function does NOT return early. The `=> value` is evaluated but execution continues past the when block.

## Reproduction

```mlld
exe @test(condition) = [
  when @condition => [
    show `condition true`
    => "early-return"
  ]
  show `this still runs`  >> Always executes even when condition is true
  => "default"
]

show `Result: @test(true)`  >> outputs: "default" not "early-return"
```

## Expected

`=> "early-return"` inside a when block should return from the enclosing exe function.

## Workaround

Use `when first` which does return correctly:

```mlld
exe @test(condition) = when first [
  @condition => "early-return"
  * => "default"
]
```


**2026-01-30 15:17 UTC:** Enrich reviewed: 2026-01-30T15:14:56.295Z

**2026-01-30 16:27 UTC:** Reverted commit 205ade62. The 'fix' forced all when blocks to behave like 'when first' inside exe functions, breaking the intentional distinction: when [...] fires ALL matching conditions (for side effects), when first [...] fires only the first match (like switch). The original behavior is BY DESIGN. However, LLMs consistently expect => inside when blocks to return early (like other languages). This suggests the when semantics may need revisiting - especially now that blocks exist for multi-statement actions. Needs human design decision on whether/how to evolve when semantics.
