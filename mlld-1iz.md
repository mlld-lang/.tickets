---
id: mlld-1iz
status: closed
deps: [mlld-cw9]
links: []
created: 2025-12-09T22:26:53.223759-08:00
type: task
priority: 1
---
# Phase 1.6: Grammar Error Recovery Infrastructure

Implement sophisticated error recovery for block syntax to provide accurate error locations and helpful messages.

## Scope
Make Peggy parse errors inside [...] blocks point to the actual bad token instead of the opening bracket. Apply to exe blocks, for blocks, and while constructs.

## Implementation Details

### 1. Structured Block Rules (No Catch-Alls)
- Define explicit statement lists without generic fallbacks
- ExeBlockStatement enumerates: LetAssignment | AugmentedAssignment | EffectAction | WhenRHSVarAssignment | ReturnStatement
- ForBlockStatement uses ForSingleAction directly
- Reorder alternatives: specific statements before generic expression fallbacks

### 2. Inline Error Recovery Branches
- Unterminated block: lookahead for ], error at EOF if missing
- Return placement: branch for => not last, error 'Return must be last'
- Comma separator: match ',' in statement lists, error 'Use whitespace not commas'
- Missing = after let @id: recovery in let-assignment.peggy
- Multiple returns: detect second => in block

### 3. Reparse-on-Failure Helper
Add helper in grammar/deps/grammar-core.ts:
reparseInner(innerText, startRule, offset) - calls peg$parse with startRule, offsets location() by block start, rethrows via mlldError

### 4. Unclosed Delimiter Scanners
- Add bracket-aware scanner (like existing) honoring strings
- Use in ExeBlock/ForBlock rules
- Catch unclosed [ with helpful error

### 5. Baseline Fixtures
Create failing fixtures capturing current bad diagnostics for regression guard

## References
- Source: todo/plan-grammar-errors.md

## Notes

Added block grammar recovery: explicit ExeBlockAction surface (effects/assignments/special literals), ensured return must be last with mlldError, and block separators already reject commas. For/Exe blocks enumerate statements (no catch-alls) and reuse unclosed-[ check. Grammar build core passed.


