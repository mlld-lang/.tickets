---
id: mlld-0vps
status: closed
deps: []
links: []
created: 2026-01-03T02:48:21.138618-08:00
type: bug
priority: 2
---
# Parallel for: StructuredValue .mx metadata lost when passed to exe functions

## Bug

When a StructuredValue is passed to an exe function inside a `for parallel` loop, the `.mx` metadata is not accessible.

## Reproduction

```mlld
exe @getFilename(f) = [
  let @rel = @f.mx.relative  >> FAILS: Field 'relative' not found
  => @rel
]
var @files = <src/*.ts>
var @results = for parallel(2) @f in @files => @getFilename(@f)
```

## Expected
`@f.mx.relative` should work inside the exe block, returning the relative path.

## Actual
Error: `Field 'relative' not found in object`

The error shows the value has `relative` as a top-level property (not under `.mx`), suggesting StructuredValue is being flattened when crossing the parallel for → exe boundary.

## Workaround
Extract metadata before the parallel loop:
```mlld
var @fileInfo = for @f in @files => { name: @f.relative, content: @f }
var @results = for parallel(2) @info in @fileInfo => @process(@info)
```

## Principles Violated (per docs/dev/DATA.md)
- 'Everything at runtime is StructuredValue'
- 'Runtime metadata flows via .mx'

## Investigation Notes
- `exec-invocation.ts:1495-1500` correctly preserves StructuredValue when detected
- Issue likely in `for.ts` parallel execution path - how values are passed to child environments
- The StructuredValue metadata gets flattened to top level instead of staying under `.mx`
- Regular (non-parallel) for loops work correctly
- Inline expressions in parallel for work: `for parallel(2) @f in @files => @f.relative` ✓
- Only exe function calls in parallel for blocks are affected


