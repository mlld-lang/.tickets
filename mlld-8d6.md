---
id: mlld-8d6
status: closed
deps: [mlld-cw9]
links: []
created: 2025-12-09T22:16:00.189932-08:00
type: task
priority: 1
---
# Phase 1.4: Types - Define AST node types for blocks and while

## Summary

Add TypeScript type definitions for the new AST nodes introduced by the grammar changes.

## ⚠️ CRITICAL: Types Must Match Grammar Output

The type definitions below are **starting points**. After grammar is implemented:

1. Run `npm run ast -- '<syntax>'` on each construct
2. Compare actual grammar output to type definitions
3. Adjust types to match grammar output exactly
4. Pay special attention to:
   - `values` vs direct properties
   - `with`/pipeline metadata flow
   - Array wrapping conventions

**DO NOT assume these types are correct** - verify against actual grammar output!

## Prereq

Complete abstraction discovery (mlld-cw9) to understand existing type hierarchy.

## Files to Modify

- `core/types/index.ts` - Add ExeBlockNode
- `core/types/for.ts` - Extend ForDirective with actionType
- `core/types/while.ts` (new) - WhilePipelineStage, WhileDirective types
- `core/types/control.ts` (new or existing) - DoneLiteral, ContinueLiteral types

## Type Definitions (Starting Points)

### ExeBlockNode

```typescript
export interface ExeBlockNode extends BaseMlldNode {
  type: 'ExeBlock';
  subtype: 'exeBlock';
  values: {
    statements: BaseMlldNode[];
    return?: BaseMlldNode[];
  };
  raw: {
    statements: string;
    hasReturn: boolean;
  };
  meta: {
    statementCount: number;
    hasReturn: boolean;
  };
}

export function isExeBlockNode(node: BaseMlldNode): node is ExeBlockNode {
  return node.type === 'ExeBlock';
}
```

### ForDirective Extension

```typescript
export interface ForDirective extends DirectiveNode {
  kind: 'for';
  subtype: 'for';
  values: {
    variable: VariableReferenceNode[];
    source: BaseMlldNode[];
    action: BaseMlldNode[];
    forOptions?: {
      parallel?: boolean;
      cap?: number;
      rateMs?: number;
    };
  };
  meta: {
    hasVariables: true;
    actionType: 'single' | 'block';  // NEW FIELD
  };
}
```

### While Types

```typescript
export interface WhilePipelineStage extends BaseMlldNode {
  type: 'WhileStage';
  values: {
    cap: number;
    rate: number | null;
    processor: ExeReferenceNode;
  };
  meta: {
    hasCap: true;
    hasRate: boolean;
  };
}

export interface DoneLiteral extends BaseMlldNode {
  type: 'ControlLiteral';
  subtype: 'done';
  values: {
    value: BaseMlldNode[];
  };
  meta: {
    hasValue: boolean;
  };
}

export interface ContinueLiteral extends BaseMlldNode {
  type: 'ControlLiteral';
  subtype: 'continue';
  values: {
    value: BaseMlldNode[];
  };
  meta: {
    hasValue: boolean;
  };
}

export function isDoneLiteral(node: BaseMlldNode): node is DoneLiteral {
  return node.type === 'ControlLiteral' && node.subtype === 'done';
}

export function isContinueLiteral(node: BaseMlldNode): node is ContinueLiteral {
  return node.type === 'ControlLiteral' && node.subtype === 'continue';
}
```

## Validation

- [ ] All new types extend BaseMlldNode correctly
- [ ] Type guards are implemented
- [ ] **Types verified against actual grammar output**
- [ ] No circular dependencies introduced
- [ ] Pipeline/with metadata flows correctly

## Notes

Added types for exe blocks, while directive/stage, and control literals. Updated pipeline stage union, ForDirective meta actionType union, LiteralNode to allow done/continue payload arrays, and added control type guards. Build passes.


