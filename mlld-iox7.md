---
id: mlld-iox7
status: open
deps: []
links: []
created: 2026-01-11T23:59:34.111087-08:00
type: feature
priority: 2
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


