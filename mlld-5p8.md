---
id: mlld-5p8
status: closed
deps: []
links: []
created: 2025-12-09T08:00:13.723859-08:00
type: bug
priority: 1
---
# Grammar: extend VariableBoundary escape to all interpolation contexts

**Reported by:** partydev during SDK dogfooding

**Problem:**
When parsing `@agent.att` in paths/strings, the grammar greedily consumes `.att` as field access. Users need a way to say "stop here, `.att` is literal text (a file extension)."

**Example:**
```mlld
/exe @buildPrompt(msg) = template "../agents/@agent.att"
>> Currently: @agent with field access .att
>> Wanted: @agent variable + literal .att extension
```

**Current state:**
- `VariableBoundary` mechanism exists in `unified-variables.peggy`
- Syntax: `@var\` terminates variable, `@var\\` produces literal backslash
- BUT: only used in `UnifiedTemplateVariableReference` (template contexts)
- AND: boundary check happens AFTER greedy field consumption (wrong order)

**Fix needed:**
1. Reorder grammar so boundary check happens BEFORE field access consumption
2. Apply `VariableBoundary` pattern to ALL variable interpolation contexts:
   - Double-quoted strings
   - Path contexts (angle brackets)
   - Template paths in /exe
   - Any other interpolation context

**User syntax after fix:**
```mlld
/exe @buildPrompt(msg) = template "../agents/@agent\.att"
>> @agent\ = terminate variable here
>> .att = literal file extension
```

**Files to modify:**
- grammar/base/unified-variables.peggy - fix ordering, ensure boundary works
- Verify all variable-consuming patterns use the unified pattern

**Future enhancement:**
Consider adding `@{var}` braced syntax as a more intuitive alternative (separate issue).


