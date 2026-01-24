---
id: mlld-rgu6
status: closed
deps: []
links: []
created: 2025-12-21T17:21:49.765429-08:00
type: bug
priority: 1
---
# Exe functions calling sibling exes break when imported through intermediate module

When module A exports @outerHelper which calls @innerHelper, and module B imports and re-exports @outerHelper, calling @outerHelper from module C fails with 'Command not found: innerHelper'. 

Root cause: VariableImporter.ts line 334 unconditionally deletes capturedModuleEnv from executables when serializing module env (skipModuleEnvSerialization=true). This strips the scope chain from imported executables.

Repro:
- base-module.mld: defines @innerHelper and @outerHelper (calls @innerHelper)
- middle-module.mld: imports @outerHelper, exports @wrapper (calls @outerHelper)
- importer.mld: imports @wrapper, calls it - FAILS


