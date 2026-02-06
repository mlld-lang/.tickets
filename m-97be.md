---
id: m-97be
status: open
deps: []
links: []
created: 2026-01-26T19:00:00Z
type: task
priority: 2
tags: [docs, howto, discoverability, size-m, complexity-s, risk-xs, impl-partial]
---
# Add cross-references between related howto topics

## Problem

QA testing (tier 2, 38 topics) revealed that the #1 cause of false-positive bug reports was agents (and by extension, users) not discovering related howto topics. The howto system is modular by design, but users who land on a specific topic (e.g., `mlld howto exe-blocks`) don't know about related topics they need (e.g., `show`, `log`, `intro`).

Specific patterns observed:
- `exe-blocks` doesn't mention `show`/`log` for producing output
- `modules-exporting` doesn't cross-reference `modules-importing-local`
- `when-blocks` doesn't link to `when-first` or `when-bare`
- `security-*` topics don't link to each other
- `templates-loops` doesn't link to `templates-basics`
- Feature-specific docs don't reference `intro` for fundamentals

## Proposed solution

Add a "See also" or "Related topics" section to howto topics that have natural connections. This could be:

1. A `related` field in the howto frontmatter that gets rendered automatically
2. A manual "See also" section at the bottom of each howto
3. Both - frontmatter for machine use, rendered section for human use

## Evidence

From QA tier 2 self-reviews:
- modules-exporting: 67% false positive rate, all resolved by finding info in adjacent topics
- exe-blocks: declared "BLOCKED" because agent didn't know about `show` directive
- when-blocks: confused `when` vs `when first` because topics are separate
- security-before-guards: most issues were from not checking `security-guards-basics` first
- Nearly every topic's self-review recommended "add cross-references"

## Impact

This is the single highest-leverage documentation improvement identified by QA testing. Would likely reduce first-time user confusion significantly and reduce QA false positive rates in future runs.
