---
id: mlld-s4qi
status: closed
deps: []
links: []
created: 2025-12-18T19:31:09.263332-08:00
type: bug
priority: 0
---
# Directory imports treat .mld files without / prefixes as templates instead of executing them

When importing .mld files (via directory import or direct import), files without / prefixes are treated as templates instead of being executed.

Reproduction:
File agents/party/index.mld contains:
var @meta = { id: "party" }
export { @meta }

When imported, shows as template instead of executing the code.

The grammar supports auto-directive mode (directives without /) in .mld files, but the import system is treating them as templates.

Root cause: Need to check ModuleContentProcessor to see why .mld files aren't being parsed/executed when they lack / prefixes.


