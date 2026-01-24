---
id: mlld-nco4
status: closed
deps: []
links: []
created: 2025-12-18T21:19:01.323563-08:00
type: bug
priority: 2
---
# Spurious DataValueEvaluator errors on directory import with nested property access in for-loops

When using directory imports with for-loops that access nested properties, DataValueEvaluator errors are logged to stderr even though the script executes correctly.

Reproduction:
```mlld
/import "@agents" as @agentRegistry
/for @agent in @agentRegistry => show @agent.meta.name
```

Output: Repeated "Error: DataValueEvaluator error:" to stderr despite correct execution.

**Fix attempt 1 (incomplete):** CollectionEvaluator treated typed objects without AST entries as opaque - reduced some errors but didn't eliminate them.

**Fix attempt 2 (current):** Updated DataValueEvaluator to skip logging for intentionally isolated errors during property walking. Errors that are caught and handled by callers no longer log globally.

Changes:
- interpreter/eval/data-values/DataValueEvaluator.ts
- interpreter/eval/data-value-evaluator.ts  
- interpreter/eval/data-values/CollectionEvaluator.ts

Impact: Low severity but creates noise that confuses users.

User report: @partydev via devrel in proto-4.1 testing


