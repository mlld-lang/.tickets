---
id: m-a0ea
status: closed
deps: []
created: 2026-02-17T04:59:08Z
type: chore
priority: 0
assignee: Adam
updated: 2026-02-17T18:37:58Z
---
# Replace any types in LSP visitor layer with proper interfaces

200+ instances of `any` type in LSP services, especially: private mainVisitor: any, node: any, ast: any[] across CommandVisitor.ts, DirectiveVisitor.ts, VariableVisitor.ts, language-server-impl.ts, and others. Introduce proper interfaces (e.g. ILspNode, ICommandVisitor) for type safety.


**2026-02-17 18:37 UTC:** Completed in commit b6fa6b587: removed explicit any usage across services/lsp/visitors, introduced shared LspAstNode typing, and added typing-regression test.
