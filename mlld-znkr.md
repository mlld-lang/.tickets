---
id: mlld-znkr
status: open
deps: []
links: []
created: 2026-01-11T23:59:34.31731-08:00
type: feature
priority: 2
---
# parallel() should accept variable for concurrency limit

## Summary
`for parallel(N)` requires a literal number, not a variable.

## Current
```mlld
var @parallelLimit = 20
>> This doesnt work:
var @results = for parallel(@parallelLimit) @t in @topics => @process(@t)
>> Must hardcode:
var @results = for parallel(20) @t in @topics => @process(@t)
```

## Desired
```mlld
var @parallelLimit = @p.parallel ? @p.parallel : 10
var @results = for parallel(@parallelLimit) @t in @topics => @process(@t)
```

This would allow configurable parallelism via payload/config.


