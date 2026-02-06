---
id: mlld-w7vz
status: open
deps: []
links: []
created: 2026-01-11T13:47:07.073391-08:00
type: task
priority: 1
tags: [size-l, complexity-m, risk-l, impl-none, needs-human-design]
updated: 2026-01-30T22:45:01Z
---
# Restructure tests/cases to align with howto topics

## Summary

Restructure `tests/cases/` to mirror `docs/src/atoms/` and howto topics structure. This creates a single source of truth where documentation and tests are aligned.

## Current Structure

```
tests/cases/
├── feat/          # Feature tests (arbitrary organization)
├── invalid/       # Syntax error tests
├── exceptions/    # Runtime error tests
├── warnings/      # Warning tests
└── docs/          # Doc-extracted tests
```

## Proposed Structure

```
tests/cases/
├── howto/                    # Feature tests aligned with howto
│   ├── syntax/
│   │   ├── variables-basics/
│   │   ├── templates-basics/
│   │   └── ...
│   ├── commands/
│   │   ├── run-basics/
│   │   └── ...
│   └── control-flow/
│       ├── when-simple/
│       └── ...
├── edge/                     # Cross-cutting integration tests
│   ├── invalid/              # Syntax errors (categorized)
│   ├── exceptions/           # Runtime errors (categorized)
│   └── warnings/             # Warnings (categorized)
└── docs/                     # Auto-extracted from documentation
```

## Benefits

1. Test discovery matches documentation discovery
2. Can auto-generate test cases from doc examples
3. Single source of truth for feature behavior
4. QA testing naturally maps to test locations

## Migration Notes

- Some tests span multiple features → move to edge/
- Invalid/exception tests should be categorized by howto area where possible
- Need to update fixture generator for new paths
- Keep backward compatibility during migration

## Related

- Triggered by QA testing infrastructure work
- Aligns with howto pattern (docs/dev/HOWTO-PATTERN.md)


