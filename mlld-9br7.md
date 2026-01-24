---
id: mlld-9br7
status: open
deps: []
links: []
created: 2026-01-11T23:59:34.564141-08:00
type: feature
priority: 3
---
# replaceAll with object/map for bulk string substitution

## Summary
When doing multiple replacements, an object-based replaceAll would be cleaner.

## Current
```mlld
let @s1 = @tmpl.replace("@topic", @topic)
let @s2 = @s1.replace("@experiment", @exp)
let @s3 = @s2.replace("@status", @status)
=> @s3
```

## Desired
```mlld
=> @tmpl.replaceAll({
  "@topic": @topic,
  "@experiment": @exp,
  "@status": @status
})
```

Common pattern in prompt template building.


