---
id: mlld-b4f
status: closed
deps: [mlld-0gd]
links: []
created: 2025-12-10T11:26:55.803735-08:00
type: task
priority: 1
---
# Phase 1.7: Verify AST/Types alignment with design principles

Comprehensive verification that all new grammar changes (exe blocks, for blocks, while loops, done/continue literals) produce ASTs that align with existing mlld design principles and type system.

## Context

Phase 1 grammar work is "complete" but we need thorough verification that:
1. AST structure follows mlld conventions (docs/dev/AST.md, docs/dev/GRAMMAR.md)
2. Types align with existing enums and interfaces (core/types/)
3. StructuredValue patterns are correct (docs/dev/DATA.md)
4. No regressions or inconsistencies introduced

## Required Reading

- **docs/dev/AST.md** - Context-aware AST design principles (may be slightly outdated but principles are sound)
- **docs/dev/GRAMMAR.md** - Grammar architecture and abstraction patterns
- **docs/dev/DATA.md** - StructuredValue system (.text, .data, .ctx)

## Verification Checklist

### 1. AST Structure Verification

For each new construct, verify AST follows mlld conventions:

**Exe Blocks** (`/exe @f() = [...]`):
- [ ] `directive.kind === 'exe'`
- [ ] `directive.subtype === 'exeBlock'`
- [ ] `values.statements` is array of statement nodes
- [ ] `values.return` has proper ExeReturn structure
- [ ] `meta.statementCount`, `meta.hasReturn` set correctly
- [ ] Location tracking includes block boundaries

**For Blocks** (`for @x in @xs [...]`):
- [ ] `directive.kind === 'for'`
- [ ] `meta.actionType === 'block'` or `'single'`
- [ ] `values.action` is array when actionType='block'
- [ ] `meta.blockMeta.statementCount` set when block
- [ ] Arrow form still parses (`for @x in @xs => [...]`)

**While Loops** (`/while(100) @processor`):
- [ ] Directive form: `kind === 'while'`, `subtype === 'whileDirective'`
- [ ] Pipeline form: node type for pipeline stage
- [ ] `values.cap` (number), `values.processor` (VariableReference)
- [ ] Optional `values.rateMs` for pacing
- [ ] Location tracking correct

**Done/Continue Literals**:
- [ ] `type === 'Literal'`
- [ ] `valueType === 'done'` or `valueType === 'continue'`
- [ ] `value` is array (may be empty or contain expression)
- [ ] Works in return position, when expression actions
- [ ] Proper location tracking for error messages

### 2. Type System Verification

Check alignment with existing types:

**DirectiveKind enum** (core/types/):
- [ ] 'exe' already exists (verify exeBlock subtype)
- [ ] 'for' already exists (verify block metadata)
- [ ] 'while' added if new directive kind needed
- [ ] No duplicate or conflicting enum values

**Literal valueType** (core/types/):
- [ ] 'done' added to valueType union
- [ ] 'continue' added to valueType union
- [ ] Literal node interface supports array value
- [ ] No conflicts with existing literal types

**Meta flags**:
- [ ] `actionType: 'block' | 'single'` on ForDirective
- [ ] `statementCount`, `hasReturn` on ExeBlock
- [ ] `blockMeta` for nested block info
- [ ] No namespace collisions with existing meta

### 3. StructuredValue Patterns

Verify alignment with DATA.md:

**Let assignments**:
- [ ] Store StructuredValue wrappers (not raw values)
- [ ] Preserve .text, .data, .ctx through assignments
- [ ] Augmented assignment (+=) works with StructuredValue

**Block returns**:
- [ ] Exe block return preserves StructuredValue wrapper
- [ ] Done/continue values preserve wrappers
- [ ] No unwrapping until boundaries (asData/asText)

**Iteration values**:
- [ ] For loop iterator values wrapped
- [ ] While loop state values wrapped
- [ ] Context (@ctx) values properly structured

### 4. Regression Testing

Run existing tests to ensure no breaks:

```bash
npm run build:grammar
npm test grammar/
npm run ast -- '/exe @f() = when [* => "test"]'  # Existing exe..when
npm run ast -- '/for @x in @xs => show @x'        # Existing for single
npm run ast -- '/var @x = 1'                       # Basic directive
```

Expected: All existing syntax still parses correctly.

### 5. AST Inspection

Use ast tool to verify structure:

```bash
# Exe block
npm run ast -- '/exe @add(x, y) = [let @sum = @x + @y; => @sum]'

# For block  
npm run ast -- '/for @item in @items [show @item; let @count += 1]'

# While directive
npm run ast -- '/while(100, 1s) @processor'

# Done/continue
npm run ast -- '/exe @f() = when [@x > 5 => done @x]'
npm run ast -- '/exe @f() = when [* => continue (@x - 1)]'
```

Inspect output for:
- Correct node types
- Proper nesting structure  
- Meta flags set appropriately
- No unexpected fields
- Location data present

### 6. Type Alignment Check

```bash
# Type check should pass
npm run build

# Look for type errors in:
# - core/types/directive.ts
# - core/types/literal.ts  
# - interpreter/eval/exe.ts
# - interpreter/eval/for.ts
# - interpreter/eval/while.ts
```

### 7. Documentation Updates

If AST.md or GRAMMAR.md are outdated:
- [ ] Update AST.md with new node types/patterns
- [ ] Update GRAMMAR.md if new abstractions added
- [ ] Document any new meta flags
- [ ] Add examples for new constructs

## Common Issues to Check

1. **Location offsets**: Block reparsing must have correct offsets
2. **Meta consistency**: Same construct different contexts should have consistent meta
3. **Array wrapping**: All directive values should be node arrays (except primitives in var)
4. **Type discriminators**: Objects/arrays use type field, not structure alone
5. **Context flags**: isDataValue, isRHSRef set correctly for embedded directives

## Acceptance Criteria

- [ ] All new constructs parse to valid AST
- [ ] AST structure follows mlld conventions (AST.md)
- [ ] Types compile without errors
- [ ] No regressions in existing grammar tests
- [ ] Meta flags consistent with patterns
- [ ] StructuredValue patterns correct
- [ ] Location tracking works for error messages

## Deliverable

Create `tmp/phase1-ast-verification.md` documenting:
1. AST samples for each new construct (pretty-printed JSON)
2. Type alignment verification (enum values, interfaces)
3. Any issues found and fixes applied
4. Confirmation that all principles followed

## Time Estimate

60-90 minutes (thorough inspection, not quick pass)

## Notes

Phase 1 AST verification complete. All constructs verified and aligned with design principles. See tmp/phase1-ast-verification.md for full report. Key findings: All tests pass (2391/2391), AST structures correct, types align. Minor action: add 'while' to DirectiveKind enum. Ready for Phase 2.


