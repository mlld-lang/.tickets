---
id: mlld-6fq
status: closed
deps: [mlld-76c]
links: []
created: 2025-12-13T14:24:43.061079-08:00
type: task
priority: 1
tags: [size-m, complexity-m, risk-m, impl-done]
updated: 2026-01-30T06:46:53Z
---
# Audit all visitors for column-based position calculations

Many visitors use column arithmetic which is fragile and breaks in specific cases:

Unsafe pattern:
  const charPos = node.location.start.column + keyword.length + 1;

Problems:
- Breaks with datatype labels (exe datatype @func)
- Breaks with multi-byte UTF-8 characters  
- Breaks with tabs vs spaces
- Breaks with text between directive and token

Safe pattern:
  const source = this.document.getText();
  const atIndex = source.indexOf('@' + identifier, startOffset);
  const atPos = this.document.positionAt(startOffset + atIndex);

Files to audit:
- VariableVisitor.ts lines 129-131 (has WORKAROUND comment)
- CommandVisitor.ts line 431-439 (simple function calls)
- ExpressionVisitor.ts (keyword positioning)
- DirectiveVisitor.ts (mostly fixed, but check other methods)

Search commands:
  grep -n '\.start\.column.*[+-]' services/lsp/visitors/*.ts
  grep -n 'start\.column' services/lsp/visitors/*.ts | grep -v 'positionAt'

Any column + number or column - number is suspect unless immediately passed to positionAt().


