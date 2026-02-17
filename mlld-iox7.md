---
id: mlld-iox7
status: closed
deps: []
links: []
created: 2026-01-11T23:59:34.111087-08:00
type: feature
priority: 1
tags: [size-m, complexity-l, risk-l, impl-partial]
updated: 2026-02-13T16:19:35Z
---
# Method chaining across multiple lines

## Summary
Method chaining requires intermediate variables, making code verbose.

## Current
```mlld
exe @buildPrompt(tmpl, failure) = [
  let @s1 = @tmpl.replace("@topic", @failure.topic)
  let @s2 = @s1.replace("@experiment", @failure.experiment)
  let @s3 = @s2.replace("@resultsPath", @failure.resultsPath)
  => @s3
]
```

## Desired
```mlld
exe @buildPrompt(tmpl, failure) = @tmpl
  .replace("@topic", @failure.topic)
  .replace("@experiment", @failure.experiment)
  .replace("@resultsPath", @failure.resultsPath)
```

Multiline method chaining would significantly clean up data transformation pipelines.



**2026-02-12 20:55 UTC:** Downgraded to P1. Has workarounds (intermediate variables), grammar change needs careful design around newline-as-statement-terminator.
