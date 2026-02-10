---
id: m-8be2
status: open
deps: [m-796b]
links: []
created: 2026-02-09T06:24:52Z
type: task
priority: 1
assignee: Adam Avenir
parent: m-d11b
tags: [refactor, var, phase-3, content-loader]
---
# Refactor Program: Modularize interpreter/eval/var.ts - Phase 3: Extract content-source evaluation paths

Objective:
Extract content-source evaluation paths (file reference/path/section/load-content).

Instructions for the implementing agent:
- Move content-loading and section/path evaluation branches into focused module(s) (example target: `interpreter/eval/var/rhs-content.ts`).
- Preserve `readFileWithPolicy` usage and source-location propagation for all file reads.
- Preserve section extraction behavior, including llmxml path and fallback extraction behavior.
- Preserve `withClause.asSection` transform behavior for both glob and single-file load-content sources.
- Add focused tests for path, section, load-content, and asSection transform behavior across single-file and glob inputs.

## Acceptance Criteria

1. Content-source RHS branches are extracted into dedicated module(s) with clear inputs/outputs.
2. Policy, section extraction, and transform semantics remain unchanged.
3. Added tests cover path/section/load-content edge cases, including glob transforms.
4. Exit criteria: all tests pass, with output attached:
   npm run build && npm test && npm run test:tokens && npm run test:examples

