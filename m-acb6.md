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

**Upgrade all tree-sitter packages to latest:**
- `web-tree-sitter` 0.20.8 → **0.25.10** (latest 0.25.x — accepts both `dylink` and `dylink.0` WASM formats; avoid 0.26.x which drops legacy `dylink` support)
- `tree-sitter-javascript` 0.20.4 → **0.25.0** (latest; ships pre-built WASM, 412KB)
- `tree-sitter-python` 0.20.4 → **0.25.0** (latest; ships pre-built WASM, 458KB)
- `tree-sitter-bash` 0.20.5 → **0.25.1** (latest; ships pre-built WASM, 1.4MB)

### WASM format compatibility note
`web-tree-sitter@0.26.x` switched to requiring `dylink.0` format only and broke backward compat with older WASM files. Staying on 0.25.x avoids this — it accepts both formats. The 0.25.x language packages should pair cleanly with `web-tree-sitter@0.25.10`. If any API breaking changes surface between 0.20→0.25, address in the same PR.

## Changes

1. **package.json** — Upgrade `web-tree-sitter` to ^0.25.10, `tree-sitter-javascript` to ^0.25.0, `tree-sitter-python` to ^0.25.0, `tree-sitter-bash` to ^0.25.1
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
