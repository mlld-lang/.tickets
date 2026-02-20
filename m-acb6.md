---
id: m-acb6
status: closed
deps: []
created: 2026-02-19T04:59:07Z
type: feature
priority: 0
assignee: Adam Avenir
tags: [lsp, embedded-languages, wasm]
updated: 2026-02-19T22:47:33Z
---
# LSP: Enable tree-sitter WASM for Python and Bash embedded blocks

## Summary

The LSP's embedded language service (`services/lsp/embedded/EmbeddedLanguageService.ts`) only loads tree-sitter WASM for JavaScript. Python and Bash code blocks fall back to plain string tokenization with @variable interpolation only. All the token mappings, alias handling, and LanguageBlockHelper wiring are already in place — the missing piece is the WASM files themselves.

## Problem

Our current `tree-sitter-python@0.20.4` and `tree-sitter-bash@0.20.5` packages don't ship pre-built WASM files. The `tree-sitter-javascript@0.20.4` package does, which is why only JS works today.

## Approach

**Upgrade tree-sitter language packages to 0.23.x** — these versions ship pre-built `.wasm` files:
- `tree-sitter-python@0.23.6` (458KB WASM)
- `tree-sitter-bash@0.23.3` (1.4MB WASM)  
- `tree-sitter-javascript@0.23.1` (359KB WASM) — upgrade for consistency

### Compatibility check required
- We use `web-tree-sitter@0.20.8`. The 0.23.x WASM files may use `dylink.0` section format while 0.20.8 expects `dylink`. Test this first.
- If incompatible, bump `web-tree-sitter` to 0.23.x or 0.25.x (do NOT go to 0.26.x — that has breaking WASM format changes going the other direction).

## Changes

1. **package.json** — Upgrade `tree-sitter-python`, `tree-sitter-bash`, `tree-sitter-javascript` to 0.23.x; bump `web-tree-sitter` if needed
2. **scripts/copy-wasm-files.js:18** — Change `const languages = ['javascript']` to `['javascript', 'python', 'bash']`
3. **services/lsp/embedded/EmbeddedLanguageService.ts:210-212** — Uncomment `loadLanguage('python')` and `loadLanguage('bash')`
4. **services/lsp/utils/LanguageBlockHelper.ts:185** — Remove the TODO comment about enabling tree-sitter for sh/py
5. **services/lsp/embedded-language-tokens.test.ts** — Update Bash and Python tests: they currently assert "WASM not present" behavior (brace-only tokens); update to expect real syntax tokens (keywords, variables, strings, etc.)
6. **docs/dev/LANGUAGE-SERVER.md** — Note embedded language WASM support for all three languages

## Acceptance

- `npm run build:wasm` copies all three `.wasm` files to `dist/wasm/`
- `embeddedLanguageService.isLanguageSupported('python')` returns true
- `embeddedLanguageService.isLanguageSupported('bash')` returns true
- Python code blocks in the LSP get keyword/variable/string/number tokens (not just string fallback)
- Bash code blocks in the LSP get keyword/function/variable/string tokens (not just string fallback)
- `npm test services/lsp` passes
- CI build succeeds (graceful fallback if WASM unavailable in CI)


**2026-02-19 22:42 UTC:** Implemented: upgraded tree-sitter-{javascript,python,bash} to 0.23.x; enabled WASM copy/load for js+python+bash; removed stale sh/py TODO fallback note; updated embedded-language token tests for real Bash/Python parsing; updated docs/dev/LANGUAGE-SERVER.md. Validation: npm run build:wasm copies all 3 wasm files; npx vitest run --config vitest.config.tokens.mts services/lsp/embedded-language-tokens.test.ts passes. Full npm test services/lsp currently fails on unrelated baseline issues (tmp/exact-check.test.ts has no suite; MCP socket EPERM in cli/mcp/MCPOrchestrator.test.ts under sandbox).
