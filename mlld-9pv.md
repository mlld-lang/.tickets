---
id: mlld-9pv
status: closed
deps: []
links: []
created: 2025-12-10T02:50:58.903344-08:00
type: feature
priority: 1
---
# Epic: Block error reparse helper

Add reparse-on-failure helper to surface inner-line parse errors for all [..] block constructs (exe blocks, for blocks, when blocks/match, guard blocks, foreach if applicable). Deliver: grammar-core helper + per-block wiring to reparse inner text with correct offsets, plus fixtures for improved diagnostics.

## Notes

Block reparse helper landed across exe/for/when/guard, docs updated, changelog noted, targeted test added; grammar build passes.


