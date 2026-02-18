---
id: m-b8c7
status: open
deps: [m-8f72]
created: 2026-02-18T17:09:31Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-18T17:09:39Z
---
# Phase 1: Hook grammar + AST/type wiring

Implement Phase 1 (grammar/AST/type wiring) from `plan-hooks-checkpoints-resume.md`.

Scope:
- Add `/hook` directive grammar (modern timing-required form only).
- Support filters:
  - function: `@fn` and `@fn("prefix")`
  - operation: `op:<type>` including `for`, `for:iteration`, `for:batch`, `loop`, `import`
  - data label
- Update grammar root + reserved directives + grammar deps directive kinds.
- Update AST/types exports (`DirectiveKind`, `DirectiveSubtype`, hook node types).
- Update syntax surfaces:
  - semantic tokens / LSP directive handling
  - syntax generator keyword/filter patterns

## Design

Primary files:
- `grammar/directives/*` (new hook directive file)
- `grammar/mlld.peggy`
- `grammar/base/tokens.peggy`
- `grammar/deps/grammar-core.ts`
- `core/types/primitives.ts`, `core/types/index.ts`
- `services/lsp/visitors/DirectiveVisitor.ts`
- `grammar/syntax-generator/build-syntax.js`

## Acceptance Criteria

Exit criteria:
- Added test coverage:
  - `grammar/tests/hook.test.ts` (valid/invalid/filter forms)
  - any required type-alignment grammar tests updated
- All tests pass:
  - `npm run build:grammar`
  - `npm test grammar/tests/`
  - `npm run build`
  - `npm test`
- Docs updates complete:
  - `docs/dev/GRAMMAR.md`
  - `docs/user/reference.md`
- Commit made after green tests:
  - `feat(grammar): add hook directive syntax, AST types, and parser integrations`

