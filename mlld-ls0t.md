---
id: mlld-ls0t
status: closed
deps: []
links: []
created: 2026-01-11T21:19:01.796637-08:00
type: task
priority: 0
---
# Add .mx.dir to file metadata for dirname access

Currently loaded files have path metadata but no directory equivalents.

## Current metadata
- `.mx.filename` - just the name (`results.json`)
- `.mx.relative` - relative path with filename (`./qa/topic/exp/results.json`)
- `.mx.absolute` - absolute path with filename (`/Users/.../results.json`)

## Proposed additions
- `.mx.dirname` - immediate parent directory name (`01-L-basic-backslash-escape`)
- `.mx.relativeDir` - relative path to directory (`./qa/escaping-basics/01-L-basic-backslash-escape`)
- `.mx.absoluteDir` - absolute path to directory (`/Users/.../qa/escaping-basics/01-L-basic-backslash-escape`)

## Use case
Comparing directories when processing glob results:
```mlld
var @results = <qa/**/results.json>
var @analyzed = <qa/**/proposed-fixes.json>
var @analyzedDirs = for @f in @analyzed => @f.mx.absoluteDir

var @needsAnalysis = for @r in @results 
  when !@analyzedDirs.includes(@r.mx.absoluteDir) => @r
```


