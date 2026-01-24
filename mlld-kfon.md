---
id: mlld-kfon
status: closed
deps: []
links: []
created: 2026-01-12T16:56:47.339221-08:00
type: task
priority: 0
---
# StructuredValue Symbol lost when nested in object literals

When a StructuredValue is placed inside an object literal like { fix: @loadedJson }, the STRUCTURED_VALUE_SYMBOL is lost somewhere in the evaluation chain. This causes isStructuredValue() to return false for nested values, breaking field access until mlld-5qqk's workaround detects the duck-typed structure.

Root cause investigation needed:
1. Trace object literal evaluation in ast-evaluator.ts:evaluateToRuntime
2. Check if Object.assign/spread operations strip Symbols
3. Verify variable creation preserves Symbols through for-utils.ts:ensureVariable
4. Consider if VariableImporter.createVariableFromValue handles nested SVs correctly

The proper fix per DATA.md should preserve Variable wrappers through field access chains using ResolutionContext.FieldAccess, rather than relying on duck-typing fallbacks.

Related: mlld-5qqk (workaround fix), docs/dev/DATA.md lines 257-275


