---
id: mlld-ccf
status: closed
deps: []
links: []
created: 2025-12-14T11:49:26.697866-08:00
type: bug
priority: 1
---
# Guard condition highlighting wrong - should match when conditions

Guard blocks not tokenizing like exe when blocks. Two issues:

1. Guard block body (after =>) should tokenize exactly like exe when blocks:
   guard before label = when [ @condition => allow @action() ]
   The condition block should tokenize identically to:
   exe @func = when first [ @idx == -1 => 0 ]

2. 'when first' grouping: The 'when first' keywords should be grouped/highlighted together (both light teal italic).

Currently: Guard condition blocks have broken/inconsistent highlighting
Target: Match exe when block highlighting exactly

Files: DirectiveVisitor.ts visitGuardDirective()


