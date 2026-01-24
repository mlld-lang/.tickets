---
id: mlld-4cw
status: closed
deps: []
links: []
created: 2025-12-09T08:29:52.689432-08:00
type: task
priority: 1
---
# Docs: restructure llms.txt into modules

Restructure llms.txt into modular documentation with two complementary resources:

## Part 1: Reference Modules (docs/llm/)

Modular reference guide in strict mode (bare directives). Each module standalone and linkable:

- llms-overview.txt - What mlld is, mental model, two modes (strict vs markdown)
- llms-core-rules.txt - The 13 rules with strict-mode examples
- llms-syntax.txt - Variables, templates, file loading, methods
- llms-commands.txt - run/cmd/sh, exe, output, log, append
- llms-control-flow.txt - when, for, foreach, while, block syntax
- llms-modules.txt - Import/export, registry, local dev
- llms-patterns.txt - Common workflows
- llms-configuration.txt - SDK modes, resolvers, frontmatter
- llms-mistakes.txt - Common mistakes (updated for strict mode)
- llms-security.txt - Guards, labels
- llms-reference.txt - Quick reference tables

Entry points:
- llms.txt - Modular entry with TOC + links to modules
- llms-combined.txt - Generated concatenation for full-context injection

## Part 2: Cookbook (llms-cookbook.txt)

Annotated real-world examples showing feature composition. Each recipe ~30-80 lines, strict mode, heavily commented:

1. **LLM Library** - Clean utility module (when-first, cmd:@dir, pipelines)
2. **Gate Pattern** - Validation (block syntax, method chaining, structured returns)
3. **Agent Definition** - Config module (frontmatter @fm, template exe, exports)
4. **Router** - Decision logic (scoring, for-filtering, object spread)
5. **Orchestrator** - Parallel execution (parallel for blocks, imports)
6. **Codebase Audit** - Analysis workflow (AST selection, aggregation, output)

Based on working examples from party/proto-5.1 (anonymized/adapted).

## Build System

- llm/run/llmstxt.mld - Script to generate llms-combined.txt
- Version matches mlld release

## Key Changes from Current llms.txt

1. All examples converted to strict mode (bare directives)
2. Up-front explanation that markdown mode adds / prefix
3. Block syntax (`exe = [...]`, `for [...]`) properly documented
4. Advanced patterns: for-filtering, object spread, method chaining, @json.llm
5. Real-world composition examples in cookbook


