---
id: m-fa56
status: open
deps: []
created: 2026-02-23T16:40:10Z
type: task
priority: 1
assignee: Adam Avenir
tags: [design, docs, semantics]
updated: 2026-02-23T16:40:14Z
---
# Design: Align @var?, ??, and ternary falsy/nullish semantics

## Context

mlld has three default-value/conditional mechanisms with different falsy semantics:

- `@var?` — falsy-based: treats null, "", 0, false, [], {}, "0", "false", "NaN" as falsy
- `??` (nullish coalescing) — nullish-only: only null/undefined trigger the default
- Ternary — falsy-based (same as @var?)

The string values "0", "false", and "NaN" being falsy in @var? is particularly surprising. This information is scattered across gotchas, variables-truthiness, variables-conditional, and escaping-defaults with no cross-references.

## Questions to resolve

1. Should "0", "false", and "NaN" strings really be falsy? This is aggressive — most languages only treat empty string as falsy. A user storing "false" as a string config value would have it silently treated as no-value.
2. Is the gap between @var? (falsy-based) and ?? (nullish-only) intentional and useful, or confusing? Having both is fine if the docs make the distinction clear, but currently they don't.
3. Should the truthiness table live in one canonical location with cross-references, rather than being duplicated (incompletely) across 5 doc pages?

## Current state of docs

- gotchas.md has a table but it's INCOMPLETE — missing undefined, "false", "0", NaN
- variables-truthiness.md has one correct inline line but no table
- reference.md, content-and-data.md, and llms-syntax.txt have correct inline lists
- No doc page has a comparison showing @var? vs ?? vs ternary side-by-side

## Action needed

1. Decide on the design questions above
2. Fix the gotchas table to include ALL falsy values
3. Create a single canonical truthiness reference (probably variables-truthiness.md) with a complete table
4. Add the @var? vs ?? vs ternary comparison
5. Cross-reference from all other locations

